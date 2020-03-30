---
title: Azure Services que suportam identidades gerenciadas - Azure AD
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 03/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9e01f1f5abfc0f76926ce503fae058c196c6e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282096"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que dão suporte a identidades gerenciadas para recursos do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, sem ter as credenciais no seu código. Estamos no processo de integração de identidades gerenciadas para recursos do Azure e autenticação do Azure AD no Azure. Verifique regularmente as atualizações.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que dão suporte às identidades gerenciadas dos recursos do Azure

Os seguintes serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Visualização | Visualização | 
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Visualização | Visualização |

Veja a lista a seguir para configurar a identidade gerenciada para Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Portal Azure](qs-configure-portal-windows-vm.md)
- [Powershell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Modelos do Azure Resource Manager](qs-configure-template-windows-vm.md)
- [Resto](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual do Azure

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Visualização | Visualização | Visualização |
| Atribuído pelo usuário | ![Disponível][check] | Visualização | Visualização | Visualização |

Veja a lista a seguir para configurar a identidade gerenciada para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Portal Azure](qs-configure-portal-windows-vm.md)
- [Powershell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Modelos do Azure Resource Manager](qs-configure-template-windows-vm.md)
- [Resto](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check] |

Veja a lista a seguir para configurar a identidade gerenciada para Serviço de Aplicativo do Azure (em regiões em que estiver disponível):

- [Portal Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo de Gerenciador de recursos do Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |

Consulte a lista a seguir para usar uma identidade gerenciada com [o Azure Blueprints](../../governance/blueprints/overview.md):

- [Portal Azure - atribuição de projeto](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST - atribuição de projeto](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerenciada |Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check]  |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Functions (em regiões em que estiver disponível):

- [Portal Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo de Gerenciador de recursos do Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Aplicativos Lógicos do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |


Veja a lista a seguir para configurar a identidade gerenciada para Aplicativos Lógicos do Azure (em regiões em que estiverem disponíveis):

- [Portal Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Modelo de Gerenciador de recursos do Azure](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Data Factory V2 (em regiões em que estiver disponível):

- [Portal Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Powershell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [Resto](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gerenciamento de API do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Gerenciamento de API do Azure (em regiões em que estiver disponível):

- [Modelo de Gerenciador de recursos do Azure](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Linux: versão prévia<br>Windows: não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Linux: versão prévia<br>Windows: não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Instâncias de Contêiner do Azure (em regiões em que estiverem disponíveis):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo de Gerenciador de recursos do Azure](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Registro de Contêiner do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para tarefas de registro de contêiner do Azure (em regiões onde disponíveis):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Os aplicativos de malha de identidade gerenciada para serviço saem](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) em pré-visualização e estão disponíveis em todas as regiões.

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | não disponível |
| Atribuído pelo usuário | ![Disponível][check] | Não disponível | Não disponível |Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para aplicativos de malha de serviço do Azure em todas as regiões:
- [Modelo de Gerenciador de recursos do Azure](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam identidades gerenciadas para recursos do Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a lista a seguir para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso através do portal Azure](howto-assign-access-portal.md)
- [Atribuir acesso via PowerShell](howto-assign-access-powershell.md)
- [Atribuir acesso via Cli do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso via modelo do Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponível][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://management.microsoftazure.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-key-vault"></a>Cofre de Chave do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponível][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha |  `https://vault.microsoftazure.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Disponível][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponível][check] |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

### <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponível][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://database.cloudapi.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Disponível][check] |

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponível][check] |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

### <a name="azure-service-bus"></a>Barramento de Serviço do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponível][check] |
| Azure Government |  | ![Disponível][check] |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |









### <a name="azure-storage-blobs-and-queues"></a>Bolhas e filas de armazenamento do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponível][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponível][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponível][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Disponível][check] |


[check]: media/services-support-managed-identities/check.png "Disponível"
