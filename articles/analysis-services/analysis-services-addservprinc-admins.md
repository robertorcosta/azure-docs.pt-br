---
title: Adicionar o principal de serviço ao papel de administrador do Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar um principal de serviço de automação à função de administrador do servidor azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298081"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas do PowerShell autônomas, uma entidade de serviço deve ter privilégios de **administrador do servidor** no servidor do Analysis Services sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função de administradores do servidor em um servidor do AS do Azure. Você pode fazer isso usando o SQL Server Management Studio ou um modelo de Gerenciador de Recursos.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deverá ter uma entidade de serviço registrado no Azure Active Directory.

[Criar o principal de serviço - Portal Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Como usar o SQL Server Management Studio.

Você pode configurar administradores de servidores usando o SSMS (SSMS) de gerenciamento de servidores sql. Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no servidor do AS do Azure. 

1. No SSMS, conecte o servidor do AS do Azure.
2. Na **segurança de propriedades** > **do servidor,** clique **em Adicionar**.
3. Em **Selecionar Usuário ou Grupo**, procure o aplicativo registrado pelo nome, selecione e clique em**Adicionar**.

    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta da entidade do serviço e clique em **OK**.
    
    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Usando um modelo de gerenciador de recursos

Você também pode configurar os administradores do servidor implantando o servidor Serviços de Análise usando um modelo do Azure Resource Manager. A identidade em execução da implantação deve pertencer à função **Contribuinte** para o recurso no [RBAC (Azure Role-Based Access Control, controle de acesso baseado em função)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> O principal de serviço deve `app:{service-principal-client-id}@{azure-ad-tenant-id}`ser adicionado usando o formato .

O modelo do Gerenciador de recursos a seguir implanta um servidor de serviços de análise com um principal de serviço especificado adicionado à função Deadministração de Serviços de Análise:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Usando identidades gerenciadas

Uma identidade gerenciada também pode ser adicionada à lista de administração de serviços de análise. Por exemplo, você pode ter um [App Lógico com uma identidade gerenciada atribuída ao sistema](../logic-apps/create-managed-service-identity.md)e deseja conceder a ele a capacidade de administrar seu servidor de Serviços de Análise.

Na maioria das partes do portal azure e APIs, as identidades gerenciadas são identificadas usando seu ID de objeto principal de serviço. No entanto, os Serviços de Análise exigem que eles sejam identificados usando seu ID do cliente. Para obter o ID do cliente para um diretor de serviço, você pode usar o Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Alternativamente, você pode usar o PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Em seguida, você pode usar esse ID do cliente em conjunto com o ID do inquilino para adicionar a identidade gerenciada à lista de administração de serviços de análise, conforme descrito acima.

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


