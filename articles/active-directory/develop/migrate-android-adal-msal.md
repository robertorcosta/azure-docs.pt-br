---
title: Guia de migração ADAL para MSAL para Android | Azure
description: Saiba como migrar seu aplicativo Azure Active Directory Authenticy Library (ADAL) para a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084063"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guia de migração ADAL para MSAL para Android

Este artigo destaca as alterações que você precisa fazer para migrar um aplicativo que usa a Adal (Active Directory Authentication Library, biblioteca de autenticação ativa do azure) para usar a Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

O ADAL trabalha com o ponto final do Azure Active Directory v1.0. A Microsoft Authentication Library (MSAL) funciona com a plataforma de identidade Microsoft - anteriormente conhecida como o ponto final do Azure Active Directory v2.0. A plataforma de identidade da Microsoft difere do Azure Active Directory v1.0 na quilona:

Oferece suporte a:
  - Identidade Organizacional (Diretório Ativo do Azure)
  - Identidades não organizacionais, como Outlook.com, Xbox Live, e assim por diante
  - (Somente B2C) Login federado com Google, Facebook, Twitter e Amazon

- É compatível com padrões com:
  - OAuth v2.0
  - Conecte openid (OIDC)

A API pública do MSAL introduz mudanças importantes, incluindo:

- Um novo modelo para acessar tokens:
  - O ADAL fornece acesso a `AuthenticationContext`tokens através do , que representa o servidor. A MSAL fornece acesso a `PublicClientApplication`tokens através do , que representa o cliente. Os desenvolvedores de clientes não `PublicClientApplication` precisam criar uma nova instância para cada Autoridade com a qual precisam interagir. Apenas `PublicClientApplication` uma configuração é necessária.
  - Suporte para solicitação de tokens de acesso usando escopos, além de identificadores de recursos.
  - Suporte para consentimento incremental. Os desenvolvedores podem solicitar escopos à medida que o usuário acessa cada vez mais funcionalidades no aplicativo, incluindo aqueles não incluídos durante o registro do aplicativo.
  - As autoridades não são mais validadas em tempo de execução. Em vez disso, o desenvolvedor declara uma lista de "autoridades conhecidas" durante o desenvolvimento.
- Alterações da API do token:
  - Na ADAL, `AcquireToken()` primeiro faz um pedido silencioso. Caso contrário, faz um pedido interativo. Esse comportamento resultou em alguns `AcquireToken`desenvolvedores confiando apenas, o que resultou em um pedido de credenciais inesperadamente solicitado pelo usuário. A MSAL exige que os desenvolvedores sejam intencionais sobre quando o usuário recebe um prompt de ia.
    - `AcquireTokenSilent`sempre resulta em um pedido silencioso que ou consegue ou falha.
    - `AcquireToken`sempre resulta em uma solicitação que solicita ao usuário via UI.
- O MSAL suporta login de um navegador padrão ou de uma visualização web incorporada:
  - Por padrão, o navegador padrão no dispositivo é usado. Isso permite que a MSAL use o estado de autenticação (cookies) que já pode estar presente para uma ou mais contas assinadas. Se não houver estado de autenticação, a autenticação durante a autorização via MSAL resulta na criação do estado de autenticação (cookies) em benefício de outros aplicativos web que serão usados no mesmo navegador.
- Novo modelo de exceção:
  - As exceções definem mais claramente o tipo de erro que ocorreu e o que o desenvolvedor precisa fazer para resolvê-lo.
- O MSAL suporta objetos de parâmetro para `AcquireToken` e `AcquireTokenSilent` chamadas.
- A MSAL suporta a configuração declarativa para:
  - ID do cliente, Redirecionar URI.
  - Navegador incorporado vs Padrão
  - Autoridades
  - Configurações HTTP, como tempo de leitura e tempo de conexão

## <a name="your-app-registration-and-migration-to-msal"></a>Seu registro de aplicativo e migração para o MSAL

Você não precisa alterar seu registro de aplicativo existente para usar o MSAL. Se você quiser aproveitar o consentimento incremental/progressivo, talvez seja necessário revisar o registro para identificar os escopos específicos que deseja solicitar incrementalmente. Mais informações sobre escopos e consentimento incremental seguem.

No seu registro de aplicativo no portal, você verá uma guia **de permissões de API.** Isso fornece uma lista das APIs e permissões (escopos) às as que seu aplicativo está configurado para solicitar acesso. Ele também mostra uma lista dos nomes de escopo associados a cada permissão de API.

### <a name="user-consent"></a>Consentimento do usuário

Com o ADAL e o ponto final AAD v1, o consentimento do usuário aos recursos que possuem foi concedido no primeiro uso. Com a MSAL e a plataforma de identidade da Microsoft, o consentimento pode ser solicitado gradualmente. O consentimento incremental é útil para permissões que um usuário pode considerar de alto privilégio, ou pode questionar de outra forma se não for fornecida uma explicação clara do porquê a permissão é necessária. No ADAL, essas permissões podem ter resultado no abandono do usuário ao login no seu aplicativo.

> [!TIP]
> Recomendamos o uso de consentimento incremental em cenários onde você precisa fornecer contexto adicional ao seu usuário sobre por que seu aplicativo precisa de uma permissão.

### <a name="admin-consent"></a>Consentimento do administrador

Os administradores da organização podem consentir com as permissões que seu aplicativo exige em nome de todos os membros de sua organização. Algumas organizações só permitem que os admins consentirem com os aplicativos. O consentimento do admin exige que você inclua todas as permissões e escopos de API usados pelo seu aplicativo no registro do aplicativo.

> [!TIP]
> Mesmo que você possa solicitar um escopo usando o MSAL para algo não incluído no registro do aplicativo, recomendamos que você atualize seu registro do aplicativo para incluir todos os recursos e escopos aos qual um usuário poderia conceder permissão.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migração de IDs de recursos para escopos

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticar e solicitar autorização para todas as permissões no primeiro uso

Se você está usando o ADAL no momento e não precisa usar o consentimento incremental, `acquireToken` a maneira `AcquireTokenParameter` mais simples de começar a usar o MSAL é fazer uma solicitação usando o novo objeto e definir o valor de ID de recurso.

> [!CAUTION]
> Não é possível definir ambos os escopos e um id de recurso. Tentar definir ambos resultará `IllegalArgumentException`em um .

 Isso resultará no mesmo comportamento v1 que você está acostumado. Todas as permissões solicitadas no registro do aplicativo são solicitadas ao usuário durante sua primeira interação.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autenticar e solicitar permissões apenas conforme necessário

Para aproveitar o consentimento incremental, faça uma lista de permissões (escopos) que seu aplicativo usa a partir do registro do aplicativo e organize-as em duas listas com base em:

- Quais escopos você deseja solicitar durante a primeira interação do usuário com seu aplicativo durante o login.
- As permissões que estão associadas a um recurso importante do seu aplicativo que você também precisará explicar ao usuário.

Depois de organizar os escopos, organize cada lista por qual recurso (API) você deseja solicitar um token. Assim como quaisquer outros escopos que você deseja que o usuário autorize ao mesmo tempo.

O objeto de parâmetros usado para fazer sua solicitação ao Suporte MSAL:

- `Scope`: A lista de escopos para os que você deseja solicitar autorização e receber um token de acesso.
- `ExtraScopesToConsent`: Uma lista adicional de escopos para os seus escopos que você deseja solicitar autorização enquanto solicita um token de acesso para outro recurso. Esta lista de escopos permite minimizar o número de vezes que você precisa para solicitar autorização do usuário. O que significa menos autorização do usuário ou solicitações de consentimento.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrar de AutenticaçãoContexto para PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Construindo o PublicClientApplication

Quando você usa MSAL, `PublicClientApplication`você instancia um . Este objeto modela sua identidade de aplicativo e é usado para fazer solicitações a uma ou mais autoridades. Com este objeto, você configurará sua identidade cliente, redirecionará uri, autoridade padrão, se usará o navegador do dispositivo vs. exibição da Web incorporada, o nível de log e muito mais.

Você pode configurar declarativamente este objeto com JSON, que você fornece como um arquivo ou armazenar como um recurso dentro do seu APK.

Embora este objeto não seja um singleton, `Executors` internamente ele usa compartilhado para solicitações interativas e silenciosas.

### <a name="business-to-business"></a>Negócios para Negócios

No ADAL, todas as organizações das que você solicita `AuthenticationContext`tokens de acesso exigem uma instância separada do . No MSAL, isso não é mais um requisito. Você pode especificar a autoridade a partir da qual deseja solicitar um token como parte de sua solicitação silenciosa ou interativa.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrar da validação de autoridades para autoridades conhecidas

A MSAL não possui uma bandeira para ativar ou desativar a validação da autoridade. A validação de autoridade é um recurso no ADAL e nos primeiros lançamentos do MSAL, que impede que seu código solicite tokens de uma autoridade potencialmente maliciosa. A MSAL agora recupera uma lista de autoridades conhecidas pela Microsoft e mescla essa lista com as autoridades que você especificou em sua configuração.

> [!TIP]
> Se você é um usuário do Azure Business to Consumer (B2C), isso significa que você não precisa mais desativar a validação da autoridade. Em vez disso, inclua cada uma das suas políticas AD B2C azure suportadas como autoridades em sua configuração MSAL.

Se você tentar usar uma autoridade que não é conhecida pela Microsoft e não `UnknownAuthorityException`estiver incluída em sua configuração, você receberá um .

### <a name="logging"></a>Registrando em log
Agora você pode configurar o registro declarativamente como parte de sua configuração, assim:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrar de UserInfo para Conta

No ADAL, `AuthenticationResult` o `UserInfo` fornece um objeto usado para recuperar informações sobre a conta autenticada. O termo "usuário", que significava um agente humano ou de software, foi aplicado de forma a dificultar a comunicação de que alguns aplicativos suportam um único usuário (seja um agente humano ou de software) que tenha múltiplas contas.

Considere uma conta bancária. Você pode ter mais de uma conta em mais de uma instituição financeira. Quando você abre uma conta, você (o usuário) é emitido credenciais, como um cartão ATM & PIN, que são usados para acessar seu saldo, pagamentos de contas, e assim por diante, para cada conta. Essas credenciais só podem ser usadas na instituição financeira que as emitiu.

Por analogia, como contas em uma instituição financeira, contas na plataforma de identidade da Microsoft são acessadas usando credenciais. Essas credenciais são registradas ou emitidas pela Microsoft. Ou pela Microsoft em nome de uma organização.

Quando a plataforma de identidade microsoft difere de uma instituição financeira, nesta analogia, é que a plataforma de identidade Microsoft fornece uma estrutura que permite que um usuário use uma conta, e suas credenciais associadas, para acessar recursos que pertencem a múltiplos indivíduos e organizações. Isso é como poder usar um cartão emitido por um banco, em outra instituição financeira. Isso funciona porque todas as organizações em questão estão usando a plataforma de identidade Microsoft, que permite que uma conta seja usada em várias organizações. Aqui está um exemplo:

Sam trabalha para Contoso.com mas gerencia máquinas virtuais Azure que pertencem a Fabrikam.com. Para Sam gerenciar as máquinas virtuais de Fabrikam, ele precisa ser autorizado a acessá-las. Esse acesso pode ser concedido adicionando a conta de Sam a Fabrikam.com, e concedendo à sua conta um papel que lhe permite trabalhar com as máquinas virtuais. Isso seria feito com o portal Azure.

Adicionar a conta Contoso.com de Sam como membro de Fabrikam.com resultaria na criação de um novo disco no Azure Active Directory for Sam da Fabrikam.com. O registro de Sam no Azure Active Directory é conhecido como um objeto de usuário. Neste caso, esse objeto de usuário apontaria de volta para o objeto de usuário do Sam em Contoso.com. O objeto de usuário Fabrikam de Sam é a representação local de Sam, e seria usado para armazenar informações sobre a conta associada a Sam no contexto de Fabrikam.com. Em Contoso.com, o título de Sam é Consultor Sênior de DevOps. Em Fabrikam, o título de Sam é Empreiteiro-Máquinas Virtuais. Em Contoso.com, Sam não é responsável, nem autorizado, a gerenciar máquinas virtuais. Em Fabrikam.com, é sua única função de trabalho. No entanto, Sam ainda tem apenas um conjunto de credenciais para acompanhar, que são as credenciais emitidas por Contoso.com.

Uma vez `acquireToken` que uma chamada bem-sucedida `IAccount` é feita, você `acquireTokenSilent` verá uma referência a um objeto que pode ser usado em solicitações posteriores.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Se você tiver um aplicativo que acessa reclamações sobre uma conta de cada `IAccount` um `IMultiTenantAccount`dos inquilinos em que a conta está representada, você pode lançar objetos para . Esta interface fornece `ITenantProfiles`um mapa de , com chave por ID do inquilino, que permite que você acesse as reivindicações que pertencem à conta em cada um dos inquilinos que você solicitou um token, em relação à conta corrente.

As reivindicações na `IAccount` raiz `IMultiTenantAccount` do e sempre contêm as reivindicações do inquilino da casa. Se você ainda não fez um pedido de um token dentro do inquilino da casa, esta coleção estará vazia.

## <a name="other-changes"></a>Outras alterações

### <a name="use-the-new-authenticationcallback"></a>Use o novo AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migre para as novas exceções

No ADAL, há um tipo `AuthenticationException`de exceção, que inclui `ADALError` um método para recuperar o valor enum.
No MSAL, há uma hierarquia de exceções, e cada uma tem seu próprio conjunto de códigos de erro específicos associados.

Exceções msal

|Exceção  | Descrição  |
|---------|---------|
| `MsalException`     | Exceção verificada padrão lançada pelo MSAL.  |
| `MsalClientException`     | Jogado se o erro é o lado do cliente. |
| `MsalArgumentException`     | Jogado se um ou mais argumentos de entrada são inválidos. |
| `MsalClientException`     | Jogado se o erro é o lado do cliente. |
| `MsalServiceException`     | Lançado se o erro for do lado do servidor. |
| `MsalUserCancelException`     | Jogado se o usuário cancelasse o fluxo de autenticação.  |
| `MsalUiRequiredException`     | Jogado se o token não pode ser atualizado silenciosamente.  |
| `MsalDeclinedScopeException`     | Jogado se um ou mais escopos solicitados foram recusados pelo servidor.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Lançado se o recurso tiver a política de proteção MAMCA ativada. |

### <a name="adalerror-to-msalexception-errorcode"></a>Erro aDALpara MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Registro adal para registro de MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
