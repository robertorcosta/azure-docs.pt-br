---
title: Introdução aos Aplicativos Móveis do Serviço de Aplicativo do Azure para aplicativos Xamarin.iOS | Microsoft Docs
description: Siga este tutorial para começar a usar os Aplicativos Móveis para desenvolvimento do Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 14fb925896fc94def59b753711eb74f155b2e139
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388350"
---
# <a name="create-a-xamarinios-app"></a>Crie um aplicativo Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel Xamarin.iOS usando um back-end de Aplicativo Móvel do Azure.  Você cria um novo back-end do aplicativo móvel e um aplicativo Xamarin.iOS *Todo list* simples que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Caso você não tenha uma conta, inscreva-se para uma avaliação do Azure e obtenha até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio para Mac. Consulte [instalação e instalação para Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Um Mac com o Xcode 9,0 ou posterior.
  
## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end de aplicativo móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Executar o aplicativo Xamarin. iOS
1. Abra o projeto Xamarin. iOS.

2. Vá para a [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na folha `Overview`, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o sitename para o nome do meu aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `QSTodoService.cs` nesta pasta-xamarin. iOS/ZUMOAPPNAME. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na classe `QSTodoService`, substitua a variável `ZUMOAPPURL` pelo ponto de extremidade público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    ficará
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Pressione a tecla F5 para implantar e executar o aplicativo em um emulador do iPhone.

6. No aplicativo, digite um texto significativo, como *concluir o tutorial* e, em seguida, clique no botão +.

    ![][10]

    Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados aparecem na lista.

   > [!NOTE]
   > Você pode examinar o código que acessa o back-end do aplicativo móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png