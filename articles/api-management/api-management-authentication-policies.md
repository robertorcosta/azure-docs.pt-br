---
title: Políticas de autenticação do gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de autenticação disponíveis para uso no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514842"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de gerenciamento de API
Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Políticas de autenticação

-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.

-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.

-   [Autenticar com a identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) – autentique com a [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de gerenciamento de API.

##  <a name="Basic"></a>Autenticar com básico
 Use a política de `authentication-basic` para autenticar com um serviço de back-end usando a autenticação básica. Essa política define efetivamente o cabeçalho de autorização HTTP para o valor correspondente às credenciais fornecidas na política.

### <a name="policy-statement"></a>Declaração de política

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|NaME|Descrição|Obrigatório|
|----------|-----------------|--------------|
|autenticação-básica|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|NaME|Descrição|Obrigatório|Os|
|----------|-----------------|--------------|-------------|
|Usu|Especifica o nome de usuário da credencial básica.|Sim|N/D|
|Senha|Especifica a senha da credencial básica.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada

-   **Escopos de política:** todos os escopos

##  <a name="ClientCertificate"></a>Autenticar com certificado de cliente
 Use a política de `authentication-certificate` para autenticar com um serviço de back-end usando o certificado do cliente. O certificado precisa ser [instalado no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=511599) primeiro e é identificado por sua impressão digital.

### <a name="policy-statement"></a>Declaração de política

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemplos

Neste exemplo, o certificado de cliente é identificado por sua impressão digital.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Neste exemplo, o certificado de cliente é identificado pelo nome do recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|NaME|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|autenticação-certificado|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|NaME|Descrição|Obrigatório|Os|  
|----------|-----------------|--------------|-------------|  
|impressão digital|A impressão digital do certificado do cliente.|O `thumbprint` ou `certificate-id` deve estar presente.|N/D|  
|ID do certificado|O nome do recurso do certificado.|O `thumbprint` ou `certificate-id` deve estar presente.|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.  
  
-   **Seções de política:** entrada  
  
-   **Escopos de política:** todos os escopos  

##  <a name="ManagedIdentity"></a>Autenticar com identidade gerenciada  
 Use a política de `authentication-managed-identity` para autenticar com um serviço de back-end usando a identidade gerenciada do serviço de gerenciamento de API. Essa política usa basicamente a identidade gerenciada para obter um token de acesso de Azure Active Directory para acessar o recurso especificado. Depois de obter o token com êxito, a política definirá o valor do token no cabeçalho de `Authorization` usando o esquema de `Bearer`.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Usar identidade gerenciada para autenticar com um serviço de back-end
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Usar identidade gerenciada na política de solicitação de envio
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|NaME|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|autenticação-gerenciada-identidade |Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|NaME|Descrição|Obrigatório|Os|  
|----------|-----------------|--------------|-------------|  
|Kit|Strings. O URI da ID do aplicativo da API Web de destino (recurso protegido) em Azure Active Directory.|Sim|N/D|  
|saída-token-variável-nome|Strings. Nome da variável de contexto que receberá o valor de token como um tipo de objeto `string`. |Não|N/D|  
|ignorar-erro|Boolean. Se definido como `true`, o pipeline de política continuará a ser executado mesmo se um token de acesso não for obtido.|Não|false|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.  
  
-   **Seções de política:** entrada  
  
-   **Escopos de política:** todos os escopos

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas de Gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
