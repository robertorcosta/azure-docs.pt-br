---
title: Python ADAL para guia de migração MSAL | Azure
description: Saiba como migrar o aplicativo Python (ADAL) Da Biblioteca de Autenticação de Diretório Ativo do Azure para a Biblioteca de Autenticação Microsoft (MSAL) para Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696547"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guia de migração ADAL para MSAL para Python

Este artigo destaca as alterações que você precisa fazer para migrar um aplicativo que usa a Adal (Active Directory Authentication Library, biblioteca de autenticação ativa do azure) para usar a Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL trabalha com o ponto final do Azure Active Directory (Azure AD). A Microsoft Authentication Library (MSAL) funciona com a plataforma de identidade Microsoft - anteriormente conhecida como o ponto final do Azure Active Directory v2.0. A plataforma de identidade da Microsoft difere do Azure AD v1.0 na quilona:

Oferece suporte a:
  - contas corporativas e de estudante (contas do Azure AD provisionadas)
  - contas pessoais (como Outlook.com ou Hotmail.com)
  - clientes que trazem seus próprios emails ou identidades sociais (como LinkedIn, Facebook e Google) por meio da oferta do Azure AD B2C

- É compatível com padrões com:
  - OAuth v2.0
  - Conecte openid (OIDC)

Veja [O que há de diferente no ponto final da plataforma de identidade microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) para obter mais detalhes.

### <a name="scopes-not-resources"></a>Escopos não recursos

O ADAL Python adquire tokens para recursos, mas o MSAL Python adquire tokens para escopos. A superfície da API no MSAL Python não tem mais parâmetro de recurso. Você precisaria fornecer escopos como uma lista de strings que declaram as permissões e recursos desejados que são solicitados. Para ver alguns exemplos de escopos, consulte os [escopos do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Tratamento de erros

A Azure Active Directory Authentication Library (ADAL) para Python usa a exceção `AdalError` para indicar que houve um problema. O MSAL for Python normalmente usa códigos de erro. Para obter mais informações, consulte [MSAL para manipulação de erros Python](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Alterações de API

A tabela a seguir lista uma API em ADAL para Python e a que deve ser usada em seu lugar no MSAL para Python:

| ADAL para Python API  | MSAL para Python API |
| ------------------- | ---------------------------------- |
| [AutenticaçãoContexto](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [Aplicativo de cliente público ou confidencial](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/D |
| [acquire_user_code](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code( )](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials e](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache com persistência, disponível a partir de [extensões MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrar tokens de atualização existentes para O MSAL Python

A Biblioteca de Autenticação da Microsoft (MSAL) abstrai o conceito de tokens de atualização. O MSAL Python fornece um cache de token na memória por padrão para que você não precise armazenar, procurar ou atualizar tokens de atualização. Os usuários também verão menos solicitações de login porque os tokens de atualização geralmente podem ser atualizados sem a intervenção do usuário. Para obter mais informações sobre o cache de token, consulte [Serialização de cache de token personalizado no MSAL for Python](msal-python-token-cache-serialization.md).

O código a seguir ajudará você a migrar seus tokens de atualização gerenciados por outra biblioteca OAuth2 (incluindo, mas não se limitando ao ADAL Python) a serem gerenciados pelo MSAL para Python. Uma das razões para migrar esses tokens de atualização é para evitar que os usuários existentes precisem fazer login novamente quando você migrar seu aplicativo para O MSAL para Python.

O método para migrar um token de atualização é usar o MSAL para Python para adquirir um novo token de acesso usando o token de atualização anterior. Quando o novo token de atualização for devolvido, o MSAL for Python irá armazená-lo no cache. Aqui está um exemplo de como fazê-lo:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira a [comparação entre a v1.0 e a v2.0](active-directory-v2-compare.md).
