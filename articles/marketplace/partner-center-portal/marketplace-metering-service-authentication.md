---
title: Estratégias de autenticação do serviço de medição do Marketplace | Azure Marketplace
description: Estratégias de autenticação de serviço de medição com suporte no Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/13/2020
ms.openlocfilehash: 4b3a2ed71845b8848c9cb0ac5002e0c69a170410
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83642309"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Estratégias de autenticação do serviço de medição do Marketplace

O serviço de medição do Marketplace dá suporte a duas estratégias de autenticação:

* [Token de segurança do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Explicaremos quando e como usar as diferentes estratégias de autenticação para enviar medidores personalizados com segurança usando o serviço de medição do Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Usar o token de segurança do Azure Active Directory

Os tipos de oferta aplicáveis são SaaS e Aplicativos Azure com o tipo de plano de aplicativo gerenciado.  

Envie medidores personalizados usando uma ID de aplicativo fixa predefinida para autenticar.

Para ofertas de SaaS, o Azure Active Directory é a única opção disponível.

Para aplicativos do Azure com o plano de aplicativo gerenciado, você deve considerar o uso dessa estratégia nos seguintes casos:

* Você já tem um mecanismo para se comunicar com seus serviços de back-end e deseja estender esse mecanismo para emitir medidores personalizados de um serviço central.
* Você tem uma lógica de medidores personalizados complexa.  Execute essa lógica em um local central, em vez dos recursos do aplicativo gerenciado.

Depois de registrar seu aplicativo, você pode solicitar um token de segurança do Azure Active Directory programaticamente. O publicador deve usar esse token e fazer uma solicitação para resolvê-lo.

Para obter mais informações sobre esses tokens, veja [Tokens de acesso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base no Aplicativo Azure AD

#### <a name="http-method"></a>Método HTTP

**POST**

#### <a name="request-url"></a>*URL de Solicitação*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parâmetro URI*

|  **Nome do parâmetro** |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID do locatário do aplicativo Azure Active Directory registrado.   |
| | | |

#### <a name="request-header"></a>*Cabeçalho da solicitação*

|  **Nome do cabeçalho**    |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corpo da solicitação*

|  **Nome da propriedade**  |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Tipo de concessão. O valor padrão é `client_credentials`. |
|  `Client_id`        |   True         | Identificador do cliente/aplicativo associado ao Aplicativo Azure AD.|
|  `client_secret`    |   True         | Senha associada ao aplicativo Azure Active Directory.  |
|  `Resource`         |   True         | Recurso de destino para o qual o token é solicitado. O valor padrão é `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Resposta*

|  **Nome**    |  **Tipo**  |  **Descrição**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Solicitação bem-sucedida.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Token de resposta de exemplo:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Usando o token de identidades gerenciadas pelo Azure

O tipo de oferta aplicável é aplicativos do Azure com tipo de plano de aplicativo gerenciado.

Usar essa abordagem permitirá que a identidade de recursos implantados se autentique para enviar eventos de uso de medidores personalizados.  Você pode inserir o código que emite o uso dentro dos limites de sua implantação.

>[!Note]
>O publicador deve garantir que os recursos que emitem o uso estejam bloqueados, para que não sejam violados.

Seu aplicativo gerenciado pode conter diferentes tipos de recursos, de Máquinas Virtuais até Azure Functions.  Para obter mais informações sobre como autenticar usando identidades gerenciadas para diferentes serviços, consulte [como usar identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources).

Por exemplo, siga as etapas abaixo para autenticar usando uma VM do Windows,

1. Verifique se a identidade gerenciada está configurada usando um dos métodos:
    * [Interface do usuário do portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Modelo do Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [SDKs do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Obtenha um token de acesso para a ID do aplicativo do serviço de medição do Marketplace (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) usando a identidade do sistema, o RDP para a VM, abra o console do PowerShell e execute o comando a seguir

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Obtenha a ID do aplicativo gerenciado da propriedade “ManagedBy” dos grupos de recursos atual

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. O serviço de medição do Marketplace requer que o relate o uso em um `resourceID` e `resourceUsageId` se for um aplicativo gerenciado.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Use a [API do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para emitir o uso.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma oferta do Aplicativo Azure](./create-new-azure-apps-offer.md)