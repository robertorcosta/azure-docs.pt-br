---
title: Experimente o serviço de Fala gratuitamente
titleSuffix: Azure Cognitive Services
description: Começar com o serviço de fala é fácil e acessível. Existem duas opções disponíveis gratuitamente para que você possa descobrir o que o serviço pode fazer e decidir se é certo para suas necessidades.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219660"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Fala gratuitamente

Neste artigo, você escolhe uma opção para testar facilmente o serviço de fala gratuitamente para que você possa descobrir o que o serviço pode fazer e decidir se é certo para suas necessidades. Escolha uma das duas opções a seguir, dependendo da sua situação e do caso de uso:

- [Opção 1](#no-card): Obter imediatamente chaves de API **de avaliação gratuita** sem fornecer qualquer informação de cartão de crédito (você precisa ter uma conta Azure existente). A **avaliação gratuita** dura 30 dias e os dados são excluídos no final. Esta opção é melhor para experimentação rápida com o serviço.
- [Opção 2](#new-resource): Criar um novo recurso Speech no Azure, gratuitamente usando uma **assinatura gratuita** (informações de cartão de crédito necessárias). Uma **assinatura gratuita** tem principalmente limites de taxa mais rigorosos do que uma assinatura paga. Essa opção é melhor se você quiser testar o serviço, mas também planeja atualizar para uma assinatura paga no futuro, e não quer perder dados.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Experimente o serviço Speech sem informações de cartão de crédito

Complete as seguintes etapas para ativar uma avaliação gratuita de 30 dias e obter as chaves da API. Seu período de teste começa imediatamente quando as seguintes etapas estiverem concluídas.

1. Vá para [Tentar Serviço Cognitivo](https://azure.microsoft.com/try/cognitive-services/).
1. Selecione a guia **APIs de Fala**.
1. Escolha **Obter a chave da API**.

Você será presenteado com escolhas de faturamento. Escolha a opção gratuita e, em seguida, leia e aprove o contrato de usuário. Você será presenteado com chaves que você pode usar para experimentar o serviço de fala gratuitamente por 30 dias.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Experimente o serviço Speech criando um recurso do Azure

Para as etapas seguintes, você precisa de uma conta Microsoft e uma conta do Azure. Se você não tiver uma conta microsoft, você pode se inscrever gratuitamente no portal da [conta da Microsoft](https://account.microsoft.com/account). Selecione **Entrar com a Microsoft** e, em seguida, quando solicitado a fazer login, **selecione Criar uma conta Microsoft**. Siga as etapas para criar e confirmar sua nova conta Microsoft.

Depois de ter uma conta microsoft, vá até a página de inscrição do [Azure,](https://azure.microsoft.com/free/ai/)selecione **Iniciar gratuitamente**e crie uma nova conta do Azure usando uma conta microsoft.

> [!NOTE]
> O serviço Speech possui dois níveis de serviço: gratuito e por assinatura, que possuem diferentes limitações e benefícios. Quando você se inscreve em uma conta gratuita do Azure, ele vem com US$ 200 em crédito de serviço que você pode solicitar para uma assinatura de serviço pago do Speech, válida por até 30 dias.
>
> Se você usar o nível de serviço de fala gratuito e de baixo volume, você pode manter esta assinatura gratuita mesmo depois que sua avaliação gratuita ou crédito de serviço expirar.
>
> Para obter mais informações, consulte [preços de Serviços Cognitivos - Serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Criar o recurso

Para adicionar um recurso de serviço de Fala (camada gratuita ou paga) à sua conta do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/) usando sua conta Microsoft.

1. Selecione **Criar um recurso** na parte superior esquerda do portal. Se você não ver **Criar um recurso,** você sempre pode encontrá-lo selecionando o menu colapsado no canto superior esquerdo:

   ![botão de navegação colapsado](media/index/collapsed-nav.png)

1. Na janela **Nova,** digite "fala" na caixa de pesquisa e pressione ENTER.

1. Nos resultados da pesquisa, selecione **Fala**.

   ![resultados de pesquisa de fala](media/index/speech-search.png)

1. Selecione **Criar,** então:

   - Dê um nome único para o seu novo recurso. O nome ajuda a distinguir entre várias assinaturas vinculadas ao mesmo serviço.
   - Escolha a assinatura do Azure a qual o novo recurso está associado para determinar como os valores serão cobrados.
   - Escolha a [região](regions.md) onde o recurso será utilizado.
   - Escolha um nível de preços gratuito (F0) ou pago (S0). Para obter informações completas sobre preços e cotas de uso para cada nível, **selecione Exibir detalhes completos de preços**.
   - Crie um grupo de recursos para esta assinatura de Fala ou atribua a assinatura a um grupo de recursos existente. Os grupos de recurso ajudam você a manter suas diversas assinaturas do Azure organizadas.
   - Selecione **Criar**. Isso o levará à visão geral da implantação e exibirá mensagens de progresso de implantação.

> [!NOTE]
> Você pode criar um número ilimitado de assinaturas da camada Standard em uma ou várias regiões. No entanto, você só pode criar uma assinatura da camada gratuita. As implantações do modelo no nível livre que permanecerem sem uso por 7 dias serão desativadas automaticamente.

Leva alguns momentos para implantar seu novo recurso speech. Uma vez concluída a implantação, **selecione Ir para recurso** e no painel de navegação à esquerda selecione **Chaves** para exibir suas chaves de assinatura do serviço Speech. Cada assinatura tem duas chaves; você pode usar uma das chaves em seu aplicativo. Para copiar/colar rapidamente uma chave para o editor de código ou outro local, selecione o botão de cópia ao lado de cada tecla, alterne as janelas para colar o conteúdo da área de transferência para o local desejado.

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de Serviço Cognitivo. Não compartilhe suas chaves. Armazená-los com segurança, por exemplo, usando o Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada de API. Ao regenerar a primeira tecla, você pode usar a segunda chave para acesso contínuo ao serviço.

## <a name="switch-to-a-new-subscription"></a>Alternar para uma nova assinatura

Para alternar de uma assinatura para outra, por exemplo, quando a avaliação gratuita expirar ou quando você publicar seu aplicativo, substitua a região e a chave de assinatura no código pela região e pela chave de assinatura do novo recurso do Azure.

## <a name="about-regions"></a>Sobre regiões

- Se o aplicativo usar um [SDK de Fala](speech-sdk.md), forneça o código da região, como `westus`, ao criar uma configuração de fala.
- Se o aplicativo usar uma das [APIs REST](rest-apis.md) do serviço de Fala, a região fará parte do URI do ponto de extremidade usado ao fazer solicitações.
- As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-las com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Próximas etapas

Conclua um de nossos inícios rápidos de 10 minutos ou confira nossas amostras de SDK:

> [!div class="nextstepaction"]
> [Quickstart: Reconheça a fala em](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> amostras[de C# Speech SDK](speech-sdk.md#get-the-samples)
