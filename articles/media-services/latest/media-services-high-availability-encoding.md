---
title: Codificação de alta disponibilidade do Azure Media Services
description: Saiba como fazer o failover em uma conta secundária dos Serviços de Mídia se ocorrer uma paralisação ou falha no data center regional.
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934187"
---
# <a name="media-services-high-availability-encoding"></a>Codificação de alta disponibilidade dos Serviços de Mídia 

O serviço de codificação Azure Media Services é uma plataforma regional de processamento em lote e não foi projetada atualmente para alta disponibilidade em uma única região. O serviço de codificação atualmente não fornece failover instantâneo do serviço se houver uma paralisação regional do datacenter ou falha de componentes subjacentes ou serviços dependentes (como armazenamento, SQL). Este artigo explica como implantar serviços de mídia para manter uma arquitetura de alta disponibilidade com failover e garantir a disponibilidade ideal para seus aplicativos.

Seguindo as diretrizes e práticas recomendadas descritas no artigo, você reduzirá o risco de falhas de codificação, atrasos e minimizará o tempo de recuperação se ocorrer uma paralisação em uma única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como construir um sistema de codificação inter-regional

* [Crie](create-account-cli-how-to.md) duas (ou mais) contas do Azure Media Services.

    As duas contas precisam estar em regiões diferentes. Para obter mais informações, consulte [as regiões em que o serviço azure Media Services é implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Envie sua mídia para a mesma região da qual você está planejando enviar o trabalho. Para obter mais informações sobre como iniciar a codificação, consulte [Criar uma entrada de trabalho a partir de uma URL HTTPS](job-input-from-http-how-to.md) ou criar uma entrada de trabalho a partir de um arquivo [local](job-input-from-local-file-how-to.md).

    Se você precisar reenviar o [trabalho](transforms-jobs-concept.md) para outra região, você pode usar JobInputHttp ou usar [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) para copiar os dados do contêiner De ré para um contêiner De ativos na região alternativa.
* Inscreva-se em Mensagens JobStateChange em cada conta via Azure Event Grid. Para obter mais informações, consulte:

    * [Amostra de análise de áudio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) que mostra como monitorar um trabalho com o Azure Event Grid, incluindo a adição de um recuo no caso de as mensagens do Azure Event Grid serem adiadas por algum motivo.
    * [Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia](media-services-event-schemas.md)
    * [Registre-se para eventos através do portal Azure ou da CLI](reacting-to-media-services-events.md) (você também pode fazê-lo com o EventGrid Management SDK)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que suporta eventos de Serviços de Mídia nativamente).

    Você também pode consumir eventos da Event Grid através de Funções Azure.
* Quando você cria um [emprego:](transforms-jobs-concept.md)

    * Selecione aleatoriamente uma conta da lista de contas usadas atualmente (esta lista normalmente conterá ambas as contas, mas se os problemas forem detectados, ela poderá conter apenas uma conta). Se a lista estiver vazia, levante um alerta para que um operador possa investigar.
    * A orientação geral é que você precisa de uma [unidade reservada](media-reserved-units-cli-how-to.md) de mídia por [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a menos que você esteja usando [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) onde 3 unidades reservadas de mídia por JobOutput é recomendada).
    * Obtenha a contagem de unidades de mídia reservadas (MRUs) para a conta escolhida. Se a contagem de **unidades reservadas** de mídia atual ainda não estiver no valor máximo, adicione o número das MRUs necessárias pelo trabalho e atualize o serviço. Se a taxa de submissão de emprego for alta e você estiver consultando frequentemente as MRUs para descobrir que você está no máximo, use um cache distribuído para o valor com um tempo limite razoável.
    * Mantenha uma contagem do número de empregos a bordo.

* Quando o manipulador JobStateChange recebe uma notificação de que um trabalho atingiu o estado programado, registre a hora em que ele entra no estado de agendamento e na região/conta usada.
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado de processamento, marque o recorde para o trabalho como processamento.
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado concluído/errado/cancelado, marque o registro para o trabalho como final e decrete a contagem de trabalho de bordo. Obtenha o número de unidades reservadas de mídia para a conta escolhida e compare o número atual de MRU com a sua contagem de trabalho a bordo. Se a contagem de bordo for menor que a contagem de ressonância magnética, decrete-a e atualize o serviço.
* Tenha um processo separado que periodicamente analisa seus registros dos trabalhos
    
    * Se você tem empregos no estado programado que não avançaram para o estado de processamento em um período razoável de tempo para uma determinada região, remova essa região da sua lista de contas usadas atualmente.  Dependendo dos requisitos do seu negócio, você pode decidir cancelar esses trabalhos imediatamente e reenviá-los para a outra região. Ou, você poderia dar-lhes algum tempo mais para se mudar para o próximo estado.
    * Dependendo do número de Unidades Reservadas de Mídia configuradas na conta e da taxa de submissão, também pode haver empregos no estado enfileirado que o sistema ainda não pegou para processamento.  Se a lista de empregos no estado enfileirado crescer além do limite aceitável em uma região, esses empregos podem ser cancelados e submetidos à outra região.  No entanto, isso pode ser um sintoma de não ter unidades reservadas de mídia suficientes configuradas na conta para a carga atual.  Você pode solicitar uma cota maior de Unidade Reservada de Mídia através do Suporte Azure, se necessário.
    * Se uma região foi removida da lista de contas, monitore-a para recuperação antes de adicioná-la de volta à lista.  A saúde regional pode ser monitorada através dos empregos existentes na região (se não foram canceladas e reenviadas), adicionando a conta de volta à lista após um período de tempo, e por operadores monitorando as comunicações do Azure sobre paralisações que podem estar afetando Azure Media Services.
    
Se você achar que a contagem de ressonânciamagnética está batendo muito para cima e para baixo, mova a lógica de decrésia para a tarefa periódica. Faça com que a lógica de envio de pré-trabalho compare a contagem de bordo com a contagem atual de MRU para ver se ele precisa atualizar as MRUs.

## <a name="next-steps"></a>Próximas etapas

* [Construa streaming de região transversal de vídeo demanda](media-services-high-availability-streaming.md)
* Confira [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
