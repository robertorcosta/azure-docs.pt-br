---
title: Alta disponibilidade dos serviços de mídia do Azure
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202280"
---
# <a name="azure-media-services-high-availability-guidance"></a>Diretrizes de alta disponibilidade dos serviços de mídia do Azure

O serviço de codificação dos serviços de mídia do Azure é uma plataforma de processamento de lote regional e, no momento, não foi projetado para alta disponibilidade em uma única região. O serviço de codificação atualmente não fornece um failover instantâneo do serviço se houver uma interrupção regional no Datacenter ou falha do componente subjacente ou dos serviços dependentes (como armazenamento, SQL, etc.)  Este artigo explica como implantar os serviços de mídia para manter uma arquitetura de alta disponibilidade com failover e garantir a disponibilidade ideal para seus aplicativos. 

Seguindo as diretrizes e as práticas recomendadas descritas no artigo, você reduzirá o risco de falhas de codificação, atrasos e minimizará o tempo de recuperação se ocorrer uma interrupção em uma única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como criar um sistema de codificação entre regiões

* [Crie](create-account-cli-how-to.md) duas (ou mais) contas dos serviços de mídia do Azure.
* Assine as mensagens **JobStateChange** em cada conta.

    * No Media Services V3, isso é feito por meio da grade de eventos do Azure. Para obter mais informações, consulte:
    
       * [Exemplos de grade de eventos](../../event-grid/receive-events.md), 
       * [Esquemas da grade de eventos do Azure para eventos dos serviços de mídia](media-services-event-schemas.md), 
       * [Registrar-se para eventos por meio do portal do Azure ou da CLI](reacting-to-media-services-events.md) (você também pode fazer isso com o SDK de gerenciamento do EventGrid)
       * [SDK do Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que dá suporte a eventos dos serviços de mídia nativamente). 
       
        Você também pode consumir eventos de grade de eventos via Azure Functions.
    * Nos serviços de mídia v2, isso é feito por meio de [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Quando você [cria um trabalho](transforms-jobs-concept.md):

    * Selecione aleatoriamente uma conta na lista de contas atualmente usadas (essa lista normalmente conterá as duas contas, mas se forem detectadas problemas, ela poderá conter apenas uma conta). Se a lista estiver vazia, gere um alerta para que um operador possa investigar.
    * As diretrizes gerais são necessárias para uma [unidade reservada de mídia](media-reserved-units-cli-how-to.md) por tarefa ou [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a menos que você esteja usando [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) em v3).
    * Obtenha a contagem de [unidades reservadas de mídia](media-reserved-units-cli-how-to.md) (MRUs) para a conta escolhida. Se a contagem de **unidades reservadas de mídia** atual ainda não estiver no valor máximo, adicione o número do MRUs necessário para o trabalho e atualize o serviço. Se a taxa de envio do trabalho for alta e você estiver consultando com frequência o MRUs para descobrir que você está no máximo, use um cache distribuído para o valor com um tempo limite razoável.
    * Mantenha uma contagem do número de trabalhos do em andamento.
* Quando o manipulador JobStateChange recebe uma notificação de que um trabalho atingiu o estado agendado, registre a hora em que ele entra no estado de agendamento e a região/conta usada.    
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado de processamento, marque o registro para o trabalho como processamento.
* Quando o manipulador JobStateChange receber uma notificação de que um trabalho atingiu o estado concluído/com erro/cancelado, marque o registro para o trabalho como final e reduza a contagem de trabalhos em andamento. Obtenha o número de unidades reservadas de mídia para a conta escolhida e compare o número de MRU atual com relação à contagem de trabalhos do em andamento. Se sua contagem de em andamento for menor que a contagem de MRU, decrementar e atualizar o serviço.
* Ter um processo separado que examina periodicamente os registros dos trabalhos. Se você tiver trabalhos no estado agendado que não são avançados para o estado de processamento em um período razoável de tempo para uma determinada região, remova essa região da sua lista de contas atualmente usadas.

    * Dependendo dos seus requisitos de negócios, você pode optar por cancelar esses trabalhos imediatamente e reenviá-los para a outra conta. Ou você pode dar mais tempo para passar para o próximo estado.   
    * Após um período de tempo, adicione a conta de volta à lista atualmente usada (com a suposição de que a região foi recuperada).
    
Se você achar que a contagem de MRU é ultrapaginação e verticalmente, mova a lógica de decréscimo para a tarefa periódica. Faça com que a lógica de envio de pré-trabalho Compare a contagem de em andamento com a contagem de MRU atual para ver se precisa atualizar o MRUs.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como criar streaming de região cruzada de vídeo por demanda 

* O streaming de região cruzada de vídeo por demanda envolve a duplicação de [ativos](assets-concept.md), [políticas de chave de conteúdo](content-key-policy-concept.md) (se usadas), políticas de [streaming](streaming-policy-concept.md)e [localizadores de streaming](streaming-locators-concept.md). 
* Você precisará criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Ao criar os localizadores de streaming, você desejará usar o mesmo valor de ID de localizador, o valor de ID de ContentKey e o valor de ContentKey.  
* Se você estiver codificando o conteúdo, é aconselhável codificar o conteúdo na região A e publicá-lo, em seguida, copiar o conteúdo codificado para a região B e publicá-lo usando os mesmos valores da região A.
* Você pode usar o Gerenciador de tráfego nos nomes de host para a origem e o serviço de distribuição de chaves (na configuração dos serviços de mídia, isso será semelhante a uma URL de servidor de chave personalizada).

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-account-cli-how-to.md)
* Confira [exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
