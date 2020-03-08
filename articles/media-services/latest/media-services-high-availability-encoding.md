---
title: Codificação de alta disponibilidade dos serviços de mídia do Azure
description: Saiba como fazer failover para uma conta de serviços de mídia secundária se ocorrer uma interrupção ou falha de um datacenter regional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: f5b02376111a3deba33cd5688330018bd7c370d8
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899211"
---
# <a name="media-services-high-availability-encoding"></a>Codificação de alta disponibilidade dos serviços de mídia 

O serviço de codificação dos serviços de mídia do Azure é uma plataforma de processamento de lote regional e, no momento, não foi projetado para alta disponibilidade em uma única região. O serviço de codificação atualmente não fornece um failover instantâneo do serviço se houver uma interrupção regional do Datacenter ou uma falha do componente subjacente ou dos serviços dependentes (como armazenamento, SQL). Este artigo explica como implantar os serviços de mídia para manter uma arquitetura de alta disponibilidade com failover e garantir a disponibilidade ideal para seus aplicativos.

Seguindo as diretrizes e as práticas recomendadas descritas no artigo, você reduzirá o risco de falhas de codificação, atrasos e minimizará o tempo de recuperação se ocorrer uma interrupção em uma única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como criar um sistema de codificação entre regiões

* [Crie](create-account-cli-how-to.md) duas (ou mais) contas dos serviços de mídia do Azure.

    As duas contas precisam estar em regiões diferentes. Para obter mais informações, consulte [regiões nas quais o serviço dos serviços de mídia do Azure está implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Carregue sua mídia na mesma região da qual você está planejando para enviar o trabalho. Para obter mais informações sobre como iniciar a codificação, consulte [criar uma entrada de trabalho de uma URL https](job-input-from-http-how-to.md) ou [criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

    Se você precisar reenviar o [trabalho](transforms-jobs-concept.md) para outra região, poderá usar JobInputHttp ou usar [Copy-blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) para copiar os dados do contêiner de ativos de origem para um contêiner de ativos na região alternativa.
* Assine mensagens JobStateChange em cada conta por meio da grade de eventos do Azure. Para obter mais informações, consulte:

    * [Exemplo de análise de áudio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) que mostra como monitorar um trabalho com a grade de eventos do Azure, incluindo a adição de um fallback, caso as mensagens da grade de eventos do Azure sejam atrasadas por algum motivo.
    * [Esquemas da grade de eventos do Azure para eventos dos serviços de mídia](media-services-event-schemas.md)
    * [Registrar-se para eventos por meio do portal do Azure ou da CLI](reacting-to-media-services-events.md) (você também pode fazer isso com o SDK de gerenciamento do EventGrid)
    * [SDK do Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que dá suporte a eventos dos serviços de mídia nativamente).

    Você também pode consumir eventos de grade de eventos via Azure Functions.
*    Quando você cria um [trabalho](transforms-jobs-concept.md):
    
    * Selecione aleatoriamente uma conta na lista de contas atualmente usadas (essa lista normalmente conterá as duas contas, mas se forem detectadas problemas, ela poderá conter apenas uma conta). Se a lista estiver vazia, gere um alerta para que um operador possa investigar.
    * As diretrizes gerais são necessárias para uma [unidade reservada de mídia](media-reserved-units-cli-how-to.md) por [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a menos que você esteja usando [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) em que é recomendado 3 unidades reservadas de mídia por JobOutput).
    * Obtenha a contagem de unidades reservadas de mídia (MRUs) para a conta escolhida. Se a contagem de **unidades reservadas de mídia** atual ainda não estiver no valor máximo, adicione o número do MRUs necessário para o trabalho e atualize o serviço. Se a taxa de envio do trabalho for alta e você estiver consultando com frequência o MRUs para descobrir que você está no máximo, use um cache distribuído para o valor com um tempo limite razoável.
    * Mantenha uma contagem do número de trabalhos do em andamento.

* Quando o manipulador JobStateChange recebe uma notificação de que um trabalho atingiu o estado agendado, registre a hora em que ele entra no estado de agendamento e a região/conta usada.
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado de processamento, marque o registro para o trabalho como processamento.
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado concluído/com erro/cancelado, marque o registro para o trabalho como final e reduza a contagem de trabalhos em andamento. Obtenha o número de unidades reservadas de mídia para a conta escolhida e compare o número de MRU atual com relação à contagem de trabalhos do em andamento. Se sua contagem de em andamento for menor que a contagem de MRU, decrementar e atualizar o serviço.
* Ter um processo separado que examina periodicamente os registros dos trabalhos
    
    * Se você tiver trabalhos no estado agendado que não são avançados para o estado de processamento em um período razoável de tempo para uma determinada região, remova essa região da sua lista de contas atualmente usadas.  Dependendo dos seus requisitos de negócios, você pode optar por cancelar esses trabalhos imediatamente e reenviá-los para a outra região. Ou você pode dar mais tempo para passar para o próximo estado.
    * Dependendo do número de unidades reservadas de mídia configuradas na conta e da taxa de envio, também poderá haver trabalhos no estado enfileirado que o sistema ainda não tenha selecionado para processamento.  Se a lista de trabalhos no estado de fila aumentar além de um limite aceitável em uma região, esses trabalhos poderão ser cancelados e enviados para a outra região.  No entanto, isso pode ser um sintoma de não ter unidades reservadas de mídia suficientes configuradas na conta para a carga atual.  Você pode solicitar uma cota de unidade reservada de mídia maior por meio do suporte do Azure, se necessário.
    * Se uma região tiver sido removida da lista de contas, monitore-a para recuperação antes de adicioná-la de volta à lista.  A integridade regional pode ser monitorada por meio dos trabalhos existentes na região (se eles não foram cancelados e reenviados), adicionando a conta de volta à lista após um período de tempo e por operadores monitorando as comunicações do Azure sobre interrupções que podem estar afetando Serviços de mídia do Azure.
    
Se você achar que a contagem de MRU é ultrapaginação e verticalmente, mova a lógica de decréscimo para a tarefa periódica. Faça com que a lógica de envio de pré-trabalho Compare a contagem de em andamento com a contagem de MRU atual para ver se precisa atualizar o MRUs.

## <a name="next-steps"></a>Próximas etapas

* [Criar streaming entre regiões de vídeo por demanda](media-services-high-availability-streaming.md)
* Confira [exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
