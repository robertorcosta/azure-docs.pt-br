---
title: Experimente o serviço de Fala gratuitamente
titleSuffix: Azure Cognitive Services
description: A introdução ao serviço de fala é fácil e acessível. Há duas opções disponíveis gratuitamente para que você possa descobrir o que o serviço pode fazer e decidir se ele é adequado para suas necessidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388277"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Fala gratuitamente

Neste artigo, você escolhe uma opção para testar facilmente o serviço de fala gratuitamente para que você possa descobrir o que o serviço pode fazer e decidir se ele é adequado para suas necessidades. Escolha uma das duas opções a seguir, dependendo da sua situação e do caso de uso:

- [Opção 1](#no-card): Obtenha imediatamente as chaves de API de **avaliação gratuitas** sem fornecer nenhuma informação de cartão de crédito (você precisa ter uma conta existente do Azure). A **avaliação gratuita** dura 30 dias e os dados são excluídos no final. Essa opção é melhor para experimentação rápida com o serviço.
- [Opção 2](#new-resource): criar um novo recurso de fala no Azure, sem nenhum encargo usando uma **assinatura gratuita** (informações de cartão de crédito necessárias). Uma **assinatura gratuita** tem, principalmente, limites de taxa mais rígidas do que uma assinatura paga. Essa opção é melhor se você quiser testar o serviço, mas também planeja atualizar para uma assinatura paga no futuro e não quer perder dados.

## <a id="no-card"></a>Experimente o serviço de fala sem informações de cartão de crédito

Conclua as etapas a seguir para ativar uma avaliação gratuita de 30 dias e obter as chaves de API. O período de avaliação começará imediatamente depois que as etapas a seguir forem concluídas.

1. Vá para [experimentar o serviço cognitiva](https://azure.microsoft.com/try/cognitive-services/).
1. Selecione a guia **APIs de Fala**.
1. Escolha **obter chave de API**.

Você verá as opções de cobrança. Escolha a opção gratuito e, em seguida, leia e aprove o contrato de usuário. Você verá as chaves que pode usar para experimentar o serviço de fala gratuitamente por 30 dias.

## <a id="new-resource"></a>Experimente o serviço de fala criando um recurso do Azure

Para as etapas a seguir, você precisa de um conta Microsoft e uma conta do Azure. Se você não tiver um conta Microsoft, poderá se inscrever gratuitamente no [portal de conta Microsoft](https://account.microsoft.com/account). Selecione **entrar com a conta da Microsoft** e, quando solicitado a entrar, selecione **criar um conta Microsoft**. Siga as etapas para criar e confirmar sua nova conta Microsoft.

Quando você tiver uma conta Microsoft, vá para a [página de inscrição do Azure](https://azure.microsoft.com/free/ai/), selecione **Iniciar gratuitamente**e crie uma nova conta do Azure usando uma conta Microsoft.

> [!NOTE]
> O serviço de fala tem duas camadas de serviço: gratuita e assinatura, que têm diferentes limitações e benefícios. Quando você se inscreve em uma conta gratuita do Azure, ela vem com $200 de crédito de serviço que você pode aplicar a uma assinatura de serviço de fala paga, válida por até 30 dias.
>
> Se você usar a camada de serviço de fala de baixo volume, gratuita, poderá manter essa assinatura gratuita mesmo depois que a avaliação gratuita ou o crédito de serviço expirar.
>
> Para obter mais informações, consulte [preços de serviços cognitivas – serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Criar o recurso

Para adicionar um recurso de serviço de Fala (camada gratuita ou paga) à sua conta do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/) usando sua conta Microsoft.

1. Selecione **Criar um recurso** na parte superior esquerda do portal. Se você não vir **criar um recurso**, você sempre poderá encontrá-lo selecionando o menu recolhido no canto superior esquerdo:

   ![botão de navegação recolhido](media/index/collapsed-nav.png)

1. Na **nova** janela, digite "fala" na caixa de pesquisa e pressione Enter.

1. Nos resultados da pesquisa, selecione **Fala**.

   ![resultados da pesquisa de fala](media/index/speech-search.png)

1. Selecione **criar**e:

   - Forneça um nome exclusivo para o novo recurso. O nome ajuda você a distinguir entre várias assinaturas vinculadas ao mesmo serviço.
   - Escolha a assinatura do Azure a qual o novo recurso está associado para determinar como os valores serão cobrados.
   - Escolha a [região](regions.md) em que o recurso será usado.
   - Escolha um tipo de preço gratuito (F0) ou pago (S0). Para obter informações completas sobre cotas de preço e uso para cada camada, selecione **Exibir detalhes de preços completos**.
   - Crie um grupo de recursos para esta assinatura de Fala ou atribua a assinatura a um grupo de recursos existente. Os grupos de recurso ajudam você a manter suas diversas assinaturas do Azure organizadas.
   - Selecione **Criar**. Isso o levará para a visão geral da implantação e exibirá as mensagens de progresso da implantação.

> [!NOTE]
> Você pode criar um número ilimitado de assinaturas da camada Standard em uma ou várias regiões. No entanto, você só pode criar uma assinatura da camada gratuita. As implantações de modelo na camada gratuita que permanecem sem uso por 7 dias serão encerradas automaticamente.

Leva alguns minutos para implantar o novo recurso de fala. Após a conclusão da implantação, selecione **ir para o recurso** e, no painel de navegação esquerdo, selecione **chaves** para exibir as chaves de assinatura do serviço de fala. Cada assinatura tem duas chaves; você pode usar uma das chaves em seu aplicativo. Para copiar/colar rapidamente uma chave no editor de código ou em outro local, selecione o botão copiar ao lado de cada chave, alterne o Windows para colar o conteúdo da área de transferência no local desejado.

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança – por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.

## <a name="switch-to-a-new-subscription"></a>Alternar para uma nova assinatura

Para alternar de uma assinatura para outra, por exemplo, quando a avaliação gratuita expirar ou quando você publicar seu aplicativo, substitua a região e a chave de assinatura no código pela região e pela chave de assinatura do novo recurso do Azure.

## <a name="about-regions"></a>Sobre regiões

- Se o aplicativo usar um [SDK de Fala](speech-sdk.md), forneça o código da região, como `westus`, ao criar uma configuração de fala.
- Se o aplicativo usar uma das [APIs REST](rest-apis.md) do serviço de Fala, a região fará parte do URI do ponto de extremidade usado ao fazer solicitações.
- As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-las com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Conclua um de nossos inícios rápidos de 10 minutos ou confira nossas amostras de SDK:

> [!div class="nextstepaction"]
> [Início Rápido: Reconhecer a fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Amostras de SDK de Fala](speech-sdk.md#get-the-samples)
