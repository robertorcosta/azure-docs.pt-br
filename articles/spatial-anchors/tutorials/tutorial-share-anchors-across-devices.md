---
title: 'Tutorial: Compartilhar âncoras entre sessões e dispositivos'
description: Neste tutorial, você aprenderá como compartilhar identificadores de Âncora Espacial do Azure entre dispositivos Android/iOS no Unity com um serviço back-end.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 12a15a2d0cd45207b4adefc7315e48c6d642c0f7
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810150"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Compartilhar Âncoras Espaciais do Azure entre as sessões e dispositivos

Neste tutorial, você aprenderá a usar [Âncoras Espaciais do Azure](../overview.md) para criar âncoras durante uma sessão e, em seguida, localizá-las, no mesmo dispositivo ou em um diferente. Essas mesmas âncoras também poderiam ser localizadas por vários dispositivos no mesmo lugar e ao mesmo tempo.

![Persistência](./media/persistence.gif)

As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. Âncoras Espaciais do Azure criadas por uma instância podem ser compartilhadas com outras.

Você aprenderá a:

> [!div class="checklist"]
> * Implante um aplicativo Web do ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-as na memória por um tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity de nossos inícios rápidos para aproveitar o aplicativo Web de Âncoras de Compartilhamento.
> * Implante e execute um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora você vá usar o Unity e o Aplicativo Web ASP.NET Core neste tutorial, é apenas para mostrar um exemplo de como compartilhar identificadores de Âncora Espacial do Azure em outros dispositivos. Você pode usar outras linguagens e tecnologias de back-end para atingir o mesmo objetivo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implantar seu Serviço de Compartilhamento de Âncoras

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Você precisará criar um grupo de recursos e um Plano do Serviço de Aplicativo antes de implantar o serviço no VS Code.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Navegue até o <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e entre em sua assinatura do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup** e selecione **Ok**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Ao lado de **Plano de Hospedagem**, selecione **Novo**.

Na caixa de diálogo **Configurar Plano de Hospedagem**, use essas configurações:

| Configuração | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicativo| MySharingServicePlan | O nome do plano do Serviço de Aplicativo. |
| Location | Oeste dos EUA | O datacenter onde o aplicativo Web está hospedado. |
| Tamanho | Grátis | O [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina os recursos de hospedagem. |

Selecione **OK**.

Abra o Visual Studio Code e o projeto na pasta `Sharing\SharingServiceSample`. Siga <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">este tutorial</a> para implantar o serviço de compartilhamento por meio do Visual Studio Code. Você pode seguir as etapas na seção "Abrir com o Visual Studio Code". Não crie outro projeto do ASP.NET, conforme explicado na etapa acima, pois você já tem o projeto que precisa ser implantado e publicado: o SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implantar o aplicativo de exemplo

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um aplicativo Web do ASP.NET Core no Azure e, em seguida, configurou e implantou um aplicativo Unity. Você criou Âncoras Espaciais com o aplicativo e as compartilhou com outros dispositivos por meio de seu aplicativo Web do ASP.NET Core.

Você pode melhorar seu Aplicativo Web ASP.NET Core para que ele use o Azure Cosmos DB para persistir o armazenamento de seus identificadores de Âncora Espacial compartilhada. Adicionar suporte ao Azure Cosmos DB permitirá que seu Aplicativo Web ASP.NET Core crie uma âncora hoje mesmo. Volte alguns dias depois para poder localizá-lo novamente usando o identificador de âncora armazenado em seu aplicativo Web.

> [!div class="nextstepaction"]
> [Usar o Azure Cosmos DB para armazenar Âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)

