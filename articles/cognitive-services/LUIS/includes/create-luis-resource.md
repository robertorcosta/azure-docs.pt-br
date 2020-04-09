---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879183"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Criar recursos LUIS no portal do Azure

1. Use [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para começar a criar recursos LUIS no portal do Azure.

1. Insira todas as configurações necessárias:

    |Nome|Finalidade|
    |--|--|
    |Nome da assinatura| a assinatura que será cobrada pelo recurso.|
    |Resource group| O nome de um grupo de recursos personalizado que você escolher ou criar. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.|
    |Nome| Um nome personalizado que você escolhe, usado como um subdomínio personalizado para as consultas de ponto de extremidade de criação e previsão.|
    |Localização de criação|A região associada ao seu modelo.|
    |Tipo de preço de criação|O tipo de preço determina a transação máxima por segundo e por mês.|
    |Localização do runtime|A região associada ao runtime do ponto de extremidade de previsão publicado.|
    |Tipo de preço do runtime|O tipo de preço determina a transação máxima por segundo e por mês.|

    > [!div class="mx-imgBorder"]
    > [![Criar o recurso de reconhecimento vocal](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Clique em **Revisar + criar** e aguarde até que o recurso seja criado.
1. Após a criação dos dois recursos, ainda no portal do Azure, selecione o novo recurso de criação e, em seguida, **Inícios Rápido** para obter a **URL do ponto de extremidade** de criação e a **chave** para criação de maneira programática.

> [!TIP]
> Para usar os recursos, no portal do LUIS, [atribua os recursos](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).