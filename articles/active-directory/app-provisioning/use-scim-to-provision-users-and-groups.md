---
title: Desenvolva um ponto final SCIM para provisionamento de usuários para aplicativos do Azure AD
description: O Sistema de Gerenciamento de Identidade Entre Domínios (SCIM) padroniza o provisionamento automático do usuário. Aprenda a desenvolver um ponto final do SCIM, integre sua API SCIM com o Azure Active Directory e comece a automatizar usuários e grupos de provisionamento em seus aplicativos na nuvem.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297671"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Construa um ponto final scim e configure o provisionamento do usuário com o Azure Active Directory (Azure AD)

Como desenvolvedor de aplicativos, você pode usar a API de gerenciamento de usuário do System for Cross-Domain Identity Management (SCIM) para permitir o provisionamento automático de usuários e grupos entre seu aplicativo e o Azure AD. Este artigo descreve como construir um ponto final SCIM e integrar-se ao serviço de provisionamento Azure AD. A especificação SCIM fornece um esquema de usuário comum para provisionamento. Quando usado em conjunto com padrões da federação, como SAML ou OpenID Connect, o SCIM oferece aos administradores uma solução completa e baseada em padrões para gerenciamento de acesso.

SCIM é uma definição padronizada de dois pontos finais: um ponto final /Usuários e um ponto final /Grupos. Ele usa verbos REST comuns para criar, atualizar e excluir objetos e um esquema pré-definido para atributos comuns como nome de grupo, nome de usuário, nome de primeira, sobrenome e e-mail. Aplicativos que oferecem uma API SCIM 2.0 REST podem reduzir ou eliminar a dor de trabalhar com uma API de gerenciamento de usuário proprietária. Por exemplo, qualquer cliente SCIM compatível sabe como fazer um POST HTTP de um objeto JSON para o ponto final /Usuários para criar uma nova entrada de usuário. Em vez de precisar de uma API ligeiramente diferente para as mesmas ações básicas, os aplicativos que estão em conformidade com o padrão SCIM podem tirar vantagem instantaneamente de clientes, ferramentas e códigopré-existentes. 

![Provisionamento do Azure AD para um aplicativo com SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

O esquema padrão de objetos de usuário e APIs de descanso para gerenciamento definidos no SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) permitem que provedores de identidade e aplicativos se integrem mais facilmente entre si. Os desenvolvedores de aplicativos que constroem um ponto final SCIM podem se integrar a qualquer cliente compatível com SCIM sem ter que fazer um trabalho personalizado.

Automatizar o provisionamento a um aplicativo requer a construção e a integração de um ponto final SCIM com o cliente Azure AD SCIM. Execute as seguintes etapas para iniciar o provisionamento de usuários e grupos em seu aplicativo. 
    
  * **[Passo 1: Desenhe seu esquema de usuário e grupo.](#step-1-design-your-user-and-group-schema)** Identifique os objetos e atributos das necessidades do aplicativo e determine como eles mapeiam para o esquema de usuário e grupo suportado pela implementação do Azure AD SCIM.

  * **[Passo 2: Entenda a implementação do Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Entenda como o cliente Azure AD SCIM é implementado e modele seu protocolo SCIM solicitando tratamento e respostas.

  * **[Passo 3: Construa um ponto final SCIM.](#step-3-build-a-scim-endpoint)** Um ponto final deve ser compatível com SCIM 2.0 para se integrar ao serviço de provisionamento AD do Azure. Como opção, você pode usar bibliotecas cli (Common Language Infrastructure) microsoft common language infrastructure (cli) e amostras de código para construir seu ponto final. Estas amostras são apenas para referência e testes; recomendamos contra a codificação do seu aplicativo de produção para ter uma dependência deles.

  * **[Passo 4: Integre seu ponto final SCIM com o cliente Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se sua organização estiver usando um aplicativo de terceiros que implemente o perfil do SCIM 2.0 que o Azure AD suporta, você pode começar a automatizar tanto o provisionamento quanto o desprovisionamento de usuários e grupos imediatamente.

  * **[Passo 5: Publique seu aplicativo na galeria de aplicativos do Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Facilite que os clientes descubram seu aplicativo e configurem facilmente o provisionamento. 

![Etapas para integrar um ponto final do SCIM com o Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passo 1: Projete seu esquema de usuário e grupo

Cada aplicativo requer atributos diferentes para criar um usuário ou grupo. Inicie sua integração identificando os objetos (usuários, grupos) e atributos (nome, gerente, cargo, etc.) que seu aplicativo requer. O padrão SCIM define um esquema para gerenciar usuários e grupos. O esquema do usuário principal requer apenas três atributos: **id** (identificador definido pelo provedor de serviços), **ID externo** (identificador definido pelo cliente) e **meta** (metadados somente leitura mantidos pelo provedor de serviços). Todos os outros atributos são opcionais. Além do esquema principal do usuário, o padrão SCIM define uma extensão do usuário corporativo e um modelo para estender o esquema do usuário para atender às necessidades do seu aplicativo. Se, por exemplo, seu aplicativo exigir o gerenciador de um usuário, você pode usar o esquema do usuário corporativo para coletar o gerenciador do usuário e o esquema principal para coletar o e-mail do usuário. Para projetar seu esquema, siga os passos abaixo:
  1. Liste os atributos que sua aplicação requer. Pode ser útil dividir seus requisitos nos atributos necessários para autenticação (por exemplo, loginNome e e-mail), atributos necessários para gerenciar o ciclo de vida do usuário (por exemplo, status /ativo) e outros atributos necessários para que seu aplicativo específico funcione (por exemplo, gerenciador, tag).
  2. Verifique se esses atributos já estão definidos no esquema principal do usuário ou no esquema do usuário corporativo. Se algum atributo que você precisa e não está coberto nos esquemas do usuário principal ou corporativo, você precisará definir uma extensão para o esquema do usuário que cobre os atributos que você precisa. No exemplo abaixo, adicionamos uma extensão ao usuário para permitir o provisionamento de uma "tag" em um usuário. É melhor começar apenas com os esquemas de usuário principal e corporativo e expandir para esquemas personalizados adicionais mais tarde.  
  3. Mapeie os atributos SCIM aos atributos do usuário no Azure AD. Se um dos atributos definidos no seu ponto final SCIM não tiver uma contrapartida clara sobre o esquema de usuário Azure AD, há uma boa chance de que os dados não sejam armazenados no objeto do usuário na maioria dos inquilinos. Considere se esse atributo pode ser opcional para a criação de um usuário. Se o atributo for fundamental para que seu aplicativo funcione, oriente o administrador do inquilino a estender seu esquema ou usar um atributo de extensão, conforme mostrado abaixo para a propriedade "tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabela 1: Delineie os atributos que você precisa 
| Passo 1: Determine atributos que seu aplicativo requer| Passo 2: Mapear os requisitos do aplicativo para o padrão SCIM| Passo 3: Mapear atributos SCIM aos atributos Azure AD|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|nome.lastName|lastName|
|workMail|E-mails[tipo eq "work"].value|Email|
|manager|manager|manager|
|marca|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|ativo|isSoftDeleted (valor computado não armazenado no usuário)|

O esquema definido acima seria representado usando a carga útil de Json abaixo. Observe que, além dos atributos necessários para a aplicação, a representação JSON inclui os atributos "id", "externalId" e "meta" necessários.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabela 2: Mapeamento padrão de atributos do usuário
Em seguida, você pode usar a tabela abaixo para entender como os atributos que seu aplicativo requer podem mapear para um atributo no Azure AD e no SCIM RFC. Você pode [personalizar](customize-application-attributes.md) como os atributos são mapeados entre o Azure AD e o seu ponto final SCIM. Observe que você não precisa suportar usuários e grupos ou todos os atributos mostrados abaixo. Eles são uma referência de como os atributos no Azure AD são frequentemente mapeados para propriedades no protocolo SCIM. 

| Usuário do Active Directory do Azure | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |ativo |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNúmero|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| Serviço Móvel |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| sobrenome |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabela 3: Mapeamento de atributos de grupo padrão

| Grupo do Active Directory do Azure | urn:ietf:params:scim:schemas:core:2.0:Grupo |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membros |membros |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Existem vários pontos finais definidos no SCIM RFC. Você pode começar com o ponto final /Usuário e, em seguida, expandir a partir daí. O ponto final /Schemas é útil ao usar atributos personalizados ou se seu esquema muda com freqüência. Ele permite que um cliente recupere o esquema mais atualizado automaticamente. O ponto final /Bulk é especialmente útil ao apoiar grupos. A tabela abaixo descreve os vários pontos finais definidos no padrão SCIM. O ponto final /Schemas é útil ao usar atributos personalizados ou se seu esquema muda com freqüência. Ele permite que um cliente recupere o esquema mais atualizado automaticamente. O ponto final /Bulk é especialmente útil ao apoiar grupos. A tabela abaixo descreve os vários pontos finais definidos no padrão SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabela 4: Determine os pontos finais que você gostaria de desenvolver
|ENDPOINT|DESCRIPTION|
|--|--|
|/user|Execute operações CRUD em um objeto de usuário.|
|/Group|Execute operações CRUD em um objeto de grupo.|
|/ServiceProviderConfig|Fornece detalhes sobre os recursos da norma SCIM que são suportados, por exemplo, os recursos suportados e o método de autenticação.|
|/Tipos de recursos|Especifica metadados sobre cada recurso|
|/Schemas|O conjunto de atributos suportados por cada cliente e prestador de serviços pode variar. Enquanto um provedor de serviços pode incluir "nome", "título" e "e-mails", enquanto outro provedor de serviços usa "nome", "título" e "números de telefone". O ponto final de esquema permite a descoberta dos atributos suportados.|
|/Granel|As operações em massa permitem que você execute operações em uma grande coleção de objetos de recursos em uma única operação (por exemplo, atualizar adesões para um grande grupo).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passo 2: Entenda a implementação do Azure AD SCIM
> [!IMPORTANT]
> O comportamento da implementação do SCIM do Azure AD foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte [Conformidade do protocolo SCIM 2.0 do serviço de provisionamento de usuário do Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se você estiver construindo um aplicativo que suporte uma API de gerenciamento de usuários SCIM 2.0, esta seção descreve em detalhes como o cliente Azure AD SCIM é implementado. Ele também mostra como modelar o seu protocolo SCIM solicitando tratamento e respostas. Depois de implementar seu ponto final SCIM, você pode testá-lo seguindo o procedimento descrito na seção anterior.

Dentro da especificação do [protocolo SCIM 2.0,](http://www.simplecloud.info/#Specification)seu aplicativo deve atender a esses requisitos:

* Suporta a criação de usuários e, opcionalmente, também grupos, conforme a seção [3.3 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Suporta modificar usuários ou grupos com solicitações PATCH, conforme [a seção 3.5.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Suporta recuperar um recurso conhecido para um usuário ou grupo criado anteriormente, conforme [a seção 3.4.1 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Suporta consulta de usuários ou grupos, conforme a seção [3.4.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os usuários `id` são recuperados por `username` eles `externalid`e questionados por `displayName`seus e , e os grupos são consultados por .  
* Suporta consulta ao usuário por ID e por gerente, conforme a seção 3.4.2 do protocolo SCIM.  
* Suporta grupos de consulta por ID e por membro, conforme seção 3.4.2 do protocolo SCIM.  
* Aceita um único token portador para autenticação e autorização do Azure AD para o seu aplicativo.

Siga essas diretrizes gerais ao implementar um ponto final Do SCIM para garantir a compatibilidade com o Azure AD:

* `id`é uma propriedade necessária para todos os recursos. Cada resposta que retorna um recurso deve garantir `ListResponse` que cada recurso tenha essa propriedade, exceto com zero membros.
* A resposta a uma consulta/solicitação `ListResponse`de filtro deve ser sempre uma .
* Os grupos são opcionais, mas só suportados se a implementação do SCIM suportar solicitações de PATCH.
* Não é necessário incluir todo o recurso na resposta patch.
* O Microsoft Azure AD só usa as seguintes operadoras:  
    - `eq`
    - `and`
* Não exija uma correspondência sensível a maiúsculas e `op` minúsculas sobre https://tools.ietf.org/html/rfc7644#section-3.5.2elementos estruturais no SCIM, em determinados valores de operação PATCH, conforme definido em . Aazure AD emite os `Add`valores de 'op' como , `Replace`e `Remove`.
* O Microsoft Azure AD faz solicitações para buscar um usuário e grupo aleatórios para garantir que o ponto final e as credenciais sejam válidos. Também é feito como parte do fluxo de **conexão** de teste no [portal Azure](https://portal.azure.com). 
* O atributo em que os recursos podem ser consultados deve ser definido como um atributo correspondente na aplicação no [portal Azure](https://portal.azure.com). Para obter mais informações, consulte [Personalização de mapeamentos de atributos de provisionamento de usuários](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Provisionamento e desprovisionamento de usuários

A ilustração a seguir mostra as mensagens que o Azure Active Directory envia a um serviço SCIM para gerenciar o ciclo de vida de um usuário na loja de identidade do seu aplicativo.  

![Mostra a seqüência de provisionamento e desprovisionamento do usuário](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Seqüência de provisionamento e desprovisionamento do usuário*

### <a name="group-provisioning-and-deprovisioning"></a>Provisionamento e desprovisionamento de grupos

O provisionamento e o desprovisionamento do grupo são opcionais. Quando implementada e ativada, a ilustração a seguir mostra as mensagens que o Azure AD envia a um serviço SCIM para gerenciar o ciclo de vida de um grupo no armazenamento de identidade do aplicativo.  Essas mensagens diferem das mensagens sobre os usuários de duas maneiras:

* Solicitações para recuperar grupos especificam que o atributo dos membros deve ser excluído de qualquer recurso fornecido em resposta à solicitação.  
* As solicitações para determinar se um atributo de referência tem um determinado valor são sobre o atributo de membros.  

![Mostra a seqüência de provisionamento e desprovisionamento do grupo](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequência de provisionamento e desprovisionamento de grupo*

### <a name="scim-protocol-requests-and-responses"></a>Solicitações e respostas do protocolo SCIM
Esta seção fornece exemplo de solicitações SCIM emitidas pelo cliente Azure AD SCIM e exemplos de respostas esperadas. Para obter melhores resultados, você deve codificar seu aplicativo para lidar com essas solicitações neste formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para entender como e quando o serviço de provisionamento de usuários Azure AD emite as operações descritas abaixo, consulte a seção [Ciclos de provisionamento: Inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em [Como funciona o provisionamento](how-provisioning-works.md).

[Operações de Usuário](#user-operations)
  - [Criar usuário](#create-user) [(resposta de solicitação)](#request) / [Response](#response)
  - [Obter usuário](#get-user) [(resposta de solicitação)](#request-1) / [Response](#response-1)
  - [Obter usuário por consulta](#get-user-by-query) [(Resposta de solicitação)](#request-2) / [Response](#response-2)
  - [Obter usuário por consulta - Resultados zero](#get-user-by-query---zero-results) [(Resposta de solicitação)](#request-3)
/ [Response](#response-3)
  - [Atualizar o usuário [propriedades multivalorizadas]](#update-user-multi-valued-properties) [(Resposta de](#response-4)[solicitação)](#request-4) /  
  - [Atualizar o usuário [propriedades de valor único]](#update-user-single-valued-properties) [(Resposta de](#response-5)[solicitação)](#request-5)
/  
  - [Desativar o usuário](#disable-user) [(Resposta de solicitação)](#request-14) / 
[Response](#response-14)
  - [Excluir usuário](#delete-user) [(Resposta de](#response-6)[solicitação)](#request-6) / 



[Operações em Grupo](#group-operations)
  - [Criar grupo](#create-group) [(resposta de](#response-7) [solicitação)](#request-7) / 
  - [Obter grupo](#get-group) [(resposta de](#response-8) [solicitação)](#request-8) / 
  - [Obter grupo por displayName](#get-group-by-displayname) [(Resposta de](#request-9) / [solicitação)](#response-9)
  - [Grupo de atualização [atributos não-membros]](#update-group-non-member-attributes) [(Resposta de](#response-10)[solicitação)](#request-10) /
 
  - [Grupo de atualização [Adicionar membros]](#update-group-add-members) [(Resposta de](#response-11) [solicitação)](#request-11) /

  - [Grupo de atualização [Remover membros]](#update-group-remove-members) [(Resposta de](#response-12) [solicitação)](#request-12) /

  - [Excluir grupo](#delete-group) [(Resposta de](#response-13)[solicitação)](#request-13) /


### <a name="user-operations"></a>Operações de Usuário

* Os usuários podem ser `userName` `email[type eq "work"]` consultados por ou atributos.  

#### <a name="create-user"></a>Criar Usuário

###### <a name="request"></a>Solicitação

*POST /Usuários*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Resposta

*HTTP/1.1 201 Criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Obter usuário

###### <a name="request"></a><a name="request-1"></a>Solicitação
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Resposta (Usuário encontrado)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Solicitação
*GET /Usuários/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Resposta (Usuário não encontrado. Observe que o detalhe não é necessário, apenas status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Obter usuário por consulta

##### <a name="request"></a><a name="request-2"></a>Solicitação

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obter usuário por consulta - Resultados zero

##### <a name="request"></a><a name="request-3"></a>Solicitação

*GET /Users?filter=userName eq "usuário inexistente"*

##### <a name="response"></a><a name="response-3"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Atualizar o usuário [propriedades multivalorizadas]

##### <a name="request"></a><a name="request-4"></a>Solicitação

*PATCH /Usuários/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Atualizar o usuário [propriedades de valor único]

##### <a name="request"></a><a name="request-5"></a>Solicitação

*PATCH /Usuários/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Desativar o usuário

##### <a name="request"></a><a name="request-14"></a>Solicitação

*PATCH /Usuários/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Resposta

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Excluir usuário

##### <a name="request"></a><a name="request-6"></a>Solicitação

*DELETE /Usuários/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="group-operations"></a>Operações em Grupo

* Os grupos devem ser sempre criados com uma lista de membros vazias.
* Grupos podem ser consultados `displayName` pelo atributo.
* A atualização para a solicitação de PATCH do grupo deve render um *HTTP 204 No Content* na resposta. Devolver um corpo com uma lista de todos os membros não é aconselhável.
* Não é necessário apoiar o retorno de todos os membros do grupo.

#### <a name="create-group"></a>Criar Grupo

##### <a name="request"></a><a name="request-7"></a>Solicitação

*POST /Grupos HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Resposta

*HTTP/1.1 201 Criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obter Grupo

##### <a name="request"></a><a name="request-8"></a>Solicitação

*GET /Groups/40734ae655284ad3abcc?excluídoS=membros HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Resposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obter grupo por displayNome

##### <a name="request"></a><a name="request-9"></a>Solicitação
*GET /Groups?excluídosAtributos=membros&filtro=displayNome eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Grupo de atualização [Atributos não-membros]

##### <a name="request"></a><a name="request-10"></a>Solicitação

*PATCH /Grupos/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="update-group-add-members"></a>Grupo de atualização [Adicionar membros]

##### <a name="request"></a><a name="request-11"></a>Solicitação

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="update-group-remove-members"></a>Grupo de atualização [Remover membros]

##### <a name="request"></a><a name="request-12"></a>Solicitação

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="delete-group"></a>Excluir grupo

##### <a name="request"></a><a name="request-13"></a>Solicitação

*EXCLUSÃO /Grupos/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="security-requirements"></a>Requisitos de segurança
**Versões do protocolo TLS**

As únicas versões de protocolo TLS aceitáveis são o TLS 1.2 e o TLS 1.3. Não são permitidas outras versões de TLS. Nenhuma versão do SSL é permitida. 
- As teclas RSA devem ser de pelo menos 2.048 bits.
- As teclas ECC devem ser de pelo menos 256 bits, gerados usando uma curva elíptica aprovada


**Comprimentos da chave**

Todos os serviços devem usar certificados X.509 gerados usando chaves criptográficas de comprimento suficiente, o que significa:

**Suítes Cipher**

Todos os serviços devem ser configurados para usar as seguintes suítes de cifra, na ordem exata especificada abaixo. Observe que se você tiver apenas um certificado RSA, as suítes de cifras ECDSA não terão nenhum efeito. </br>

Barra mínima do TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Passo 3: Construa um ponto final SCIM

Agora que você projetou seu esquema e entendeu a implementação do Azure AD SCIM, você pode começar a desenvolver seu ponto final SCIM. Em vez de começar do zero e construir a implementação completamente por conta própria, você pode contar com uma série de bibliotecas SCIM de código aberto publicadas pela comunidade SCIM.

O código de [referência](https://aka.ms/SCIMReferenceCode) de código aberto .NET Core publicado pela equipe de provisionamento Azure AD é um desses recursos que podem impulsionar o seu desenvolvimento. Depois de ter construído seu ponto final SCIM, você vai querer testá-lo. Você pode usar a coleção de testes de [carteiro](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos como parte do código de referência ou executar através das solicitações/respostas da amostra fornecidas [acima](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > O código de referência destina-se a ajudá-lo a começar a construir seu ponto final SCIM e é fornecido "AS IS". Contribuições da comunidade são bem-vindas para ajudar a construir e manter o código.

A solução é composta por dois projetos, _Microsoft.SCIM_ e _Microsoft.SCIM.WebHostSample_.

O projeto _Microsoft.SCIM_ é a biblioteca que define os componentes do serviço web que estão em conformidade com a especificação SCIM. Ele declara a interface _Microsoft.SCIM.IProvider_, as solicitações são traduzidas em chamadas para os métodos do provedor, que seriam programadas para operar em um armazenamento de identidade.

![Decomposição: Uma solicitação traduzida em chamadas para os métodos do provedor](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

O projeto _Microsoft.SCIM.WebHostSample_ é um Visual Studio ASP.NET Web Application, baseado no modelo _Empty._ Isso permite que o código de amostra seja implantado como autônomo, hospedado em contêineres ou dentro dos Serviços de Informação da Internet. Ele também implementa a interface _Microsoft.SCIM.IProvider_ mantendo as classes na memória como um armazenamento de identidade de exemplo.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Construindo um ponto final SCIM personalizado

O serviço SCIM deve ter um certificado de autenticação de endereço e servidor HTTP do qual a autoridade de certificação raiz é um dos seguintes nomes:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

O .NET Core SDK inclui um certificado de desenvolvimento HTTPS que pode ser usado durante o desenvolvimento, o certificado é instalado como parte da experiência de primeira execução. Dependendo de como você executa o ASP.NET Aplicativo Web Principal, ele ouvirá uma porta diferente:

* Microsoft.SCIM.WebHostSample:https://localhost:5001
* IIS Express:https://localhost:44359/

Para obter mais informações sobre HTTPS no ASP.NET Core, use o seguinte link: [Impor HTTPS no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Manipulando a autenticação do ponto de extremidade

As solicitações do Active Directory do Azure incluem um token de portador do OAuth 2.0. Qualquer serviço que receba a solicitação deve autenticar o emissor como sendo o Azure Active Directory para o esperado inquilino do Azure Active Directory.

No token, o emissor é identificado por uma `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`reivindicação da ISS, como . Neste exemplo, o endereço base do `https://sts.windows.net`valor de sinistro, identifica o Azure Active Directory como o emissor, enquanto o segmento de endereço relativo, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422,_ é um identificador único do inquilino do Azure Active Directory para o qual o token foi emitido.

O público-alvo do token será o ID do modelo de aplicativo para o aplicativo na galeria, cada um dos aplicativos registrados em um único inquilino poderá receber a mesma `iss` reclamação com solicitações SCIM. O ID do modelo de inscrição para cada [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) aplicativo na galeria varia, entre em contato para perguntas sobre o ID do modelo do aplicativo para um aplicativo de galeria. O ID do modelo de aplicativo para todos os aplicativos personalizados é _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

No código de exemplo, as solicitações são autenticadas usando o pacote Microsoft.AspNetCore.Authentication.JwtBearer. O código a seguir impõe que as solicitações a qualquer um dos pontos finais do serviço sejam autenticadas usando o token do portador emitido pelo Azure Active Directory para um inquilino especificado:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Um token portador também é necessário para usar os testes de [carteiro fornecidos](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) e realizar depuração local usando host local. O código de exemplo usa ambientes ASP.NET Core para alterar as opções de autenticação durante o estágio de desenvolvimento e permitir o uso de um token auto-assinado.

Para obter mais informações sobre vários ambientes no ASP.NET Core, use o seguinte link: [Use vários ambientes no ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

O código a seguir impõe que as solicitações a qualquer um dos pontos finais do serviço são autenticadas usando um token do portador assinado com uma chave personalizada:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Envie uma solicitação GET ao controlador Token para obter um token portador válido, o método _GenerateJSONWebToken_ é responsável por criar um token correspondente aos parâmetros configurados para o desenvolvimento:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Manuseio de provisionamento e desprovisionamento de usuários

***Exemplo 1. Consultar o serviço para um usuário correspondente***

O Azure Active Directory consulta o serviço para procurar um usuário com um valor de atributo externalId correspondente ao valor de atributo mailNickname de um usuário no Azure AD. A consulta é expressa como uma solicitação de Protocolo de Transferência de Hipertexto (HTTP), como este exemplo, em que jyoung é uma amostra de um mailNickname de um usuário no Azure Active Directory.

>[!NOTE]
> Este é apenas um exemplo. Nem todos os usuários terão um atributo mailNickname, e o valor que um usuário tem pode não ser único no diretório. Além disso, o atributo usado para correspondência (que neste caso é externoId) é configurável nos mapeamentos de [atributos Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

No código de amostra, a solicitação é traduzida em uma chamada para o método QueryAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

Na consulta de amostra, para um usuário com um determinado valor para o atributo externoId, os valores dos argumentos passados para o método QueryAsync são:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Exemplo 2. Provisão de um usuário***

Se a resposta a uma consulta ao serviço web para um usuário com um valor de atributo externo que corresponde ao valor de atributo mailNickname de um usuário não retornar nenhum usuário, então o Azure Active Directory solicitará que a prestação de serviço satisfaça um usuário correspondente ao no Azure Active Directory.  Veja um exemplo de tal solicitação: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

No código de amostra, a solicitação é traduzida em uma chamada para o método CreateAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

Em uma solicitação de provisionamento de um usuário, o valor do argumento de recurso é uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, definida na biblioteca Microsoft.SCIM.Schemas.  Se a solicitação de provisão do usuário for bem-sucedida, espera-se que a implementação do método retorne uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, com o valor da propriedade Identificador definido para o identificador exclusivo do recém-provisionado Usuário.  

***Exemplo 3. Consultar o estado atual de um usuário*** 

Para atualizar um usuário conhecido que existe em um repositório de identidades administrado por um SCIM, o Azure Active Directory prossegue solicitando o estado atual desse usuário no serviço com uma solicitação como: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

No código de amostra, a solicitação é traduzida em uma chamada para o método RetrieveAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

No exemplo de uma solicitação para recuperar o estado atual de um usuário, os valores das propriedades do objeto fornecidos como o valor do argumento de parâmetros são: 
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Exemplo 4. Consulte o valor de um atributo de referência a ser atualizado*** 

Se um atributo de referência for atualizado, o Azure Active Directory consulta o serviço para determinar se o valor atual do atributo de referência no armazenamento de identidade frontal ao serviço já corresponde ao valor desse atributo no Azure Active Diretório. Para usuários, o único atributo no qual o valor atual é consultado dessa forma é o atributo de gerenciador. Aqui está um exemplo de uma solicitação para determinar se o atributo gerenciador de um objeto de usuário atualmente tem um determinado valor: No código de amostra, a solicitação é traduzida em uma chamada para o método QueryAsync do provedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento de parâmetros é: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro de consulta do filtro.   

***Exemplo 5. Solicitação do Azure AD a um serviço SCIM para atualizar um usuário*** 

Veja um exemplo de uma solicitação do Azure Active Directory a um serviço SCIM para atualizar um usuário: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

No código de exemplo, a solicitação é traduzida em uma chamada para o método UpdateAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

No exemplo de uma solicitação para atualizar um usuário, o objeto fornecido como valor do argumento de patch tem estes valores de propriedade: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***Exemplo 6. Deprovisiona um usuário***

Para desprovisionar um usuário de uma loja de identidade frontal a um serviço SCIM, o Azure AD envia uma solicitação como:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

No código de exemplo, a solicitação é traduzida em uma chamada para o método DeleteAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

O objeto fornecido como o valor do argumento resourceIdentifier tem esses valores de propriedade no exemplo de uma solicitação para desprovisionar um usuário: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passo 4: Integre seu ponto final SCIM com o cliente Azure AD SCIM

O Azure AD pode ser configurado para provisionar automaticamente usuários e grupos atribuídos a aplicativos que implementam um perfil específico do [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). As especificidades do perfil estão documentadas na [Etapa 2: Entenda a implementação do Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Verifique com o seu provedor de aplicativo ou na documentação do seu provedor de aplicativo as declarações de compatibilidade com esses requisitos.

> [!IMPORTANT]
> A implementação do Azure AD SCIM é construída em cima do serviço de provisionamento de usuários Azure AD, que foi projetado para manter os usuários constantemente em sincronia entre o Azure AD e o aplicativo-alvo, e implementa um conjunto muito específico de operações padrão. É importante entender esses comportamentos para entender o comportamento do cliente Azure AD SCIM. Para obter mais informações, consulte os [ciclos de provisionamento](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) da seção: Inicial e incremental em [Como funciona o provisionamento](how-provisioning-works.md).

### <a name="getting-started"></a>Introdução

Os aplicativos que dão suporte ao perfil SCIM descrito neste artigo podem ser conectados ao Azure Active Directory usando o recurso de "aplicativo não galeria" na galeria de aplicativos do Azure AD. Uma vez conectado, o Azure AD executa um processo de sincronização a cada 40 minutos, em que ele consulta o ponto de extremidade SCIM do aplicativo para os usuários e grupos atribuídos e os cria ou modifica de acordo com os detalhes da atribuição.

**Para conectar um aplicativo que dê suporte a SCIM:**

1. Faça login no portal do Diretório Ativo do [Azure](https://aad.portal.azure.com). Observe que você pode obter acesso a uma avaliação gratuita para o Azure Active Directory com licenças P2, inscrevendo-se para o [programa de desenvolvedor](https://developer.microsoft.com/office/dev-program)
2. Selecione **aplicações Enterprise** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo aplicativos que foram adicionados da galeria.
3. Selecione **+ Novo aplicativo** > **Todos os** > **aplicativos não-galeria**.
4. Digite um nome para o seu aplicativo e selecione **Adicionar** para criar um objeto de aplicativo. O novo aplicativo é adicionado à lista de aplicativos corporativos e abre para sua tela de gerenciamento de aplicativos.

   ![Captura de tela mostra a galeria de aplicativos do Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galeria de aplicativos Azure AD*

5. Na tela de gerenciamento de aplicativos, **selecione Provisionamento** no painel esquerdo.
6. No menu **Modo de Provisionamento**, selecione **Automático**.

   ![Exemplo: A página de provisionamento de um aplicativo no portal Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configuração do provisionamento no portal Azure*

7. No campo **URL do locatário** , insira a URL do ponto de extremidade do SCIM do aplicativo. Exemplo: `https://api.contoso.com/scim/`
8. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se este campo for deixado em branco, o Azure AD inclui um token de portador OAuth emitido do Azure AD a cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD. 
   > [!NOTE]
   > Não ***é*** recomendável deixar este campo em branco e confiar em um token gerado pelo Azure AD. Esta opção está disponível principalmente para fins de teste.
9. Selecione **Conexão de teste** para que o Azure Active Directory tente se conectar ao ponto final do SCIM. Se a tentativa falhar, as informações de erro são exibidas.  

    > [!NOTE]
    > **Testar Conexão** consulta o ponto de extremidade SCIM para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem de SCIM ListResponse vazia.

10. Se as tentativas de conexão ao aplicativo forem bem sucedidas, selecione **Salvar** para salvar as credenciais de admin.
11. Na seção **Mapeamentos,** há dois conjuntos selecionáveis de mapeamentos de [atributos](customize-application-attributes.md): um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    > [!NOTE]
    > Opcionalmente, você pode desabilitar a sincronização dos objetos de grupo desabilitando o mapeamento "grupos".

12. Em **Configurações**, o campo **Escopo** define quais usuários e grupos são sincronizados. Selecione **Sincronizar apenas usuários e grupos** (recomendados) para sincronizar apenas usuários e grupos atribuídos na guia Usuários e **grupos.**
13. Uma vez que sua configuração esteja concluída, defina o **status de provisionamento** como **"On**"
14. Selecione **Salvar** para iniciar o serviço de provisionamento Azure AD.
15. Se sincronizar apenas usuários e grupos atribuídos (recomendado), certifique-se de selecionar a guia **Usuários e grupos** e atribuir os usuários ou grupos que deseja sincronizar.

Uma vez iniciado o ciclo inicial, você pode selecionar **registros de provisionamento** no painel esquerdo para monitorar o progresso, que mostra todas as ações feitas pelo serviço de provisionamento em seu aplicativo. Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](check-status-user-account-provisioning.md).

> [!NOTE]
> O ciclo inicial leva mais tempo para ser executado do que sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passo 5: Publique seu aplicativo na galeria de aplicativos do Azure AD

Se você estiver construindo um aplicativo que será usado por mais de um inquilino, você pode disponibilizá-lo na galeria de aplicativos do Azure AD. Isso facilitará que as organizações descubram o aplicativo e configurem o provisionamento. Publicar seu aplicativo na galeria Azure AD e disponibilizar provisionamento para outros é fácil. Confira as etapas [aqui](../develop/howto-app-gallery-listing.md). A Microsoft trabalhará com você para integrar seu aplicativo em nossa galeria, testar seu ponto final e liberar [documentação](../saas-apps/tutorial-list.md) de onboarding para os clientes usarem. 

### <a name="gallery-onboarding-checklist"></a>Lista de verificação de onboarding da galeria
Siga a lista de verificação abaixo para garantir que seu aplicativo esteja a bordo rapidamente e que os clientes tenham uma experiência de implantação suave. As informações serão coletadas de você ao embarcar para a galeria. 
> [!div class="checklist"]
> * Suporte a um usuário [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) e ponto final de grupo (apenas um é necessário, mas ambos são recomendados)
> * Suporte pelo menos 25 solicitações por segundo por inquilino (Obrigatório)
> * Estabeleça contatos de engenharia e suporte para orientar os clientes após o onboarding da galeria (Obrigatório)
> * 3 Credenciais de teste que não expiram para sua aplicação (Obrigatório)
> * Apoie a concessão do código de autorização OAuth ou um token de longa duração conforme descrito abaixo (Obrigatório)
> * Estabeleça um ponto de contato de engenharia e suporte para apoiar os clientes após o onboarding da galeria (Obrigatório)
> * Suporte à atualização de várias associações de grupo com um único PATCH (Recomendado) 
> * Documente seu ponto final SCIM publicamente (Recomendado) 
> * [Detecção de esquema de suporte](https://tools.ietf.org/html/rfc7643#section-6) (Recomendado)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorização para provisionamento de conectores na galeria de aplicativos
A especificação SCIM não define um esquema específico do SCIM para autenticação e autorização. Ele se baseia no uso dos padrões existentes da indústria. O cliente de provisionamento Azure AD suporta dois métodos de autorização para aplicativos na galeria. 

|Método de autorização|Vantagens|Desvantagens|Suporte|
|--|--|--|--|
|Nome de usuário e senha (não recomendado ou suportado pelo Azure AD)|Fácil de implementar|Inseguro - [Seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Suportado caso a caso para aplicativos de galeria. Não suportado para aplicativos que não são de galeria.|
|Token de portador de longa duração|Os tokens de longa duração não exigem que um usuário esteja presente. Eles são fáceis de usar para os admins ao configurar o provisionamento.|Tokens de longa duração podem ser difíceis de compartilhar com um admin sem usar métodos inseguros, como e-mail. |Suportado para aplicativos de galeria e não-galeria. |
|Concessão do código de autorização OAuth|Os tokens de acesso são muito mais curtos do que as senhas, e têm um mecanismo de atualização automatizado que os tokens portadores de longa duração não têm.  Um usuário real deve estar presente durante a autorização inicial, adicionando um nível de responsabilidade. |Requer que um usuário esteja presente. Se o usuário deixar a organização, o token é inválido e a autorização precisará ser concluída novamente.|Suportado para aplicativos de galeria. O suporte para aplicativos que não são de galeria está em andamento.|
|Concessão de credenciais de cliente OAuth|Os tokens de acesso são muito mais curtos do que as senhas, e têm um mecanismo de atualização automatizado que os tokens portadores de longa duração não têm. Tanto a concessão do código de autorização quanto a concessão de credenciais do cliente criam o mesmo tipo de token de acesso, portanto, mover-se entre esses métodos é transparente para a API.  O provisionamento pode ser completamente automatizado, e novos tokens podem ser solicitados silenciosamente sem a interação do usuário. ||Não suportado para aplicativos de galeria e não-galeria. O suporte está em nosso backlog.|

[!NOTE] Não é recomendável deixar o campo de token em branco na configuração de provisionamento AD do Azure. O token gerado está disponível principalmente para fins de teste.

**Fluxo de concessão de código de autorização OAuth:** O serviço de provisionamento suporta a concessão do [código de autorização.](https://tools.ietf.org/html/rfc6749#page-24) Depois de enviar sua solicitação para publicar seu aplicativo na galeria, nossa equipe trabalhará com você para coletar as seguintes informações:
*  URL de autorização: uma URL do cliente para obter autorização do proprietário do recurso através do redirecionamento usuário-agente. O usuário é redirecionado para esta URL para autorizar o acesso. 
*  URL da exchange de token: uma URL do cliente para trocar uma concessão de autorização por um token de acesso, normalmente com autenticação do cliente.
*  ID do cliente: O servidor de autorização emite ao cliente registrado um identificador de cliente, que é uma seqüência única representando as informações de registro fornecidas pelo cliente.  O identificador do cliente não é um segredo; ele é exposto ao proprietário do recurso e **não deve** ser usado sozinho para autenticação do cliente.  
*  Segredo do cliente: O segredo do cliente é um segredo gerado pelo servidor de autorização. Deve ser um valor único conhecido apenas para o servidor de autorização. 

Note que o OAuth v1 não é suportado devido à exposição do segredo do cliente. OAuth v2 é suportado.  

Melhores práticas (recomendadas, mas não necessárias):
* Suporte a vários URLs de redirecionamento. Os administradores podem configurar o provisionamento tanto de "portal.azure.com" quanto de "aad.portal.azure.com". O suporte a vários URLs de redirecionamento garantirá que os usuários possam autorizar o acesso a partir de qualquer portal.
* Apoie vários segredos para garantir uma renovação secreta suave, sem tempo de inatividade. 

**Tokens de portador de OAuth de longa duração:** Se o seu aplicativo não suportar o fluxo de concessão de código de autorização OAuth, você também pode gerar um token de portador OAuth de longa duração do que um administrador pode usar para configurar a integração de provisionamento. O token deve ser perpétuo, ou então o trabalho de provisionamento será [colocado em quarentena](application-provisioning-quarantine-status.md) quando o token expirar. Este token deve estar abaixo de 1KB de tamanho.  

Para obter métodos adicionais de autenticação e autorização, deixe-nos saber no [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista de verificação de lançamento da galeria
Para ajudar a promover a conscientização e a demanda de nossa integração conjunta, recomendamos que você atualize sua documentação existente e amplie a integração em seus canais de marketing.  O abaixo é um conjunto de atividades de checklist que recomendamos que você complete para suportar o lançamento

* **Prontidão para vendas e suporte ao cliente.** Certifique-se de que suas equipes de vendas e suporte estejam cientes e possam falar com os recursos de integração. Informe sua equipe de vendas e suporte, forneça-lhes perguntas frequentes e inclua a integração em seus materiais de vendas. 
* **Post no blog e/ou comunicado de imprensa.** Crie um post no blog ou um comunicado de imprensa que descreva a integração conjunta, os benefícios e como começar. [Exemplo: Comunicado de imprensa do Diretório Ativo imprivata e azure](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Mídias sociais.** Aproveite suas mídias sociais como Twitter, Facebook ou LinkedIn para promover a integração com seus clientes. Certifique-se @AzureAD de incluir para que possamos retweetar seu post. [Exemplo: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Site de marketing.** Crie ou atualize suas páginas de marketing (por exemplo, página de integração, página de parceiros, página de preços, etc.) para incluir a disponibilidade da integração conjunta. [Exemplo: Página de integração pingboard,](https://pingboard.com/org-chart-for) [página de integração do Smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [página de preços Monday.com](https://monday.com/pricing/) 
* **Documentação técnica.** Crie um artigo de centro de ajuda ou documentação técnica sobre como os clientes podem começar. [Exemplo: Emissária + Integração do Diretório Ativo do Microsoft Azure.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Comunicação com o cliente.** Alerte os clientes sobre a nova integração através da comunicação do cliente (boletins mensais, campanhas de e-mail, notas de lançamento de produtos). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Permitir endereços IP usados pelo serviço de provisionamento AD do Azure para fazer solicitações SCIM

Certos aplicativos permitem tráfego de entrada em seu aplicativo. Para que o serviço de provisionamento Azure AD funcione conforme o esperado, os endereços IP utilizados devem ser permitidos. Para obter uma lista de endereços IP para cada tag de serviço/região, confira o arquivo JSON – [Intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). Você pode baixar e programar esses IPs em seu firewall conforme necessário. As faixas de IP reservadas para provisionamento Azure AD podem ser encontradas em "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Artigos relacionados

* [Automatize o provisionamento e o desprovisionamento do usuário para aplicativos SaaS](user-provisioning.md)
* [Personalizar mapeamentos de atributos para provisionamento do usuário](customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
