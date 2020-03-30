---
title: incluir arquivo
description: incluir arquivo
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440214"
---
1. Baixe o cliente SDK quickstarts para as seguintes plataformas:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Janelas (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se você usar o projeto iOS, você precisa baixar "azuresdk-iOS-\*.zip" da versão mais recente do [GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Descompacte `MicrosoftAzureMobile.framework` e adicione o arquivo à raiz do projeto.
    >

2. Você terá que adicionar uma conexão de banco de dados ou se conectar a uma conexão existente. Primeiro, determine se você criará um armazenamento de dados ou usará um já existente.

    - **Criar um novo armazenamento de dados**: Se você vai criar um armazenamento de dados, use o seguinte quickstart:

        [Quickstart: Começando com bancos de dados únicos no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Fonte de dados existente**: Siga as instruções abaixo se você quiser usar uma conexão de banco de dados existente

        1. Formato de string de conexão de banco de dados SQL -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Nome do servidor, isso pode ser encontrado na página de visão geral do seu banco de dados e geralmente é na forma de "server_name.database.windows.net".
            **{porta}** geralmente 1433.
            **{your_catalogue}** Nome do banco de dados.
            **{your_username}** Nome de usuário para acessar seu banco de dados.
            **{your_password}** Senha para acessar seu banco de dados.

            [Saiba mais sobre o formato sql connection string](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adicione a seqüência de conexões ao seu **aplicativo móvel** No Serviço de Aplicativos, você pode gerenciar strings de conexão para o seu aplicativo usando a opção **Configuração** no menu.

            Para adicionar uma seqüência de conexões:

            1. Clique na **guia Configurações** do aplicativo.

            2. Clique em **[+] Nova seqüência de conexão**.

            3. Você precisará fornecer **Nome,** **Valor** e **Tipo** para sua seqüência de conexão.

            4. **Nome do tipo** como`MS_TableConnectionString`

            5. O valor deve ser a seqüência de conexão que você formou na etapa anterior.

            6. Se você estiver adicionando uma seqüência de conexão a um banco de dados SQL Azure, escolha **o SQLAzure** **o tipo**.

3. O Azure Mobile Apps tem SDKs para backends .NET e Node.js.

   - **Back-end do Node.js**
    
     Se você vai usar o aplicativo Node.js quickstart, siga as instruções abaixo.

     1. No portal Azure, vá para **Easy Tables**, você verá esta tela.
      
        ![Tabelas fáceis do nó](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Certifique-se de que a seqüência de conexão SQL já está adicionada na guia **Configuração.** Em seguida, verifique a caixa de **eu reconheço que isso irá substituir todo** o conteúdo do site e clicar no botão Criar **todoitem tabela.**
     
        ![Configuração de tabelas fáceis do nó](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Em **Tabelas Fáceis,** clique no botão **+ Adicionar.**
    
        ![Tabelas fáceis do nó adicionar botão](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Crie `TodoItem` uma tabela com acesso anônimo.
      
        ![Tabelas fáceis de nó adicionar tabela](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Back-end do .NET**
    
        Se você vai usar o aplicativo .NET quickstart, siga as instruções abaixo.

        1. Baixe o projeto de servidor Azure Mobile Apps .NET do [repositório azure-mobile-apps quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Construa o projeto do servidor .NET localmente no Visual Studio.

        3. No Visual Studio, abra o Solution `ZUMOAPPNAMEService` Explorer, clique com o `Publish to App Service` botão direito do mouse no projeto, clique em **Publicar,** você verá uma janela. Se você está trabalhando no Mac, confira outras maneiras de implantar o aplicativo [aqui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publicação de estúdio visual](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selecione **O Serviço de Aplicativo** como destino de publicação e clique em Selecionar **Existir**e clique no botão **Publicar** na parte inferior da janela.

        5. Você precisará entrar no Visual Studio com sua assinatura do Azure primeiro. Selecione `Subscription` `Resource Group`o e selecione o nome do seu aplicativo. Quando estiver pronto, clique em **OK,** isso implantará o projeto de servidor .NET que você tem localmente no backend do Serviço de Aplicativos. Quando a implantação terminar, você `http://{zumoappname}.azurewebsites.net/` será redirecionado para o navegador.                   