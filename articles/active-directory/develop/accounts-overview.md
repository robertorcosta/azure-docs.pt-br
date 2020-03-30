---
title: Contas da plataforma de identidade da Microsoft & perfis de inquilinos no Android | Azure
description: Uma visão geral das contas da plataforma de identidade da Microsoft para Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611865"
---
# <a name="accounts--tenant-profiles-android"></a>Contas e perfis de locatário (Android)

Este artigo fornece uma visão `account` geral do que é um na plataforma de identidade da Microsoft.

A API da Microsoft Authentication Library (MSAL) substitui o termo *usuário* pelo termo *conta*. Uma das razões é que um usuário (agente humano ou de software) pode ter, ou pode usar, várias contas. Essas contas podem estar na própria organização do usuário e/ou em outras organizações das as que o usuário é membro.

Uma conta na plataforma de identidade da Microsoft consiste em:

- Um identificador único.  
- Uma ou mais credenciais usadas para demonstrar a propriedade/controle da conta.
- Um ou mais perfis consistindo de atributos como:
  - Imagem, Nome dado, Nome da família, Título, Localização do Escritório
- Uma conta tem uma fonte de autoridade ou sistema de registro. Este é o sistema onde a conta é criada e onde as credenciais associadas a essa conta são armazenadas. Em sistemas multi-inquilinos como a plataforma de `tenant` identidade da Microsoft, o sistema de registro é o local onde a conta foi criada. Este inquilino também é `home tenant`referido como o .
- As contas na plataforma de identidade da Microsoft têm os seguintes sistemas de registro:
  - Diretório Ativo do Azure, incluindo o Azure Active Directory B2C.
  - Conta Microsoft (Ao Vivo).
- Contas de sistemas de registro fora da plataforma de identidade da Microsoft são representadas dentro da plataforma de identidade da Microsoft, incluindo:
  - identidades de diretórios on-premises conectados (Diretório Ativo do Servidor Windows)
  - identidades externas do LinkedIn, GitHub e assim por diante.
  Nesses casos, uma conta tem um sistema de registro de origem e um sistema de registro dentro da plataforma de identidade da Microsoft.
- A plataforma de identidade da Microsoft permite que uma conta seja usada para acessar recursos pertencentes a várias organizações (inquilinos do Azure Active Directory).
  - Para registrar que uma conta de um sistema de registro (AAD Tenant A) tem acesso a um recurso em outro sistema de registro (AAD Tenant B), a conta deve ser representada no inquilino onde o recurso é definido. Isso é feito criando um registro local da conta do sistema A no sistema B.
  - Este registro local, que é a representação da conta, está vinculado à conta original.
  - MSAL expõe este registro `Tenant Profile`local como um .
  - O Perfil do Inquilino pode ter diferentes atributos apropriados ao contexto local, como título de trabalho, localização do escritório, informações de contato, etc.
- Como uma conta pode estar presente em um ou mais inquilinos, uma conta pode ter mais de um perfil.

> [!NOTE]
> A MSAL trata o sistema de conta da Microsoft (Live, MSA) como outro inquilino dentro da plataforma de identidade da Microsoft. A id de inquilino do inquilino da conta Microsoft é:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagrama de visão geral da conta

![Diagrama de visão geral da conta](./media/accounts-overview/accounts-overview.svg)

No diagrama acima:

- A `bob@contoso.com` conta é criada no Windows Server Active Directory (sistema de registro de origem no local).
- A `tom@live.com` conta é criada no inquilino da conta da Microsoft.
- `bob@contoso.com`tem acesso a pelo menos um recurso nos seguintes inquilinos do Azure Active Directory:
  - contoso.com (sistema de registro em nuvem - vinculado ao sistema de registro local)
  - fabrikam.com
  - woodgrovebank.com
  - Existe um `bob@contoso.com` perfil de inquilino em cada um desses inquilinos.
- `tom@live.com`tem acesso aos recursos nos seguintes inquilinos da Microsoft:
  - contoso.com
  - fabrikam.com
  - Existe um `tom@live.com` perfil de inquilino em cada um desses inquilinos.
- Informações sobre Tom e Bob em outros inquilinos podem diferir das do sistema de registro. Eles podem diferir por atributos como título de trabalho, localização do escritório e assim por diante. Eles podem ser membros de grupos e/ou funções dentro de cada organização (Azure Active Directory Tenant). Referimos a bob@contoso.com essas informações como perfil de inquilino.

No bob@contoso.com diagrama, tom@live.com e ter acesso a recursos em diferentes inquilinos do Azure Active Directory. Para obter mais informações, consulte [Adicionar usuários de colaboração B2B do Active Directory no portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Contas e único login (SSO)

O cache de token MSAL armazena um *único token de atualização* por conta. Esse token de atualização pode ser usado para solicitar silenciosamente tokens de acesso de vários inquilinos da plataforma de identidade microsoft. Quando um corretor é instalado em um dispositivo, a conta é gerenciada pelo corretor, e o login único em todo o dispositivo se torna possível.

> [!IMPORTANT]
> A conta Business to Consumer (B2C) e o comportamento do token de atualização diferem do resto da plataforma de identidade da Microsoft. Para obter mais informações, consulte [As contas & Políticas B2C](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identificadores de conta

O ID da conta MSAL não é um ID de objeto de conta. Não se destina a ser analisado e/ou confiado para transmitir qualquer outra coisa que não seja a exclusividade dentro da plataforma de identidade da Microsoft.

Para compatibilidade com a Adead Authentication Library (ADAL) e para facilitar a migração do ADAL para o MSAL, a MSAL pode procurar contas usando qualquer identificador válido para a conta disponível no cache MSAL.  Por exemplo, o seguinte sempre recuperará tom@live.com o mesmo objeto de conta para porque cada um dos identificadores é válido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Acessando reclamações sobre uma conta

Além de solicitar um token de acesso, a MSAL também sempre solicita um token de ID de cada inquilino. Ele faz isso sempre solicitando os seguintes escopos:

- openid
- Perfil

O token de ID contém uma lista de reclamações. `Claims`são pares de nome/valor sobre a conta e são usados para fazer a solicitação.

Como mencionado anteriormente, cada inquilino onde existe uma conta pode armazenar informações diferentes sobre a conta, incluindo, mas não se limitando a atributos como: cargo, localização do escritório e assim por diante.

Embora uma conta possa ser um membro ou convidado em várias organizações, a MSAL não consulta um serviço para obter uma lista dos inquilinos dos preços que a conta é membro. Em vez disso, a MSAL constrói uma lista de inquilinos em que a conta está presente, como resultado de solicitações de token que foram feitas.

As reivindicações expostas no objeto da conta são sempre as reivindicações do 'inquilino doméstico'/{autoridade} para uma conta. Se essa conta não foi usada para solicitar um token para seu inquilino doméstico, a MSAL não pode fornecer reclamações através do objeto da conta.  Por exemplo: 

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Para ver uma lista de reclamações disponíveis no objeto da conta, consulte [as reclamações em um id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Para incluir reclamações adicionais em seu id_token, consulte a documentação opcional de reclamações em [Como: Fornecer reclamações opcionais ao seu aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Acessa ndo reivindicações de perfil de inquilino

Para acessar reclamações sobre uma conta como elas aparecem em outros `IMultiTenantAccount`inquilinos, primeiro você precisa lançar seu objeto de conta para . Todas as contas podem ser multi-inquilinos, mas o número de perfis de inquilinos disponíveis via MSAL é baseado em quais inquilinos você solicitou tokens de usar a conta corrente.  Por exemplo: 

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Políticas B2C & contas

Os tokens de atualização de uma conta não são compartilhados entre as políticas B2C. Como resultado, o uso de tokens por único sinal não é possível. Isso não significa que a única inscrição não seja possível. Isso significa que o single sign-on tem que usar uma experiência interativa na qual um cookie está disponível para habilitar o login único.

Isso também significa que, no caso do MSAL, se você adquirir tokens usando diferentes políticas B2C, então estes são tratados como contas separadas - cada uma com seu próprio identificador. Se você quiser usar uma conta para `acquireTokenSilent`solicitar um token usando, então você precisará selecionar a conta na lista de contas que correspondem à política que você está usando com a solicitação de token. Por exemplo: 

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
