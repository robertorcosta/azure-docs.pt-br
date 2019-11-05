---
title: Experimente o Speech Service gratuitamente
titleSuffix: Azure Cognitive Services
description: A introdução ao serviço de fala é fácil e acessível. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é o ideal para as necessidades do seu aplicativo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464687"
---
# <a name="try-speech-services-for-free"></a>Experimente os Serviços de Fala gratuitamente

É fácil e barato começar a usar os Serviços de Fala. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é o ideal para as necessidades do seu aplicativo.

Se você precisar de mais tempo, inscreva-se em uma conta do Microsoft Azure, ela vem com US$ 200 em crédito de serviço que você pode aplicar para em uma assinatura paga dos Serviços de Fala por até 30 dias.

Por fim, os Serviços de Fala oferecem uma camada gratuita, de baixo volume e adequada para o desenvolvimento de aplicativos. Você pode manter essa assinatura gratuita, mesmo depois que o crédito do serviço expirar.

## <a name="free-trial"></a>Avaliação gratuita

A avaliação gratuita de 30 dias fornece acesso ao tipo de preço padrão por tempo limitado.

Para se inscrever em uma avaliação gratuita de 30 dias:

1. Acesse [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

1. Selecione a guia **APIs de Fala**.

   ![Guia Speech API-serviços de fala](media/index/cognitive-services-speech-api-tab.png)

1. Em **serviços de fala**, selecione **obter chave de API**.

   ![Speech API-obter chave de API](media/index/speech-api-get-api-key.png)

1. Aceite os termos e selecione sua localidade no menu suspenso.

   ![Speech API-concordar com os termos](media/index/speech-api-agree-to-terms.png)

1. Entre usando sua conta Microsoft, do Facebook, do LinkedIn ou do GitHub.

    Inscreva-se em uma conta gratuita Microsoft no [portal de conta Microsoft](https://account.microsoft.com/account). Para começar, selecione **entrar com a conta da Microsoft** e, quando solicitado a entrar, selecione **criar um.** Siga as etapas para criar e confirmar sua nova conta Microsoft.

Depois de entrar em Experimentar os Serviços Cognitivos, sua avaliação gratuita será iniciada. A página da Web exibida lista todos os Serviços Cognitivos do Azure para os quais você tem uma assinatura de avaliação. Duas chaves de assinatura estão listadas ao lado de **Serviços de Fala**. Use uma das chaves em seus aplicativos.

> [!NOTE]
> Todas as assinaturas de avaliação gratuita estão na região Oeste dos EUA. Ao fazer solicitações, use o ponto de extremidade `westus`.

## <a name="new-azure-account"></a>Nova conta do Azure

As novas contas do Azure recebem um crédito de serviço de US$ 200 que fica disponível por até 30 dias. Use esse crédito para explorar ainda mais os Serviços de Fala ou para iniciar o desenvolvimento de aplicativos.

Para se inscrever em uma nova conta do Azure, vá para a [página de inscrição do Azure](https://azure.microsoft.com/free/ai/), selecione **Iniciar gratuitamente** e crie uma nova conta do Azure usando seu conta Microsoft.

Inscreva-se em uma conta gratuita Microsoft no [portal de conta Microsoft](https://account.microsoft.com/account). Para começar, selecione **entrar com a conta da Microsoft** e, quando solicitado a entrar, selecione **criar um.** Siga as etapas para criar e confirmar sua nova conta Microsoft.

Depois de criar sua conta do Azure, siga as etapas na próxima seção para iniciar uma assinatura dos Serviços de Fala.

## <a name="create-a-speech-resource-in-azure"></a>Criar um recurso de Fala no Azure

Para adicionar um recurso de Serviços de Fala (camada gratuita ou paga) à sua conta do Azure:

1. Entrar no [portal do Azure](https://portal.azure.com/) usando sua conta da Microsoft.

1. Selecione **Criar um recurso** na parte superior esquerda do portal.

    ![Speech API-criar um recurso](media/index/speech-api-create-resource.png)

1. Na janela **Nova**, pesquise **fala**.

1. Nos resultados da pesquisa, selecione **Fala**.

    ![Speech API-selecionar fala](media/index/speech-api-select-speech.png)

1. Em **Fala**, selecione o botão **Criar**.

    ![Botão Speech API-criar](media/index/speech-api-create-button.png)

1. Em **Criar**, insira:

   * Um nome para o novo recurso. O nome ajuda a distinguir entre várias assinaturas do mesmo serviço.
   * Escolha a assinatura do Azure a qual o novo recurso está associado para determinar como os valores serão cobrados.
   * Escolha a [região](regions.md) em que o recurso será usado.
   * Escolha um tipo de preço gratuito ou pago. Selecione **Exibir detalhes de preços completos** para obter informações completas sobre cotas de preço e uso para cada camada.
   * Crie um grupo de recursos para esta assinatura de Fala ou atribua a assinatura a um grupo de recursos existente. Os grupos de recurso ajudam você a manter suas diversas assinaturas do Azure organizadas.
   * Para ter acesso conveniente à sua assinatura futuramente, marque a caixa de seleção **Fixar no painel**.
   * Selecione **Criar.**

     ![Speech API-selecione criar](media/index/speech-api-select-create.png)

     São necessários alguns instantes para criar e implantar o novo recurso de Fala. Selecione **Início Rápido** para ver informações sobre o novo recurso.

     ![Speech API-implantar o recurso](media/index/speech-api-deploy-resource.png)

1. Em **início rápido**, selecione o link **chaves** na etapa 1 para exibir as chaves de assinatura. Cada assinatura tem duas chaves; você pode usar uma das chaves em seu aplicativo. Selecione o botão ao lado de cada chave para copiá-la para a área de transferência e colá-la no código.

> [!NOTE]
> Você pode criar um número ilimitado de assinaturas da camada Standard em uma ou várias regiões. No entanto, você só pode criar uma assinatura da camada gratuita. As implantações de modelo na camada gratuita que permanecerem inutilizadas por 7 dias serão desativadas automaticamente.

## <a name="switch-to-a-new-subscription"></a>Alternar para uma nova assinatura

Para alternar de uma assinatura para outra, por exemplo, quando a avaliação gratuita expirar ou quando você publicar seu aplicativo, substitua a região e a chave de assinatura no código pela região e pela chave de assinatura do novo recurso do Azure.

> [!NOTE]
> As chaves de avaliação gratuita são criadas na região Oeste dos EUA (`westus`). Uma assinatura criada por meio do painel do Azure pode estar em alguma outra região, se você assim escolher.

* Se o aplicativo usar um [SDK de Fala](speech-sdk.md), forneça o código da região, como `westus`, ao criar uma configuração de fala.
* Se o aplicativo usar uma das [APIs REST](rest-apis.md) dos Serviços de Fala, a região fará parte do URI do ponto de extremidade usado ao fazer solicitações.

As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-las com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Próximas etapas

Conclua um de nossos inícios rápidos de 10 minutos ou confira nossas amostras de SDK:

> [!div class="nextstepaction"]
> [Início Rápido: Reconhecer a fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Amostras de SDK de Fala](speech-sdk.md#get-the-samples)
