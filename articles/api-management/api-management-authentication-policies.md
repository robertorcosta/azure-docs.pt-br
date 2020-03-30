---
title: Políticas de autenticação de Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de autenticação disponíveis para uso no Gerenciamento de API do Azure.
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
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473550"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Políticas de autenticação

-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.

-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.

-   [Autenticar com identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) - Autenticar com a [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de gerenciamento de API.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Autenticar com o Basic
 Use a política `authentication-basic` para autenticar com um serviço de back-end usando a autenticação do Basic. Essa política define efetivamente o cabeçalho de autorização HTTP para o valor correspondente às credenciais fornecidas na política.

### <a name="policy-statement"></a>Declaração de política

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|authentication-basic|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Padrão|
|----------|-----------------|--------------|-------------|
|Nome de Usuário|Especifica o nome de usuário da credencial do Basic.|Sim|N/D|
|password|Especifica a senha da credencial do Basic.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos da política:** todos os escopos

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Autenticar com o certificado de cliente
 Use a política `authentication-certificate` para autenticar com um serviço de back-end usando um certificado de cliente. O certificado precisa ser [instalado no Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=511599) primeiro e é identificado por sua impressão digital.

### <a name="policy-statement"></a>Declaração de política

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemplos

Neste exemplo, o certificado do cliente é identificado por sua impressão digital.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Neste exemplo, o certificado do cliente é identificado pelo nome do recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|thumbprint|A impressão digital do certificado do cliente.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|  
|id certificado|O nome do recurso do certificado.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos da política:** todos os escopos  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Autenticar com identidade gerenciada  
 Use `authentication-managed-identity` a diretiva para autenticar com um serviço backend usando a identidade gerenciada do serviço de gerenciamento de API. Essa política usa essencialmente a identidade gerenciada para obter um token de acesso do Azure Active Directory para acessar o recurso especificado. Após a obtenção com sucesso do token, a `Authorization` diretiva definirá o valor do token no cabeçalho usando o `Bearer` esquema.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Use a identidade gerenciada para autenticar com um serviço backend
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
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
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Use a identidade gerenciada na diretiva de solicitação de envio
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|autenticação-identidade gerenciada |Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|recurso|Cadeia de caracteres. O ID do App da API web de destino (recurso protegido) no Azure Active Directory.|Sim|N/D|  
|nome de saída-token-variável|Cadeia de caracteres. Nome da variável de contexto que receberá o `string`valor do token como um tipo de objeto . |Não|N/D|  
|ignore-error|Booliano. Se definido `true`como , o pipeline de diretiva continuará a ser executado mesmo que um token de acesso não seja obtido.|Não|false|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos da política:** todos os escopos

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
