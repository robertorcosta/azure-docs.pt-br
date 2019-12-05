---
title: Chamar uma ASP.NET Web API protegida pelo Azure AD – plataforma de identidade da Microsoft
description: Neste início rápido, saiba como chamar uma ASP.NET Web API protegida pelo Azure Active Directory de um aplicativo de Área de Trabalho do Windows (WPF). O cliente do WPF autentica um usuário, solicita um token de acesso e chama a API Web.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328545"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Criar um daemon multilocatário com o ponto de extremidade da plataforma Microsoft Identity

Neste tutorial, você aprenderá a usar a plataforma de identidade da Microsoft para acessar os dados de clientes empresariais da Microsoft em um processo de execução longa e não interativa. O daemon de exemplo usa [concessão de credenciais de cliente OAuth2](v2-oauth2-client-creds-grant-flow.md) para adquirir um token de acesso, que ele usa para chamar o [Microsoft Graph](https://graph.microsoft.io) e acessar dados organizacionais.

O aplicativo é criado como um aplicativo MVC ASP.NET e usa o middleware OWIN OpenID Connect para conectar os usuários.  O componente "daemon" desse aplicativo usado neste exemplo é um controlador de API que, quando chamado, efetua pull em uma lista de usuários no locatário do Azure AD do cliente por meio do Microsoft Graph.  Esse `SyncController.cs` é disparado por uma chamada AJAX no aplicativo Web e usa a [MSAL (biblioteca de autenticação da Microsoft) para .NET](msal-overview.md) para adquirir um token de acesso para o Microsoft Graph.

Para um aplicativo de daemon de console mais simples, confira o [Início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Cenário

Já que o aplicativo é um aplicativo multilocatário destinado para uso por qualquer cliente empresarial da Microsoft, ele deve fornecer uma maneira para que os clientes "se inscrevam" ou "conectem" o aplicativo aos dados da empresa.  Durante o fluxo de conexão, um administrador da empresa primeiro concede **permissões de aplicativo** diretamente ao aplicativo para que ele possa acessar os dados da empresa de maneira não interativa, sem a presença de um usuário conectado.  A maior parte da lógica neste exemplo mostra como alcançar esse fluxo de conexão usando a plataforma de identidade de ponto de extremidade de [consentimento do administrador](v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos usados neste exemplo, não deixe de ler a [documentação do protocolo de credenciais do cliente do ponto de extremidade da plataforma de identidade](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o exemplo presente neste início rápido, você precisará de:

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/)
- Um locatário do Azure AD (Azure Active Directory). Para saber mais sobre como obter um locatário do Azure AD, confira [Como obter um locatário do Azure AD](quickstart-create-new-tenant.md)
- Uma ou mais contas de usuário em seu locatário do Azure AD. Este exemplo não funcionará com uma conta Microsoft (anteriormente, conta do Windows Live). Portanto, se você tiver entrado no [portal do Azure](https://portal.azure.com) com uma conta Microsoft e nunca tiver criado uma conta de usuário em seu diretório anteriormente, precisará fazer isso agora.

## <a name="clone-or-download-this-repository"></a>Clonar ou baixar este repositório

No shell ou na linha de comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [baixar o exemplo em um arquivo ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Registrar o aplicativo de exemplo com seu locatário do Azure AD

Há um projeto neste exemplo. Para registrá-lo, você pode escolher uma das opções a seguir:

- Seguir as etapas em referências: [Registrar o exemplo com seu locatário do Azure Active Directory](#register-the-sample-application-with-your-azure-ad-tenant) e [Configurar o exemplo para usar o seu locatário do Azure AD](#choose-the-azure-ad-tenant-for-the-applications)
- Ou usar scripts do PowerShell que:
  - Criem **automaticamente** os aplicativos do Azure AD e objetos relacionados (senhas, permissões, dependências) para você
  - Modifiquem os arquivos de configuração dos projetos do Visual Studio.

Se você quiser usar essa automação:

1. No Windows, execute o PowerShell e navegue até a raiz do diretório clonado
1. Na execução do PowerShell:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar seu aplicativo do Azure AD e configure adequadamente o código do aplicativo de exemplo.
1. Na execução do PowerShell:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Outras maneiras de executar os scripts são descritas em [Scripts de Criação de Aplicativo](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Abra a solução do Visual Studio e clique em Iniciar para executar o código.

Se você não quiser usar essa automação, siga as etapas abaixo.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Escolher o locatário do Azure AD para os aplicativos

Como primeira etapa:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD, selecione seu perfil no canto superior direito no menu na parte superior da página e, em seguida, **alternar diretório**.
   Altere a sessão do portal para o locatário do Azure AD desejado.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrar o aplicativo cliente (dotnet-web-daemon-v2)

1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `dotnet-web-daemon-v2`.
   - Na seção **Tipos de conta com suporte**, escolha **Contas em qualquer diretório organizacional**.
   - Na seção Redirecionamento de URIs (opcional), selecione **Web** na caixa de combinação e insira os seguintes URIs de redirecionamento:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` Se houver mais de um URI de redirecionamento, você precisará adicioná-los da guia **Autenticação** posteriormente depois que o aplicativo tiver sido criado com êxito.
1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará dele para definir o arquivo de configuração do Visual Studio para este projeto.
1. Na lista de páginas para o aplicativo, selecione **Autenticação**.
   - Na seção **Configurações avançadas**, defina **URL de Saída** como `https://localhost:44316/Account/EndSession`
   - Na seção **Configurações avançadas** | **Concessão implícita**, marque **Tokens de acesso** e **Tokens de ID**, pois este exemplo requer que o [Fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário e chamar uma API.
1. Clique em **Salvar**.
1. Na página **Certificados e segredos**, na seção **Segredos do cliente**, escolha **Novo segredo do cliente**:

   - Digite uma descrição de chave (do `app secret` da instância),
   - Selecione uma duração de chave de **Em 1 ano**, **Em 2 anos** ou **Nunca Expira**.
   - Quando você pressionar o botão **Adicionar**, o valor da chave será exibido. Copie-o e salve-o em uma localização segura.
   - Você precisará dessa chave posteriormente para configurar o projeto no Visual Studio. Esse valor de chave não será exibido novamente nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.
1. Na lista de páginas para o aplicativo, selecione **Permissões da API**
   - Clique no botão **Adicionar uma permissão** e, em seguida,
   - Verifique se a guia **APIs da Microsoft** está selecionada
   - Na seção *APIs da Microsoft frequentemente utilizadas*, clique em **Microsoft Graph**
   - Na seção **Permissões do aplicativo**, verifique se as permissões corretas estão marcadas: **User.Read.All**
   - Selecione o botão **Adicionar permissões**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurar o exemplo para usar seu locatário do Azure AD

Nas etapas a seguir, "ClientID" é o mesmo que "ID do Aplicativo" ou "AppId".

Abrir a solução no Visual Studio para configurar os projetos

### <a name="configure-the-client-project"></a>Configurar o projeto do cliente

Se você tiver usado os scripts de instalação, as alterações a seguir terão sido aplicadas para você.

1. Abra o arquivo `UserSync\Web.Config`
1. Localize a chave do aplicativo `ida:ClientId` e substitua o valor existente pela ID do aplicativo (clientId) do aplicativo `dotnet-web-daemon-v2` copiado do portal do Azure.
1. Localize a chave do aplicativo `ida:ClientSecret` e substitua o valor existente pela chave que você salvou durante a criação do aplicativo `dotnet-web-daemon-v2` no portal do Azure.

## <a name="run-the-sample"></a>Execute o exemplo

Limpe a solução, recompile-a, execute o aplicativo UserSync e comece entrando como um administrador em seu locatário do Azure AD.  Se você não tiver um locatário do Azure AD para teste, poderá [seguir estas instruções](quickstart-create-new-tenant.md) para obter um.

Quando você entrar, o aplicativo primeiro solicitará permissão para realizar esse processo e para ler seu perfil do usuário.  Com esse consentimento, o aplicativo tem a certeza de que você é um usuário empresarial.

![Consentimento do usuário](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Em seguida, o aplicativo tentará sincronizar uma lista de usuários do seu locatário do Azure AD por meio do Microsoft Graph.  Se não for possível fazer isso, ele solicitará que você (o administrador do locatário) conecte o seu locatário ao aplicativo.

O aplicativo pedirá então permissão para ler a lista de usuários em seu locatário.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Você será desconectado do aplicativo depois de conceder a permissão**. Isso é feito para garantir que os tokens de acesso existentes para o Graph sejam removidos do cache de tokens. Depois de entrar novamente, o novo token obtido terá as permissões necessárias para fazer chamadas ao MS Graph.
Quando você conceder a permissão, o aplicativo poderá fazer consultas por usuários em qualquer momento.  Você pode verificar isso clicando no botão **Sincronizar Usuários** na página usuários, atualizando a lista de usuários.  Tente adicionar ou remover um usuário e ressincronizar a lista (mas observe que ela apenas sincroniza a primeira página de usuários!).

## <a name="about-the-code"></a>Observações sobre o código

O código relevante para este exemplo está nos seguintes arquivos:

- Conexão inicial: `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  Em particular, as ações no controlador têm um atributo Authorize, que força o usuário a entrar. O aplicativo usa o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) para conectar o usuário.
- Sincronização da lista de usuários para o repositório local na memória: `Controllers\SyncController.cs`
- Exibição da lista de usuários do repositório local na memória: `Controllers\UserController.cs`
- Aquisição de permissões do administrador de locatários usando o ponto de extremidade de consentimento do administrador: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Recriar este aplicativo de exemplo

1. No Visual Studio, crie um projeto de `ASP.NET Web Application (.NET Framework)` do `Visual C#`. Na próxima tela, escolha o modelo de projeto `MVC`. Além disso, adicione referências de pasta e de núcleo para `Web API`, já que você adicionaria um controlador de API Web mais tarde.  Deixe o modo de autenticação escolhido do projeto como o padrão, ou seja, `No Authentication`.
2. Selecione o projeto na janela **Gerenciador de Soluções** e pressione a tecla **F4** para trazer as propriedades do projeto. Nas propriedades do projeto, defina **SSL Habilitado** para `True`. Anote a **URL do SSL**. Você precisará dela ao configurar o registro desse aplicativo no portal do Azure.
3. Adicione os seguintes NuGets de middleware ASP.NET OWIN: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` e `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` e `Microsoft.Identity.Client`. 
4. Na pasta `App_Start`, crie uma classe `Startup.Auth.cs`. Remova `.App_Start` do nome do namespace.  Substitua o código da classe `Startup` pelo código do mesmo arquivo do aplicativo de exemplo.  Verifique se você obteve toda a definição de classe!  A definição é alterada de `public class Startup` para `public partial class Startup`
5. Em `Startup.Auth.cs`, resolva as referências ausentes adicionando instruções `using` conforme sugerido pelo Visual Studio IntelliSense.
6. Clique com o botão direito do mouse no projeto, selecione Adicionar, selecione "classe" e, na caixa de pesquisa, digite "OWIN".  A "classe de inicialização OWIN" será exibida como uma seleção; selecione-a e nomeie a classe `Startup.cs`.
7. Em `Startup.cs`, substitua o código da classe `Startup` pelo código do mesmo arquivo do aplicativo de exemplo.  Novamente, observe que as alterações de definição de `public class Startup` para `public partial class Startup`.
8. Na pasta, adicione uma nova classe chamada `MsGraphUser.cs`.  Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
9. Adicione um novo **Controlador MVC 5 – Vazio** chamado `AccountController`. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
10. Adicione um novo **Controlador MVC 5 – Vazio** chamado `UserController`. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
11. Adicione um novo **Controlador de API Web 2 – Vazio** chamado `SyncController`. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
12. Para a interface do usuário, na pasta `Views\Account`, adicione três **Exibições Vazias (sem modelo)** nomeadas `GrantPermissions`, `Index` e `UserMismatch` e uma nomeada `Index` na pasta `Views\User`. Substitua a implementação pelo conteúdo do arquivo com o mesmo nome do exemplo.
13. Atualize o `Shared\_Layout.cshtml` e o `Home\Index.cshtml` para vincular juntas as diversas exibições.

## <a name="deploy-this-sample-to-azure"></a>Implantar este exemplo no Azure

Este projeto tem projetos de API Web/aplicativo Web. Para implantá-los nos sites do Azure, você precisará, para cada um:

- criar um site do Azure;
- publicar o aplicativo Web/APIs Web no site da Web e
- atualizar os respectivos clientes para chamar o site da Web em vez de IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Criar e publicar o `dotnet-web-daemon-v2` em um site do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em `Create a resource` no canto superior esquerdo, selecione **Web** --> **Aplicativo Web** e dê um nome ao seu site da Web, por exemplo, `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Depois disso, selecione a `Subscription`, o `Resource Group` e o `App service plan and Location`. O `OS` será **Windows** e o `Publish` será **Código**.
1. Clique em `Create` e aguarde até que o Serviço de Aplicativo seja criado.
1. Depois de obter a notificação de `Deployment succeeded`, clique em `Go to resource` para navegar até o Serviço de Aplicativo recém-criado.
1. Depois que o site for criado, localize-o no **Painel** e clique nele para abrir a tela **Serviços de Aplicativos** **Visão geral**.
1. Na guia **Visão geral** do Serviço de Aplicativo, baixe o perfil de publicação clicando no link **Obter perfil de publicação** e salve-o.  Outros mecanismos de implantação, tais como por meio de controle do código-fonte, também podem ser usados.
1. Mude para o Visual Studio e vá para o projeto dotnet-web-daemon-v2.  Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar**.  Clique em **Importar Perfil** na barra inferior e importe o perfil de publicação que você baixou anteriormente.
1. Clique em **Configurar** e, na `Connection tab`, atualize a URL de destino para que ela seja uma `https` na URL da página inicial, por exemplo, [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Clique em **Próximo**.
1. Na guia Configurações, certifique-se de que a opção `Enable Organizational Authentication` não esteja selecionada.  Clique em **Save** (Salvar). Clique em **Publicar** na tela principal.
1. O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto.  Se você vir a página da Web padrão do projeto, a publicação terá sido bem-sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualize o registro de aplicativo de locatário do Azure AD para `dotnet-web-daemon-v2`

1. Navegue até o [Portal do Azure](https://portal.azure.com).
No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Na tela resultante, selecione o aplicativo `dotnet-web-daemon-v2`.
1. Na página **Autenticação** | do seu aplicativo, atualize os campos de URL de Logout com o endereço do seu serviço, por exemplo, [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. No menu de *Identidade Visual*, atualize a **URL da página inicial** para o endereço do serviço, por exemplo, [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Salve a configuração.
1. Adicione a mesma URL na lista de valores do menu *Autenticação-> URIs de Redirecionamento*. Se você tiver várias URLs de redirecionamento, verifique se há uma nova entrada usando o URI do serviço de aplicativo para cada URL de redirecionamento.

## <a name="community-help-and-support"></a>Ajuda e suporte da comunidade

Use o [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) para obter suporte da comunidade.
Faça suas perguntas no Stack Overflow primeiro e navegar pelos problemas existentes para ver se alguém já fez sua pergunta antes.
Verifique se suas perguntas ou comentários estão marcados com [`adal` `msal` `dotnet`].

Se você encontrar um bug no exemplo, crie um registro do problema em [Problemas do GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se você encontrar um bug no MSAL.NET, crie um registro do problema em [Problemas do GitHub no MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, visite a seguinte [página da Voz do Usuário](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) aos quais a plataforma de identidade da Microsoft dá suporte.

Para obter mais informações, confira a seguinte documentação conceitual:

- [Locatários no Azure Active Directory](single-and-multi-tenant-apps.md)
- [Noções básicas sobre experiências de consentimento de aplicativo do Azure AD](application-consent-experience.md)
- [Como: Entrar em qualquer usuário do Azure Active Directory usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md)
- [Entenda o consentimento do usuário e do administrador](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objetos de entidade de serviço e aplicativo no Azure Active Directory](app-objects-and-service-principals.md)
- [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)
- [Início Rápido: Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
- [Adquirir um token para um aplicativo com fluxos de credencial do cliente](msal-client-applications.md)

Para um aplicativo de daemon de console multilocatário mais simples, confira o [Início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).
