---
title: API REST do Azure Active Directory-autenticação
description: Usar Azure Active Directory para autenticar para Azure App configuração usando a API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423874"
---
# <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

As solicitações HTTP podem ser autenticadas usando o esquema de autenticação de **portador** com um token adquirido do Azure Active Directory (AD do Azure). Essas solicitações devem ser transmitidas por TLS.

## <a name="prerequisites"></a>Pré-requisitos

A entidade de segurança que será usada para solicitar um token do Azure AD deve ser atribuída a uma das [funções de configuração de aplicativo](./rest-api-authorization-azure-ad.md) aplicáveis

Forneça cada solicitação com todos os cabeçalhos HTTP necessários para autenticação. O mínimo necessário são:

|  Cabeçalho da solicitação | Descrição  |
| --------------- | ------------ |
| **Autorização** | Informações de autenticação exigidas pelo esquema de **portador** . O formato e os detalhes são explicados abaixo. |

**Exemplo:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Aquisição de token Azure Active Directory

Antes de adquirir um token do Azure AD, é necessário identificar o usuário para o qual deseja autenticar, para qual público ele está solicitando o token e qual ponto de extremidade do AD do Azure (autoridade) eles devem usar.

### <a name="audience"></a>Público

O token do Azure AD deve ser solicitado com um público-alvo adequado. Para Azure App configuração, um dos públicos a seguir deve ser especificado ao solicitar um token. O público-alvo também pode ser chamado de "recurso" para o qual o token está sendo solicitado.

- {configurationStoreName}. azconfig. Io
- *. azconfig.io

> [!IMPORTANT]
> Quando o público-alvo solicitado é {configurationStoreName}. azconfig. Io, ele deve corresponder exatamente ao cabeçalho de solicitação "host" (diferencia maiúsculas de minúsculas) usado para enviar a solicitação.

### <a name="azure-ad-authority"></a>Autoridade do Azure AD

A autoridade do Azure AD é o ponto de extremidade usado para adquirir um token do Azure AD. Ele está na forma de `https://login.microsoftonline.com/{tenantId}` . O `{tenantId}` segmento refere-se à ID de locatário Azure Active Directory à qual o usuário/aplicativo que está tentando autenticar pertence.

### <a name="authentication-libraries"></a>Bibliotecas de autenticação

O Azure fornece um conjunto de bibliotecas chamadas de ADAL (bibliotecas de autenticação Azure Active Directory) para simplificar o processo de aquisição de um token do Azure AD. Essas bibliotecas são criadas para vários idiomas. A documentação pode ser encontrada [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Erros**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Motivo:** O cabeçalho de solicitação de autorização com esquema de portador não foi fornecido.
**Solução:** Fornecer um ```Authorization``` cabeçalho de solicitação HTTP válido

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Motivo:** O token do Azure AD não é válido.
**Solução:** Adquirir um token do Azure AD da autoridade do Azure AD e garantir que o público-alvo adequado seja usado.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Motivo:** O token do Azure AD não é válido.
**Solução:** Adquira um token do Azure AD da autoridade do Azure AD e verifique se o locatário do Azure AD é aquele associado à assinatura, ao qual o repositório de configuração pertence. Esse erro pode aparecer se a entidade pertencer a mais de um locatário do Azure AD.
