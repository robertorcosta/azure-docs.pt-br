---
title: incluir arquivo
description: incluir arquivo
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309034"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AutenticaçãoPropriedades de resultado em MSAL.NET

Os métodos para adquirir `AuthenticationResult`tokens retornam . Para métodos de `Task<AuthenticationResult>` sincronismo, retorna.

Em MSAL.NET, `AuthenticationResult` expõe:

- `AccessToken`para a API web acessar recursos. Este parâmetro é uma seqüência, geralmente um JWT codificado pela Base-64. O cliente nunca deve olhar dentro do token de acesso. O formato não é garantido para permanecer estável, e pode ser criptografado para o recurso. Escrever código que depende do conteúdo do token de acesso no cliente é uma das maiores fontes de erros e quebras lógicas do cliente. Para obter mais informações, consulte [tokens de acesso](../articles/active-directory/develop/access-tokens.md).
- `IdToken`para o usuário. Este parâmetro é um JWT codificado. Para obter mais informações, consulte [tokens de ID](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`diz a data e a hora em que o token expira.
- `TenantId`contém o inquilino em que o usuário foi encontrado. Para usuários convidados nos cenários B2B do Azure Active Directory (Azure AD), o ID do inquilino é o inquilino convidado, não o inquilino único.
Quando o token é entregue `AuthenticationResult` para um usuário, também contém informações sobre este usuário. Para fluxos confidenciais de clientes onde os tokens são solicitados sem usuário para o aplicativo, essas informações do usuário são nulas.
- O `Scopes` para o qual o token foi emitido.
- A ID exclusiva para o usuário.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de `IAccount` uma conta através da interface. Esta mudança de ruptura fornece a semântica certa. O mesmo usuário pode ter várias contas, em diferentes diretórios Azure AD. Além disso, MSAL.NET fornece melhores informações no caso de cenários de hóspedes porque as informações da conta doméstica são fornecidas.
O diagrama a seguir `IAccount` mostra a estrutura da interface.

![Estrutura de interface do IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A `AccountId` classe identifica uma conta em um inquilino específico com as propriedades mostradas na tabela a seguir.

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de string para um GUID, que é o ID do inquilino onde a conta reside. |
| `ObjectId` | Uma representação de string para um GUID, que é o ID do usuário que possui a conta no inquilino. |
| `Identifier` | Identificador exclusivo para a conta. `Identifier`é a `ObjectId` concatenação `TenantId` e separado por uma comma. Não estão codificados na Base 64. |

A `IAccount` interface representa informações sobre uma única conta. O mesmo usuário pode estar presente em diferentes inquilinos, o que significa que um usuário pode ter várias contas. Seus membros são mostrados na tabela a seguir.

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma string que contém o valor exibivel no formato UserPrincipalName (UPN), por exemplo, john.doe@contoso.com. Essa seqüência pode ser nula, ao contrário do HomeAccountId e do HomeAccountId.Identifier, que não será nulo. Esta propriedade substitui `DisplayableId` a `IUser` propriedade de em versões anteriores de MSAL.NET. |
| `Environment` | Uma string que contém o provedor de `login.microsoftonline.com`identidade para esta conta, por exemplo, . Este imóvel substitui `IdentityProvider` a `IUser`propriedade `IdentityProvider` de, exceto que também tinha informações sobre o inquilino, além do ambiente em nuvem. Aqui, o valor é apenas o hospedeiro. |
| `HomeAccountId` | O ID da conta da conta residencial para o usuário. Esta propriedade identifica exclusivamente o usuário entre os inquilinos do Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Use o token para chamar uma API protegida

Depois `AuthenticationResult` é devolvido pelo `result`MSAL em , adicione-o ao cabeçalho de autorização HTTP antes de fazer a chamada para acessar a API da Web protegida.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```