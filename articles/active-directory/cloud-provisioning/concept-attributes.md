---
title: Entenda o esquema Azure AD e expressões personalizadas
description: Este artigo descreve o esquema Azure AD, os atributos que o agente de provisionamento flui e expressões personalizadas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299322"
---
# <a name="understand-the-azure-ad-schema"></a>Entenda o esquema Azure AD
Um objeto no Azure Active Directory (Azure AD), como qualquer diretório, é um conjunto programático de dados de alto nível que representa coisas como usuários, grupos e contatos. Quando você cria um novo usuário ou contato no Azure AD, você está criando uma nova instância desse objeto. Essas instâncias podem ser diferenciadas com base em suas propriedades.

As propriedades no Azure AD são os elementos responsáveis por armazenar informações sobre uma instância de um objeto no Azure AD.

O esquema Azure AD define as regras para quais propriedades podem ser usadas em uma entrada, os tipos de valores que essas propriedades podem ter e como os usuários podem interagir com esses valores. 

O Azure AD tem dois tipos de propriedades:
- **Propriedades incorporadas**: Propriedades que são predefinidas pelo esquema Azure AD. Essas propriedades fornecem diferentes usos e podem ou não ser acessíveis.
- **Extensões de diretório**: Propriedades fornecidas para que você possa personalizar o Azure AD para seu próprio uso. Por exemplo, se você estendeu seu Diretório Ativo no local com um determinado atributo e deseja fluir esse atributo, você pode usar uma das propriedades personalizadas fornecidas. 

## <a name="attributes-and-expressions"></a>Atributos e expressões
Quando um objeto como um usuário é provisionado para o Azure AD, uma nova instância do objeto de usuário é criada. Essa criação inclui as propriedades desse objeto, que também são conhecidas como atributos. Inicialmente, o objeto recém-criado tem seus atributos definidos como valores determinados pelas regras de sincronização. Esses atributos são então mantidos atualizados através do agente de provisionamento de nuvem.

![Provisionamento de objetos](media/concept-attributes/attribute1.png)

Por exemplo, um usuário pode fazer parte de um departamento de Marketing. O atributo do departamento Azure AD é criado inicialmente quando eles são provisionados, e o valor é definido como Marketing. Seis meses depois, se eles mudarem para Vendas, seu atributo do departamento de Diretório Ativo no local é alterado para Vendas. Essa alteração sincroniza-se com o Azure AD e é refletida no objeto de usuário Azure AD.

A sincronização de atributos pode ser direta, onde o valor no Azure AD é diretamente definido para o valor do atributo on-premises. Ou, uma expressão programática pode lidar com a sincronização. Uma expressão programática é necessária nos casos em que alguma lógica ou determinação deve ser feita para preencher o valor.

Por exemplo, se você tivessejohn.smith@contoso.como atributo de@contoso.come-mail " e precisasse tirar a " " porção e fluir apenas o valor "john.smith", você usaria algo assim:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Entrada/saída de exemplo:** <br>

* **INPUT** (mail): "john.smith@contoso.com"
* **SAÍDA**: "john.smith"

Para obter mais informações sobre como escrever expressões personalizadas e a sintaxe, consulte [Escrever expressões para mapeamentos de atributos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

A tabela a seguir lista atributos comuns e como eles são sincronizados com o Azure AD.


|Active Directory local|Tipo de mapeamento|AD do Azure|
|-----|-----|-----|
|cn|Direto|Commonname
|countryCode|Direto|countryCode|
|displayName|Direto|displayName|
|givenName|Expression|givenName|
|objectGUID|Direto|fonteAnchorBinary|  
|userprincipalName|Direto|userPrincipalName|
|ProxyAdress|Direto|ProxyAddress|

## <a name="view-the-schema"></a>Ver o esquema
> [!WARNING]
> A configuração de provisionamento em nuvem cria um principal de serviço. O principal serviço é visível no portal Azure. Você não deve modificar os mapeamentos de atributos usando a experiência principal do serviço no portal Azure.  Isso não tem suporte.

Para visualizar o esquema e verificar, siga estas etapas.

1.  Vá para [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Faça login com sua conta de administrador global.
1.  À esquerda, selecione **modificar permissões** e **certifique-se de que o Diretório.ReadWrite.All** esteja *consentido*.
1.  Execute a `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`consulta . Esta consulta retorna uma lista filtrada de diretores de serviço.
1.  Localize `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` e anote o valor de `"id"`.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Substitua pelo `{Service Principal id}` seu valor e `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`execute a consulta .
1. Localize `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` e anote o valor de `"id"`.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Agora execute a `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`consulta .
 
    Exemplo: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   `{Service Principal Id}` Substitua `{AD2ADD Provisioning Id}` e com seus valores.

1. Esta consulta retorna o esquema.

   ![Esquema devolvido](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Próximas etapas

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
