---
title: Criar um daemon multilocatário que usa o ponto de extremidade da plataforma de identidade da Microsoft
description: Neste tutorial, saiba como chamar uma API Web do ASP.NET protegida pelo Azure Active Directory em um aplicativo de área de trabalho do Windows (WPF). O cliente do WPF autentica um usuário, solicita um token de acesso e chama a API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4b05bbf818676cc70f485dd94ece79141e8f01a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982855"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Tutorial: Criar um daemon multilocatário que usa o ponto de extremidade da plataforma de identidade da Microsoft

Neste tutorial, você aprenderá a usar a plataforma de identidade da Microsoft para acessar os dados de clientes empresariais da Microsoft em um processo de execução longa e não interativa. O daemon de exemplo usa a [concessão de credenciais de cliente OAuth2](v2-oauth2-client-creds-grant-flow.md) para adquirir um token de acesso. Em seguida, o daemon usa o token para chamar o [Microsoft Graph](https://graph.microsoft.io) e acessar dados organizacionais.

> [!div class="checklist"]
> * Integrar um aplicativo daemon à plataforma de identidade da Microsoft
> * Conceder permissões de aplicativo diretamente ao aplicativo por um administrador
> * Obter um token de acesso para chamar a API do Microsoft Graph
> * Chame a API do Microsoft Graph.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

O aplicativo é criado como um aplicativo MVC ASP.NET. Ele usa o middleware OWIN OpenID Connect para conectar usuários.

O componente "daemon" neste exemplo é um controlador de API, `SyncController.cs`. Quando o controlador é chamado, ele efetua pull em uma lista de usuários no Microsoft Graph no locatário do Azure AD (Azure Active Directory) do cliente. O `SyncController.cs` é disparado por uma chamada AJAX no aplicativo Web. Ele usa a [MSAL (biblioteca de autenticação da Microsoft) para .NET](msal-overview.md) para adquirir um token de acesso para o Microsoft Graph.

>[!NOTE]
> Se você é novo na plataforma de identidade da Microsoft, recomendamos começar com o [início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Cenário

Já que o aplicativo é um aplicativo multilocatário para clientes empresariais da Microsoft, ele deve fornecer uma maneira para que os clientes "inscrevam" ou "conectem" o aplicativo aos dados da empresa. Durante o fluxo de conexão, um administrador da empresa primeiro concede *permissões de aplicativo* diretamente ao aplicativo para que ele possa acessar os dados da empresa de maneira não interativa, sem a presença de um usuário conectado. A maior parte da lógica neste exemplo mostra como alcançar esse fluxo de conexão usando a plataforma de identidade de ponto de extremidade de [consentimento do administrador](v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

![O diagrama mostra o Aplicativo UserSync com três itens locais que se conectam ao Azure. O Start dot Auth adquire um token interativamente para se conectar ao Azure AD, o AccountController obtém o consentimento do administrador para se conectar ao Azure AD e o SyncController lê o usuário para se conectar ao Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos usados neste exemplo, leia a [documentação do protocolo de credenciais do cliente do ponto de extremidade da plataforma de identidade](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o exemplo presente neste início rápido, você precisará de:

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/).
- Um locatário do Azure AD. Para saber mais, consulte [Como obter um locatário do Azure AD](quickstart-create-new-tenant.md).
- Uma ou mais contas de usuário em seu locatário do Azure AD. Este exemplo não funcionará com uma conta Microsoft (anteriormente, conta do Windows Live). Se você tiver entrado no [portal do Azure](https://portal.azure.com) com uma conta Microsoft e nunca tiver criado uma conta de usuário em seu diretório, precisará fazer isso agora.

## <a name="clone-or-download-this-repository"></a>Clonar ou baixar este repositório

No shell ou na linha de comando, digite este comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [baixe o exemplo em um arquivo zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registre seu aplicativo

Este exemplo tem um projeto. Para registrar o aplicativo com seu locatário do Azure AD, você pode:

- Seguir as etapas em [Registrar o exemplo com seu locatário do Azure Active Directory](#register-your-application) e [Configurar o exemplo para usar o locatário do Azure AD](#choose-the-azure-ad-tenant).
- Usar scripts do PowerShell que:
  - Crie *automaticamente* os aplicativos do Azure AD e objetos relacionados (senhas, permissões, dependências) para você.
  - Modifiquem os arquivos de configuração dos projetos do Visual Studio.

Se você quiser usar a automação:

1. No Windows, execute o PowerShell e acesse a raiz do diretório clonado.
1. Execute este comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar o aplicativo do Azure AD e configure adequadamente o código do aplicativo de exemplo:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Outras maneiras de executar scripts são descritas em [Scripts de criação de aplicativo](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Abra a solução do Visual Studio e selecione **Iniciar** para executar o código.

Se você não quiser usar a automação, use as etapas nas seções a seguir.

### <a name="choose-the-azure-ad-tenant"></a>Escolher o locatário do Azure AD

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta estiver em mais de um locatário do Azure AD, selecione seu perfil no menu na parte superior da página e, em seguida, selecione **Alternar diretório**.
1. Altere a sessão do portal para o locatário do Azure AD desejado.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrar o aplicativo cliente (dotnet-web-daemon-v2)

1. Vá até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo. Por exemplo, digite **dotnet-web-daemon-v2**.
   - Na seção **Tipos de conta com suporte**, escolha **Contas em qualquer diretório organizacional**.
   - Na seção **URI de redirecionamento (opcional)** , selecione **Web** na caixa de combinação e insira os seguintes URIs de redirecionamento:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     Se houver mais de dois URIs de redirecionamento, você precisará adicioná-los por meio da guia **Autenticação** posteriormente, depois que o aplicativo tiver sido criado com êxito.
1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará dele para definir o arquivo de configuração do Visual Studio para este projeto.
1. Na lista de páginas para o aplicativo, selecione **Autenticação**. Em seguida:
   - Na seção **Configurações avançadas**, defina **URL de Logoff** como **https://localhost:44316/Account/EndSession** .
   - Na seção **Configurações avançadas** > **Concessão implícita**, selecione **Tokens de acesso** e **Tokens de ID**. Este exemplo requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário e chamar uma API.
1. Clique em **Salvar**.
1. Na página **Certificados e segredos**, na seção **Segredos do cliente**, selecione **Novo segredo do cliente**. Em seguida:

   1. Insira uma descrição de chave (por exemplo, **segredo do aplicativo**),
   1. Selecione uma duração de chave de **Em 1 ano**, **Em 2 anos** ou **Nunca Expira**.
   1. Selecione o botão **Adicionar**.
   1. Quando o valor da chave for exibido, copie e salve-o em um local seguro. Você precisará dessa chave posteriormente para configurar o projeto no Visual Studio. Ele não será exibido novamente nem poderá ser recuperado por outros meios.
1. Na lista de páginas do aplicativo, selecione **Permissões da API**. Em seguida:
   1. Selecione o botão **Adicionar uma permissão**.
   1. Verifique se a guia **APIs da Microsoft** está selecionada.
   1. Na seção **APIs da Microsoft frequentemente utilizadas**, selecione **Microsoft Graph**.
   1. Na seção **Permissões do aplicativo**, verifique se as permissões corretas estão selecionadas: **User.Read.All**.
   1. Selecione o botão **Adicionar permissões**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurar o exemplo para usar seu locatário do Azure AD

Nas etapas seguintes, **ClientID** é o mesmo que "ID do aplicativo" ou **AppId**.

Abrir a solução no Visual Studio para configurar os projetos.

### <a name="configure-the-client-project"></a>Configurar o projeto do cliente

Se você tiver usado os scripts de instalação, as alterações a seguir terão sido aplicadas para você.

1. Abra o arquivo **UserSync\Web.Config**.
1. Localize a chave do aplicativo **ida:ClientId**. Substitua o valor existente pela ID do aplicativo **dotnet-web-daemon-v2** copiado do portal do Azure.
1. Localize a chave do aplicativo **ida:ClientSecret**. Substitua o valor existente pela chave que você salvou durante a criação do aplicativo **dotnet-web-daemon-v2** no portal do Azure.

## <a name="run-the-sample"></a>Execute o exemplo

Limpe a solução, recompile-a, execute o aplicativo UserSync e entre como um administrador no locatário do Azure AD. Se você não tiver um locatário do Azure AD para teste, poderá [seguir estas instruções](quickstart-create-new-tenant.md) para obter um.

Quando você entrar, o aplicativo primeiro solicitará permissão para realizar esse processo e para ler seu perfil do usuário. Esse consentimento permite ao aplicativo ter a certeza de que você é um usuário empresarial.

![Consentimento do usuário](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Em seguida, o aplicativo tentará sincronizar uma lista de usuários do seu locatário do Azure AD por meio do Microsoft Graph. Se não for possível, ele solicitará que você (o administrador do locatário) conecte o locatário ao aplicativo.

O aplicativo pedirá então permissão para ler a lista de usuários em seu locatário.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Depois de conceder a permissão, você será desconectado do aplicativo. Essa saída garante que os tokens de acesso existentes do Microsoft Graph sejam removidos do cache de token. Quando você entrar novamente, o novo token obtido terá as permissões necessárias para fazer chamadas ao Microsoft Graph.


Quando você concede a permissão, o aplicativo pode consultar os usuários em qualquer momento. Você pode verificar isso selecionando o botão **Sincronizar Usuários** e atualizando a lista de usuários. Tente adicionar ou remover um usuário e ressincronizar a lista. (Mas observe que o aplicativo sincroniza apenas a primeira página de usuários).

## <a name="about-the-code"></a>Observações sobre o código

O código relevante para este exemplo está nos seguintes arquivos:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Conexão inicial. Em particular, as ações no controlador têm um atributo **Authorize**, que força o usuário a entrar. O aplicativo usa o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) para conectar o usuário.
- **Controllers\SyncController.cs**: Sincronização da lista de usuários para o repositório local na memória.
- **Controllers\UserController.cs**: Exibição da lista de usuários do repositório local na memória.
- **Controllers\AccountController.cs**: Aquisição de permissões do administrador de locatários usando o ponto de extremidade de consentimento do administrador.

## <a name="re-create-the-sample-app"></a>Recriar o aplicativo de exemplo

1. No Visual Studio, crie um projeto em **Visual C#** **aplicativo Web ASP .NET (.NET Framework)** .
1. Na próxima tela, escolha o modelo de projeto **MVC**. Além disso, adicione referências de pasta e núcleo para a **API Web**, pois você adicionará um controlador de API Web mais tarde. Deixe o modo de autenticação escolhido do projeto como o padrão: **Sem autenticação**.
1. Selecione o projeto na janela do **Gerenciador de Soluções** e selecione a tecla **F4**.
1. Nas propriedades do projeto, defina **Habilitado para SSL** como **True**. Observe as informações em **URL de SSL**. Você precisará dessas informações ao configurar o registro do aplicativo no portal do Azure.
1. Adicione os seguintes pacotes NuGet do middleware ASP.NET OWIN:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. Na pasta **App_Start**:
   1. Crie uma classe chamada **Startup.Auth.cs**.
   1. Remova **.App_Start** do nome do namespace.
   1. Substitua o código da classe **Startup** pelo código do mesmo arquivo do aplicativo de exemplo.
   Verifique se você obteve toda a definição de classe. A definição muda de **Inicialização de classe pública** para **Inicialização de classe parcial pública.**
1. Em **Startup.Auth.cs**, resolva as referências ausentes adicionando instruções **using** conforme sugerido pelo IntelliSense do Visual Studio.
1. Clique com o botão direito do mouse no projeto, selecione **Adicionar** e, em seguida, selecione **Classe**.
1. Na caixa de pesquisa, digite **OWIN**. A **Classe de inicialização OWIN** aparece como uma seleção. Selecione-a e nomeie a classe **Startup.cs**.
1. Em **Startup.cs**, substitua o código da classe **Startup** pelo código do mesmo arquivo do aplicativo de exemplo. Novamente, observe que a definição muda de **Inicialização de classe pública** para **Inicialização de classe parcial pública**.
1. Na pasta **Modelos**, adicione uma nova classe chamada **MsGraphUser.cs**. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
1. Adicione uma nova instância da **MVC 5 Controller – Vazia** chamada **AccountController**. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
1. Adicione uma nova instância da **MVC 5 Controller – Vazia** chamada **UserController**. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
1. Adicione uma nova instância da **API Web 2 Controller – Vazia** chamada **SyncController**. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
1. Para a interface do usuário, na pasta **Views\Account**, adicione três instâncias de **Exibições vazias (sem modelo)** chamadas **GrantPermissions**, **Index** e **UserMismatch**. Adicione a chamada **Index** na pasta **Views\User**. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
1. Atualize **Shared\_Layout.cshtml** e **Home\Index.cshtml** para vincular corretamente as várias exibições juntas.

## <a name="deploy-the-sample-to-azure"></a>Implantar o exemplo no Azure

Este projeto tem projetos de API Web e aplicativo Web. Para implantá-los em sites do Azure, execute as seguintes etapas para cada um:

1. Crie um site do Azure.
1. Publique o aplicativo Web e as APIs Web no site.
1. Atualize os clientes para chamarem o site em vez de IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Criar e publicar o dotnet-web-daemon-v2 em um site do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, selecione **Criar um recurso**.
1. Selecione **Web** > **Aplicativo Web** e dê um nome ao seu site. Por exemplo, nomeie-o **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selecione as informações para **Assinatura**, **Grupo de recursos** e **Plano do serviço de aplicativo e local**. O **SO** é **Windows** e a **Publicação** é **Código**.
1. Clique em **Criar** e aguarde até que o serviço de aplicativo seja criado.
1. Quando você obtiver a notificação de **Implantação bem-sucedida**, selecione **Ir para o recurso** para acessar o serviço de aplicativo recém-criado.
1. Depois que o site for criado, localize-o no **Painel** e selecione-o para abrir a tela **Visão geral** do serviço de aplicativo.
1. Na guia **Visão geral** do serviço de aplicativo, baixe o perfil de publicação selecionando o link **Obter perfil de publicação** e salve-o. Você pode usar outros mecanismos de implantação, como a implantação por meio de controle do código-fonte.
1. Alterne para o Visual Studio e, em seguida:
   1. Acesse o projeto **dotnet-web-daemon-v2**.
   1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar**.
   1. Selecione **Importar Perfil** na barra inferior e importe o perfil de publicação que você baixou anteriormente.
1. Selecione **Configurar**.
1. Na guia **Conexão**, atualize a URL de destino para que ela use "https". Por exemplo, use `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Selecione **Avançar**.
1. Na guia **Configurações**, verifique se **Habilitar Autenticação Organizacional** está desmarcada.
1. Clique em **Salvar**. Selecione **Publicar** na tela principal.

O Visual Studio publicará o projeto e abrirá automaticamente um navegador na URL do projeto. Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualizar o registro de aplicativo de locatário do Azure AD para dotnet-web-daemon-v2

1. Retorne ao [Portal do Azure](https://portal.azure.com).
1. No painel à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Selecione o aplicativo **dotnet-web-daemon-v2**.
1. Na página **Autenticação** do seu aplicativo, atualize os campos de **URL de Logout** com o endereço do seu serviço. Por exemplo, use `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. No menu de **Identidade Visual**, atualize a **URL da página inicial** para o endereço do serviço. Por exemplo, use `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Salve a configuração.
1. Adicione a mesma URL na lista de valores do menu **Autenticação** > **URIs de Redirecionamento**. Se você tiver várias URLs de redirecionamento, verifique se há uma nova entrada que usa o URI do serviço de aplicativo para cada URL de redirecionamento.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando não for mais necessário, exclua o objeto de aplicativo criado na etapa [Registrar seu aplicativo](#register-your-application).  Para remover o aplicativo, siga as instruções em [Remover um aplicativo criado por você ou sua organização](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Obter ajuda

Use o [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) para obter suporte da comunidade.
Faça suas perguntas primeiro no Stack Overflow e navegue pelos problemas existentes para ver se alguém já fez sua pergunta antes.
Verifique se suas perguntas ou comentários estão marcados com "adal," "msal" e "dotnet".

Se você encontrar um bug no exemplo, crie um registro do problema em [Problemas do GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se você encontrar um bug no MSAL.NET, crie um registro do problema em [Problemas do GitHub no MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, acesse a [página Voz do Usuário](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) aos quais a plataforma de identidade da Microsoft dá suporte.

Para obter mais informações, confira a seguinte documentação conceitual:

- [Locatários no Azure Active Directory](single-and-multi-tenant-apps.md)
- [Noções básicas sobre experiências de consentimento de aplicativo do Azure AD](application-consent-experience.md)
- [Entrar qualquer usuário do Azure Active Directory usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md)
- [Entenda o consentimento do usuário e do administrador](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objetos de entidade de serviço e aplicativo no Azure Active Directory](app-objects-and-service-principals.md)
- [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)
- [Início Rápido: Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
- [Adquirir um token para um aplicativo com fluxos de credencial do cliente](msal-client-applications.md)

Para um aplicativo do daemon de console multilocatário mais simples, veja o [Início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).
