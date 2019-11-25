---
title: 'Início Rápido: Pesquisa de Mapa Interativo com o Azure Mapas'
description: Início Rápido do Azure – Criar uma demonstração de pesquisa de mapa interativo usando o Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1b9d8c98391e7e2bac3492dcf696f098f5c6cf57
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903174"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Início Rápido: Criar um mapa de pesquisa interativo usando o Azure Mapas

Este artigo demonstra os recursos dos Mapas do Azure para criar um mapa que fornece aos usuários uma experiência de pesquisa interativa. Ele apresenta e explica as seguintes etapas básicas:
* Criar sua conta do Azure Mapas.
* Obter a chave de conta para usar no aplicativo Web de demonstração.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Criar uma conta e obter sua chave

1. No canto superior esquerdo do [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.
2. Na caixa **Pesquisar no Marketplace**, digite **Mapas**.
3. Nos **Resultados**, selecione **Mapas**. Selecione o botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta do Azure Mapas**, insira os seguintes valores:
   - O **Nome** da sua nova conta.
   - A **Assinatura** que você deseja usar para a conta.
   - O **Grupo de recursos** dessa conta. Você pode optar por **Criar novo** ou **Usar existente** em relação ao grupo de recursos.
   - Selecione o **Tipo de Preço** de sua escolha.
   - Leia a **Licença** e a **Política de Privacidade**. Marque a caixa de seleção para aceitar os termos.
   - Por fim, selecione o botão **Criar**.

     ![Crie uma conta do Azure Mapas no portal](./media/quick-demo-map-app/create-account.png)

5. Depois de criar a conta, abra-a e encontre a seção de configurações no menu da conta. Selecione **Autenticação** para exibir as chaves primária e secundária de sua conta dos Azure Mapas. Copie o valor de **Chave Primária** para a área de transferência local a ser usada na seção a seguir.

## <a name="download-the-application"></a>Fazer o download do aplicativo

1. Baixe ou copie o conteúdo do arquivo [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html).
2. Salve o conteúdo deste arquivo localmente como **AzureMapDemo.html**. Abra-o em um editor de texto.
3. Pesquise pela cadeia de caracteres `<Your Azure Maps Key>`. Substitua-o pelo valor **Chave primária** da seção anterior.

## <a name="open-the-application"></a>Abra o aplicativo

1. Abra o arquivo **AzureMapDemo.html** em um navegador de sua escolha.
2. Observe o mapa mostrado da Cidade de Los Angeles. Amplie e reduza para ver como o mapa renderiza automaticamente com mais ou menos informações, dependendo do nível de zoom. 
3. Altere o centro padrão do mapa. No arquivo **AzureMapDemo.html**, procure a variável chamada **center**. Substitua o par de valores de latitude e longitude dessa variável pelos novos valores **[-74,0060; 40,7128]** . Salve o arquivo e atualize seu navegador.
4. Teste a experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo do aplicativo Web da demonstração, pesquise **restaurantes**.
5. Mova o mouse sobre a lista de endereços e locais que aparecem abaixo da caixa de pesquisa. Observe como o marcador correspondente no mapa mostra informações sobre esse local. Para preservar a privacidade de empresas particulares, são mostrados endereços e nomes fictícios.

    ![Aplicativo Web de pesquisa interativa](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais entram em detalhes sobre como usar e configurar o Azure Mapas com sua conta. Se você planeja continuar com os tutoriais, não limpe os recursos criados neste início rápido. Se você não planeja continuar, siga estas etapas para limpar os recursos:

1. Feche o navegador que está executando o aplicativo Web **AzureMapDemo.html**.
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos**. Em seguida, selecione sua conta do Azure Mapas. Na parte superior da folha **Todos os recursos**, marque **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou sua conta do Azure Mapas e criou um aplicativo de demonstração. Confira os seguintes tutoriais para saber mais sobre os Azure Mapas:

> [!div class="nextstepaction"]
> [Pesquisar pontos de interesse próximos usando o Azure Mapas](tutorial-search-location.md)

Para obter mais exemplos de código e uma experiência interativa de codificação, consulte estes guias:

> [!div class="nextstepaction"]
> [Encontrar um endereço usando o serviço de pesquisa do Azure Mapas](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Usar o Controle de Mapeamento do Azure Mapas](how-to-use-map-control.md)
