---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006464"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localizar uma âncora espacial de nuvem

Ser capaz de localizar uma âncora espacial de nuvem salva anteriormente é um dos principais motivos para usar as Âncoras Espaciais do Azure. Há várias maneiras de localizar uma âncora espacial de nuvem. Você pode usar uma estratégia em um observador por vez.
- Localizar âncoras por identificador.
- Localizar âncoras conectadas a uma âncora localizada anteriormente. Saiba mais sobre as relações entre âncoras [aqui](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Localizar âncora usando a [Relocalização grosseira](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Cada vez que você localizar uma âncora, as Âncoras Espaciais do Azure tentarão usar os dados do ambiente coletados para aumentar as informações visuais na âncora. Se você tiver problemas para localizar uma âncora, poderá ser útil criar uma âncora e, em seguida, localizá-la várias vezes de diferentes ângulos e condições de iluminação.

Se estiver localizando âncoras espaciais de nuvem pelo identificador, você desejará armazenar o identificador da âncora espacial de nuvem no serviço de back-end do aplicativo e torná-lo acessível para todos os dispositivos que puderem se autenticar nele corretamente. Para ver um exemplo disso, confira [Tutorial: Compartilhar âncoras espaciais entre dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Crie uma instância de um objeto `AnchorLocateCriteria`, defina os identificadores que está procurando e invoque o método `CreateWatcher` na sessão fornecendo seu `AnchorLocateCriteria`.