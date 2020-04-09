---
title: Aplicativos de clientepúblico de conta única e múltipla | Azure
description: Uma visão geral de aplicativos de clientes públicos de conta única e múltipla.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881325"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplicativos de clientes públicos de conta única e múltipla

Este artigo irá ajudá-lo a entender os tipos usados em aplicativos de clientes públicos de conta única e múltipla, com foco em aplicativos de clientepúblico de conta única. 

A Adocção de Autenticação de Diretório Ativo (ADAL) modela o servidor.  Em vez disso, a Microsoft Authentication Library (MSAL) modela seu aplicativo cliente.  A maioria dos aplicativos Android são considerados clientes públicos. Um cliente público é um aplicativo que não pode guardar segredo.  

A MSAL é especializada `PublicClientApplication` na superfície da API para simplificar e esclarecer a experiência de desenvolvimento de aplicativos que permitem que apenas uma conta seja usada por vez. `PublicClientApplication`é subclassificado `SingleAccountPublicClientApplication` por `MultipleAccountPublicClientApplication`e .  O diagrama a seguir mostra a relação entre essas classes.

![Diagrama de classe UML do SingleAccountPublicPublicApplication UML](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Aplicativo de cliente público de conta única

A `SingleAccountPublicClientApplication` classe permite que você crie um aplicativo baseado em MSAL que só permite que uma única conta seja assinada por vez. `SingleAccountPublicClientApplication` é diferente de `PublicClientApplication` nestes aspectos:

- A MSAL rastreia a conta de entrada atualmente assinada.
  - Se o seu aplicativo estiver usando uma corretora (o padrão durante o registro do aplicativo do portal Azure) e estiver instalado em um dispositivo onde um corretor está presente, a MSAL verificará se a conta ainda está disponível no dispositivo.
- `signIn`permite que você faça login em uma conta explicitamente e separadamente da solicitação de escopos.
- `acquireTokenSilent`não requer um parâmetro de conta.  Se você fornecer uma conta, e a conta que você fornece não corresponder `MsalClientException` à conta corrente rastreada pela MSAL, uma é lançada.
- `acquireToken`não permite que o usuário alterne as contas. Se o usuário tentar mudar para uma conta diferente, uma exceção será lançada.
- `getCurrentAccount`retorna um objeto de resultado que fornece o seguinte:
  - Um booleano indicando se a conta mudou. Uma conta pode ser alterada como resultado da retirada do dispositivo, por exemplo.
  - A conta anterior. Isso é útil se você precisar fazer qualquer limpeza local de dados quando a conta é removida do dispositivo ou quando uma nova conta é assinada.
  - A conta corrente.
- `signOut`remove quaisquer tokens associados ao seu cliente do dispositivo.  

Quando um corretor de autenticação Android, como o Microsoft Authenticator ou o Intune Company Portal, estiver instalado no dispositivo e seu aplicativo estiver configurado para usar o corretor, `signOut` não removerá a conta do dispositivo.

## <a name="single-account-scenario"></a>Cenário de conta única

O seguinte pseudocódigo `SingleAccountPublicClientApplication`ilustra o uso .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Aplicativo de cliente público de várias contas

A `MultipleAccountPublicClientApplication` classe é usada para criar aplicativos baseados em MSAL que permitem que várias contas sejam assinadas ao mesmo tempo. Ele permite obter, adicionar e remover contas da seguinte forma:

### <a name="add-an-account"></a>Adicionar uma conta

Use uma ou mais contas `acquireToken` em seu aplicativo ligando uma ou mais vezes.  

### <a name="get-accounts"></a>Obter contas

- Ligue `getAccount` para obter uma conta específica.
- Chamada `getAccounts`para obter uma lista de contas atualmente conhecidas pelo aplicativo.

Seu aplicativo não será capaz de enumerar todas as contas da plataforma de identidade da Microsoft no dispositivo conhecido pelo aplicativo corretor. Ele só pode enumerar contas que foram usadas pelo seu aplicativo.  As contas que foram removidas do dispositivo não serão devolvidas por essas funções.

### <a name="remove-an-account"></a>Remover uma conta

Remova uma conta `removeAccount` ligando com um identificador de conta.

Se o aplicativo estiver configurado para usar um corretor e um corretor estiver instalado no dispositivo, `removeAccount`a conta não será removida do corretor quando você ligar .  Apenas os tokens associados ao seu cliente são removidos.

## <a name="multiple-account-scenario"></a>Cenário de várias contas

O pseudocódigo a seguir mostra como criar um aplicativo de conta múltipla, listar contas no dispositivo e adquirir tokens.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
