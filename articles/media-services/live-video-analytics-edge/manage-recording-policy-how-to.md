---
title: Gerenciar política de gravação-Azure
description: Este tópico explica como gerenciar a política de gravação.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011474"
---
# <a name="manage-recording-policy"></a>Gerenciar política de gravação

Você pode usar a análise de vídeo ao vivo em IoT Edge para [gravação contínua de vídeo](continuous-video-recording-concept.md), na qual você pode gravar vídeo na nuvem por semanas ou meses. Você pode gerenciar o tamanho (em dias) desse arquivo de nuvem usando as [ferramentas de gerenciamento de ciclo de vida](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) internas no armazenamento do Azure.  

Sua conta de serviço de mídia está vinculada a uma conta de armazenamento do Azure e, ao gravar vídeo na nuvem, o conteúdo é gravado em um [ativo](../latest/assets-concept.md)de serviço de mídia. Cada ativo é mapeado para um contêiner na conta de armazenamento. o gerenciamento do ciclo de vida permite que você defina uma [política](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) para uma conta de armazenamento, onde você pode especificar uma [regra](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) como a seguinte.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

A regra acima:

* Aplica-se a todos os blobs de blocos na conta de armazenamento.
* Especifica que, quando os BLOBs envelhecem além de 30 dias, eles são movidos da [camada de acesso quente para fria](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* E quando os BLOBs envelhecem além de 90 dias, eles devem ser excluídos.

Como a análise de vídeo ao vivo arquiva seu vídeo em unidades de tempo especificadas, seu ativo conterá uma série de BLOBs, um blob por segmento. Quando a política de gerenciamento de ciclo de vida iniciar e excluir BLOBs mais antigos, você continuará a ser capaz de acessar e reproduzir os BLOBs restantes por meio de APIs de serviço de mídia. Para obter mais informações, consulte [gravações de reprodução](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limitações

A seguir estão algumas limitações conhecidas do gerenciamento do ciclo de vida:

* Você pode ter no máximo 100 regras dentro da política e cada regra pode especificar até 10 contêineres. Portanto, se precisar ter diferentes políticas de gravação (por exemplo, arquivo de três dias para a câmera voltada para o estacionamento, 30 dias para a câmera no encaixe de carga e 180 dias para a câmera por trás do contador de check-out), com uma conta de serviço de mídia, você poderá personalizar as regras para no máximo 1000 câmeras.
* As atualizações de política de gerenciamento de ciclo de vida não são imediatas. Consulte [esta seção de perguntas frequentes](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) para obter mais detalhes.
* Se você optar por aplicar uma política em que os BLOBs são movidos para a camada fria, a reprodução dessa parte do arquivo pode ser afetada. Você pode ver latências adicionais ou erros esporádicos. Os serviços de mídia não oferecem suporte à reprodução de conteúdo na camada de arquivo morto.

## <a name="next-steps"></a>Próximas etapas

[Reprodução de gravações](playback-recordings-how-to.md)
