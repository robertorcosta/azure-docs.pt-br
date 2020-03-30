---
title: Introdução ao Xamarin.Forms
description: Siga este tutorial para começar a usar os Aplicativos Móveis para desenvolvimento do Xamarin.Forms
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461327"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Criar um aplicativo Xamarin.Forms com Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel do Xamarin.Forms usando o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure como back-end. Você cria um novo back-end do Aplicativo Móvel e um aplicativo de lista de tarefas pendentes Xamarin.Forms que armazena dados do aplicativo no Azure.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para o Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para saber mais, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio Tools para Xamarin, no Visual Studio 2017 ou posterior, ou Visual Studio para Mac. Consulte a [página de instalação do Xamarin][Install Xamarin] para obter instruções.

* (opcional) Para compilar um aplicativo para iOS é necessário um Mac com Xcode 9.0 ou posterior. Visual Studio para Mac pode ser usado para desenvolver aplicativos para iOS, ou Visual Studio 2017 ou posterior pode ser usado (desde que o Mac esteja disponível na rede).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicativos Móveis
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Crie uma conexão de banco de dados e configure o projeto cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Execute a solução Xamarin.Forms

As Ferramentas do Visual Studio para Xamarin são necessárias para abrir a solução, consulte as [instruções de instalação do Xamarin][Install Xamarin]. Se as ferramentas já estiverem instaladas, execute estas etapas para baixar e abrir a solução:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows e Mac)

1. Acesse o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na `Overview` lâmina, procure a URL que é o ponto final público para o seu aplicativo móvel. Exemplo - o nome do site do meu nome https://test123.azurewebsites.netde aplicativo "test123" será .

2. Abra o `Constants.cs` arquivo nesta pasta - xamarin.forms/ZUMOAPPNAME. O nome do app é `ZUMOAPPNAME`.

3. Em `Constants.cs` classe, `ZUMOAPPURL` substitua a variável por ponto final público acima.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    se torna

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Siga as instruções abaixo para executar os projetos Android ou Windows e, se houver um computador Mac em rede disponível, o projeto iOS.

## <a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto do Android

Nesta seção, será executado o projeto Xamarin.Android. Você poderá ignorá-la se não estiver trabalhando com dispositivos Android.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto do Android (Droid) e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto Android e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo em um emulador Android, pressione a tecla **F5** ou clique no botão **Iniciar**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android e selecione **Definir Como Projeto de Inicialização**.

2. Para compilar o projeto e iniciar o aplicativo em um emulador Android, selecione o menu **Executar** e, em seguida, **Iniciar Depuração**.

No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

![Aplicativo de tarefa pendente do Android][11]

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

> [!NOTE]
> O código que acessa o back-end dos Aplicativos Móveis está no arquivo C# **TodoItemManager.cs** do projeto de código compartilhado na solução.
>

## <a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto do iOS

Nesta seção, você executará o projeto Xamarin.iOS para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto iOS e selecione **Set as StartUp Project**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto iOS e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo no emulador do iPhone, selecione a tecla **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir Como Projeto de Inicialização**.

2. No menu **Executar**, selecione **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

![Aplicativo de tarefa pendente do iOS][10]

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

> [!NOTE]
> Você localizará o código que acessa o back-end do Aplicativo Móvel no arquivo C# **TodoItemManager.cs** do projeto de código compartilhado na solução.
>

## <a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto do Windows

Nesta seção, você executará o projeto Xamarin.Forms da UWP (Plataforma Universal do Windows) para dispositivos Windows. Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse em qualquer projeto UWP e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto Windows que você escolheu e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo em um emulador do Windows, pressione a tecla **F5** ou clique no botão **Iniciar** (que deverá ler **Computador Local**).

> [!NOTE]
> O projeto do Windows não pode ser executado no macOS.

No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

![Aplicativo de tarefa pendente da UWP][12]

> [!NOTE]
> Você encontrará o código que acessa o back-end dos Aplicativos Móveis no arquivo C# **TodoItemManager.cs** do projeto de biblioteca de classes portátil da sua solução.
>

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas para compilar a solução, execute o gerenciador de pacotes NuGet e atualize para a última versão do `Xamarin.Forms` e, no projeto Android, atualize os pacotes de suporte `Xamarin.Android`. Os projetos de Início Rápido nem sempre incluem as últimas versões.

Observe que todos os pacotes de suporte referenciados em seu projeto Android devem ter a mesma versão. O [pacote NuGet de Aplicativos Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) depende de `Xamarin.Android.Support.CustomTabs` para a plataforma Android e, portanto, se o projeto usar pacotes de suporte mais recentes, será necessário instalar diretamente esse pacote com a versão necessária para evitar conflitos.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
