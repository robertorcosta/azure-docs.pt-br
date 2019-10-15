---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837389"
---
Para criar um projeto do Visual Studio para desenvolvimento de aplicativo móvel multiplataforma com .NET com o Xamarin, você precisa configurar as opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino e instalar o SDK de Fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar as opções de desenvolvimento do Visual Studio

Para começar, verifique se o Visual Studio está configurado corretamente para o desenvolvimento móvel multiplataforma com .NET:

1. Abra o Visual Studio 2019.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter ferramentas e recursos** para abrir o Instalador do Visual Studio e exibir a caixa de diálogo **Modificação**.

   ![Guia Cargas de trabalho, caixa de diálogo Modificação, Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na guia **Cargas de trabalho**, em **Windows**, encontre a carga de trabalho **Desenvolvimento móvel com .NET**. Se a caixa de seleção ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **Modificação** e vá para a etapa 5.

1. Selecione a caixa de seleção **Desenvolvimento Móvel com .NET**, selecione **Modificar** e, em seguida, na caixa de diálogo **Antes de começar**, selecione **Continuar** para instalar a carga de trabalho de Desenvolvimento Móvel com .NET. A instalação do novo recurso pode levar algum tempo.

1. Feche o Instalador do Visual Studio.

### <a name="create-the-project"></a>Criar o projeto

1. Na barra de menus do Visual Studio, escolha **Arquivo** > **Novo** > **Projeto** para exibir a janela **Criar projeto**.

   ![Criar projeto – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Localize e selecione **Aplicativo móvel (Xamarin Forms)** .

1. Selecione **Avançar** para exibir a tela **Configurar seu novo projeto**. 

   ![Configurar seu novo projeto – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Em **Nome do projeto**, insira `helloworld`.

1. Em **Localização**, navegue até a pasta em que deseja salvar o projeto e selecione-a ou, caso ainda não exista, crie-a.

1. Selecione **Criar** para ir para a janela **Novo Projeto de Aplicativo Móvel do Xamarin Forms**.

   ![Nova caixa de diálogo do Projeto da Plataforma Universal do Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecione modelo **Em branco**

1. Em **Plataforma**, marque as caixas para **Android**, **iOS** e **Windows (UWP)** .

1. Selecione **OK**. Você será direcionado de volta ao IDE do Visual Studio, com o novo projeto criado e visível no painel do **Gerenciador de Soluções**.

   ![Projeto Olá Mundo – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Agora, selecione a arquitetura da plataforma de destino e o projeto de inicialização. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **Plataformas de Solução**. (Caso não a veja, escolha **Exibir** > **Barras de Ferramentas** > **Padrão** para exibir a barra de ferramentas que contém a caixa **Plataformas de Solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. O Windows de 64 bits também pode executar aplicativos de 32 bits. Portanto, você poderá escolher **x86**, se preferir. Para a caixa de lista suspensa **Projetos de Inicialização**, defina helloworld.UWP (Windows Universal).

### <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Instale o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e referencie o SDK de Fala no seu projeto:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet para Solução** a fim de acessar a janela **NuGet – Solução**.

1. Selecione **Procurar**.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Em **Origem do pacote**, escolha **nuget.org**.

1. Na caixa **Pesquisar**, insira `Microsoft.CognitiveServices.Speech` e escolha o pacote depois que ele aparecer nos resultados da pesquisa.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Observação: a biblioteca do iOS dentro do NuGet Microsoft.CognitiveServices.Speech não tem bitcode habilitado. Caso você precise de uma biblioteca habilitada para bitcode para o aplicativo, use o NuGet Microsoft.CognitiveServices.Speech.Xamarin.iOS para o projeto do iOS especificamente.

1. No painel de status do pacote, ao lado dos resultados da pesquisa, selecione todos os projetos. **helloworld**, **helloworld.Android**, **helloworld.iOS** e **helloworld.UWP**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar Alterações**, selecione **OK**.

1. Na caixa de diálogo **Aceitação de Licença**, exiba a licença e, em seguida, selecione **Eu Aceito** e instale a referência de pacote do SDK de fala para todos os projetos. Depois que a instalação for concluída com êxito, você poderá ver o aviso a seguir para helloworld.iOS. Esse é um problema conhecido e não deve afetar a funcionalidade do aplicativo.

> Não foi possível resolver a referência "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se essa referência for exigida pelo seu código, você poderá obter erros de compilação.
