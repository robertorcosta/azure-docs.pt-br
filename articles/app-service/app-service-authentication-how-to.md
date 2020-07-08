---
title: Uso avançado de Authn/autho
description: Saiba como personalizar o recurso de autenticação e autorização no serviço de aplicativo para diferentes cenários e obter declarações de usuário e tokens diferentes.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6efa5461fab9faf3ce1599a01540cf314b34281b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85205638"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avançado de autenticação e autorização no Serviço de Aplicativo do Azure

Este artigo mostra como personalizar a [autenticação e a autorização integradas no Serviço de Aplicativo](overview-authentication-authorization.md) e gerenciar a identidade do seu aplicativo. 

Para começar rapidamente, veja um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure para Linux](containers/tutorial-auth-aad.md)
* [Como configurar seu aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar seu aplicativo para usar o logon do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar seu aplicativo para usar o logon do Google](configure-authentication-provider-google.md)
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar seu aplicativo para usar o logon do Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Usar vários provedores de entrada

A configuração do portal não oferece uma maneira prática turnkey para apresentar vários provedores de entrada aos usuários (como o Facebook e o Twitter). No entanto, não é difícil adicionar a funcionalidade ao aplicativo. As etapas são destacadas como a seguir:

Primeiro, na página **Autenticação/Autorização** no Portal do Azure, configure cada provedor de identidade que você deseja habilitar.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Permitir solicitações anônimas (nenhuma ação)**.

Na página de entrada, na barra de navegação, ou em qualquer outro local do aplicativo, adicione um link de entrada a cada um dos provedores que você habilitou (`/.auth/login/<provider>`). Por exemplo:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o usuário clica em um dos links, a respectiva página de entrada é aberta para que ele entre.

Para redirecionar o usuário pós-entada para uma URL personalizada, use o parâmetro de cadeia de caracteres de consulta `post_login_redirect_url` (não deve ser confundido com o URI de redirecionamento na configuração do provedor de identidade). Por exemplo, para orientar o usuário para `/Home/Index` após entrar, use o seguinte código HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar os tokens de provedores

Em um login direcionado ao cliente, o aplicativo faz login manual do usuário no provedor e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação (consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow)). Essa validação em si não concede a você acesso aos recursos desejados do aplicativo, mas uma validação bem-sucedida fornecerá um token de sessão que você pode usar para acessar os recursos do aplicativo. 

Para validar o token do provedor, o aplicativo Serviço de Aplicativo deve ser configurado primeiro com o provedor desejado. Em runtime, depois de recuperar o token de autenticação do seu provedor, poste o token em `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato do token varia ligeiramente de acordo com o provedor. Consulte a tabela a seguir para obter detalhes:

| Valor do provedor | Necessário no corpo da solicitação | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A propriedade `expires_in` é opcional. <br/>Ao solicitar o token de serviços em tempo real, sempre solicitar o `wl.basic` escopo. |
| `google` | `{"id_token":"<id_token>"}` | A propriedade `authorization_code` é opcional. Quando especificado, ele também pode ser acompanhado pelo `redirect_uri` propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Use um válido [token de acesso do usuário](https://developers.facebook.com/docs/facebook-login/access-tokens) do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do provedor for validado com êxito, a API retorna com um `authenticationToken` no corpo da resposta, que é seu token de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Uma vez que esse token de sessão, você pode acessar os recursos de aplicativo protegido, adicionando o `X-ZUMO-AUTH` cabeçalho às solicitações HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Sair de uma sessão

Os usuários podem iniciar uma saída, enviando uma `GET` solicitação ao ponto de extremidade `/.auth/logout` do aplicativo. A solicitação `GET` faz o seguinte:

- Limpa os cookies de autenticação da sessão atual.
- Exclui os tokens do usuário atual do Token Store.
- Para o Azure Active Directory e o Google, executa uma saída do servidor no provedor de identidade.

Aqui está um link de saída simples em uma página da Web:

```html
<a href="/.auth/logout">Sign out</a>
```

Por padrão, uma saída com êxito redireciona o cliente para a URL `/.auth/logout/done`. É possível alterar a página de redirecionamento pós-saída, adicionando o parâmetro de consulta `post_logout_redirect_uri`. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

É recomendável [codificar](https://wikipedia.org/wiki/Percent-encoding) o valor de `post_logout_redirect_uri`.

Ao usar URLs totalmente qualificadas, a URL deve ser hospedada no mesmo domínio ou configurada como uma URL de redirecionamento externo permitido para o aplicativo. No exemplo a seguir, para redirecionar para `https://myexternalurl.com` que não está hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Execute o seguinte comando no [Azure cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois que os usuários entram no aplicativo, geralmente querem ser redirecionados para a mesma seção da mesma página, como `/wiki/Main_Page#SectionZ`. No entanto, como os [fragmentos de URL](https://wikipedia.org/wiki/Fragment_identifier) (por exemplo, `#SectionZ`) nunca são enviados ao servidor, consequentemente não são preservados por padrão depois que a assinatura OAuth é concluída e redirecionada de volta ao aplicativo. Portanto, os usuários obtêm uma experiência abaixo do ideal quando precisam navegar novamente para a âncora desejada. Essa limitação aplica-se a todas as soluções de autenticação do lado do servidor.

Na autenticação do Serviço de Aplicativo, é possível preservar os fragmentos de URL na assinatura OAuth. Para fazer isso, defina uma configuração de aplicativo chamada `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` para `true`. Você pode fazer isso no [portal do Azure](https://portal.azure.com) ou simplesmente executar o comando a seguir no [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Acessar declarações de usuários

O Serviço de Aplicativo transmite declarações do usuário para seu aplicativo usando cabeçalhos especiais. Solicitações externas não são permitidas para definir esses cabeçalhos, portanto são apresentadas somente se definido pelo Serviço de Aplicativo. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Um código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4.6, **ClaimsPrincipal** é definido automaticamente com os valores apropriados. O ASP.NET Core, no entanto, não fornece um middleware de autenticação que se integra às declarações de usuário do serviço de aplicativo. Para obter uma solução alternativa, consulte [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Seu aplicativo também pode obter detalhes adicionais sobre o usuário autenticado chamando `/.auth/me`. Os SDKs do servidor dos Aplicativos Móveis fornecem métodos auxiliares para trabalhar com esses dados. Para saber mais, confira [Como usar o SDK do Node.js dos Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Trabalhar com o SDK do servidor de back-end .NET para Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperar tokens no código do aplicativo

No seu código de servidor, os tokens específicos do provedor são injetados no cabeçalho da solicitação, para que você possa acessá-los facilmente. A tabela a seguir mostra os possíveis nomes de cabeçalho do token:

| Provedor | Nomes do Cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token do Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta da Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Do seu código do cliente (por exemplo, um aplicativo móvel ou JavaScript no navegador), envie uma solicitação HTTP `GET` para `/.auth/me`. O JSON retornado tem os tokens específicos do provedor.

> [!NOTE]
> Tokens de acesso são para acessar recursos do provedor, para que eles fiquem presentes somente se você configurar o provedor com um segredo do cliente. Para saber como obter tokens de atualização, confira Tokens de acesso de atualização.

## <a name="refresh-identity-provider-tokens"></a>Atualizar tokens do provedor de identidade

Quando o token de acesso do seu provedor (não o [token de sessão](#extend-session-token-expiration-grace-period)) expirar, você precisará autenticar novamente o usuário antes de usar esse token novamente. Você pode evitar a expiração do token fazendo uma `GET` chamada para o `/.auth/refresh` ponto de extremidade de seu aplicativo. Quando chamado, o Serviço de Aplicativo atualiza automaticamente tokens de acesso no repositório de token para o usuário autenticado. As solicitações subsequentes de tokens do seu código do aplicativo obtêm tokens atualizados. No entanto, para que a atualização do token funcione, o repositório de token deve conter [tokens de atualização](https://auth0.com/learn/refresh-tokens/) para o seu provedor. A forma de obter tokens de atualização é documentada por cada provedor, mas a lista a seguir traz um breve resumo:

- **Google**: anexe um `access_type=offline` parâmetro de cadeia de caracteres para consulta a sua `/.auth/login/google` chamada à API. Se usar o SDK de Aplicativos Móveis, você pode adicionar o parâmetro a uma das `LogicAsync` sobrecargas (consulte [Tokens de atualização do Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: não fornece tokens de atualização. Tokens de vida útil longa expiram em 60 dias (consulte [Expiração e extensão de tokens de acesso do Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: tokens de acesso não expiram (consulte [Perguntas frequentes sobre o OAuth do Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft Account**: quando [definir configurações de autenticação de conta Microsoft](configure-authentication-provider-microsoft.md), selecione o escopo `wl.offline_access`.
- **Azure Active Directory**: em [https://resources.azure.com](https://resources.azure.com), execute as seguintes etapas:
    1. Na parte superior da página, selecione **Ler/Gravar**.
    2. No navegador esquerdo, navegue até **assinaturas** > * *_ \<subscription\_name_** > **resourceGroups** > *_* * * \<resource\_group\_name> _> **provedores**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a propriedade a seguir. Substitua _\<app\_id>_ pela ID de aplicativo Azure Active Directory do serviço que você deseja acessar.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Put**. 

Depois que seu provedor estiver configurado, você poderá [ encontrar o token de atualização e o tempo de expiração do token de acesso ](#retrieve-tokens-in-app-code) na loja do token. 

Para atualizar seu token de acesso a qualquer momento, basta chamar `/.auth/refresh` em qualquer idioma. O snippet a seguir usa o jQuery para atualizar seus tokens de acesso de um cliente JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se um usuário revoga as permissões concedidas ao seu aplicativo, a chamada para `/.auth/me` pode falhar com um resposta `403 Forbidden`. Para diagnosticar erros, verifique os logs de aplicativo para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Estender o período de cortesia de término do token da sessão

A sessão autenticada expira após 8 horas. Depois que uma sessão autenticada expira, há um período de cortesia de 72 horas por padrão. Dentro desse período, você pode atualizar o token de sessão com o Serviço de Aplicativo sem reautenticar o usuário. Você pode simplesmente chamar `/.auth/refresh` quando o token de sessão se torna inválido e não é necessário rastrear o término do token por conta própria. Após o período de cortesia de 72 horas, o usuário deverá entrar novamente para obter um token de sessão válido.

Se 72 horas não for tempo suficiente, você pode estender essa janela de expiração. Estender a expiração por um longo período pode ter implicações significativas de segurança (por exemplo, quando um token de autenticação é perdido ou roubado). Portanto, você deve deixar o padrão de 72 horas ou definir o período de extensão para o menor valor.

Para estender a janela de validade padrão, execute o seguinte comando no [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de cortesia se aplica somente à sessão autenticada do Serviço de Aplicativo, não aos tokens de provedores de identidade. Não há um período de cortesia para os tokens de provedor expirados. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limite do domínio de contas de entrada

A Conta da Microsoft e o Microsoft Azure Active Directory permitem a entrada de vários domínios. Por exemplo, a Conta da Microsoft permite contas de _outlook.com_, _live.com_ e _hotmail.com_. O Azure AD permite qualquer número de domínios personalizados para as contas de entrada. No entanto, talvez você queira acelerar seus usuários diretamente para sua própria página de entrada do Azure AD com marca (como `contoso.com` ). Para sugerir o nome de domínio das contas de entrada, siga estas etapas.

No [https://resources.azure.com](https://resources.azure.com) , navegue até **assinaturas** > * *_ \<subscription\_name_** > **resourceGroups** > *_* * * \<resource\_group\_name> _> **provedores**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Clique em **Editar**, modifique a propriedade a seguir e, em seguida, clique em **Put**. Certifique-se de substituir _\<domain\_name>_ pelo domínio desejado.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Essa configuração acrescenta o `domain_hint` parâmetro de cadeia de caracteres de consulta à URL de redirecionamento de logon. 

> [!IMPORTANT]
> É possível que o cliente remova o `domain_hint` parâmetro depois de receber a URL de redirecionamento e, em seguida, faça logon com um domínio diferente. Portanto, embora essa função seja conveniente, ela não é um recurso de segurança.
>

## <a name="authorize-or-deny-users"></a>Autorizar ou negar usuários

Embora o serviço de aplicativo se encarrega do caso de autorização mais simples (ou seja, rejeitar solicitações não autenticadas), seu aplicativo pode exigir um comportamento de autorização mais refinado, como limitar o acesso a apenas um grupo específico de usuários. Em determinados casos, você precisa escrever código de aplicativo personalizado para permitir ou negar acesso ao usuário conectado. Em outros casos, o serviço de aplicativo ou o provedor de identidade pode ser capaz de ajudar sem a necessidade de alterações de código.

- [Nível de servidor](#server-level-windows-apps-only)
- [Nível do provedor de identidade](#identity-provider-level)
- [Nível do aplicativo](#application-level)

### <a name="server-level-windows-apps-only"></a>Nível do servidor (somente aplicativos do Windows)

Para qualquer aplicativo do Windows, você pode definir o comportamento de autorização do servidor Web do IIS, editando o arquivo de *Web.config* . Os aplicativos do Linux não usam o IIS e não podem ser configurados por meio de *Web.config*.

1. Navegue até `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. No Gerenciador de navegador de seus arquivos do serviço de aplicativo, navegue até *site/wwwroot*. Se um *Web.config* não existir, crie-o selecionando **+**  >  **novo arquivo**. 

1. Selecione o lápis para *Web.config* para editá-lo. Adicione o código de configuração a seguir e clique em **salvar**. Se *Web.config* já existir, basta adicionar o `<authorization>` elemento a tudo nele. Adicione as contas que você deseja permitir no `<allow>` elemento.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Nível do provedor de identidade

O provedor de identidade pode fornecer determinada autorização de chave. Por exemplo:

- Para [Azure app serviço](configure-authentication-provider-aad.md), você pode [gerenciar o acesso de nível corporativo](../active-directory/manage-apps/what-is-access-management.md) diretamente no Azure AD. Para obter instruções, consulte [como remover o acesso de um usuário a um aplicativo](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Para o [Google](configure-authentication-provider-google.md), os projetos de API do Google que pertencem a uma [organização](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) podem ser configurados para permitir acesso somente aos usuários em sua organização (consulte a [página de suporte do **OAuth 2,0 Configurando** o Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Nível de aplicativo

Se qualquer um dos outros níveis não fornecer a autorização de que você precisa, ou se sua plataforma ou provedor de identidade não tiver suporte, você deverá escrever um código personalizado para autorizar usuários com base nas [declarações do usuário](#access-user-claims).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: autenticar e autorizar usuários de ponta a ponta (Windows)](app-service-web-tutorial-auth-aad.md) 
>  [Tutorial: autenticar e autorizar usuários de ponta a ponta (Linux)](containers/tutorial-auth-aad.md)
