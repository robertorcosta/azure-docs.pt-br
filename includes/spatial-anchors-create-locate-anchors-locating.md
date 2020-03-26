---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76887835"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localizar uma âncora espacial de nuvem

Ser capaz de localizar uma âncora espacial de nuvem salva anteriormente é um dos principais motivos para usar as Âncoras Espaciais do Azure. Há várias maneiras de localizar uma âncora espacial de nuvem. Você pode usar uma estratégia em um observador por vez.
- Localizar âncoras por identificador.
- Localizar âncoras conectadas a uma âncora localizada anteriormente. Saiba mais sobre as relações entre âncoras [aqui](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Localizar âncora usando a [Relocalização grosseira](/azure/spatial-anchors/concepts/coarse-reloc/).

Se estiver localizando âncoras espaciais de nuvem pelo identificador, você desejará armazenar o identificador da âncora espacial de nuvem no serviço de back-end do aplicativo e torná-lo acessível para todos os dispositivos que puderem se autenticar nele corretamente. Para ver um exemplo disso, confira [Tutorial: Compartilhar âncoras espaciais entre dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Crie uma instância de um objeto `AnchorLocateCriteria`, defina os identificadores que está procurando e invoque o método `CreateWatcher` na sessão fornecendo seu `AnchorLocateCriteria`.