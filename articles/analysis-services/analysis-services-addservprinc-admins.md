---
title: Adicionar entidade de serviço à função de administrador de Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar uma entidade de serviço de automação à função de administrador do Azure Analysis Services Server
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 925fbbb51ac240b96486a2c0aa09c850a8d164bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80408638"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas do PowerShell autônomas, uma entidade de serviço deve ter privilégios de **administrador do servidor** no servidor do Analysis Services sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função de administradores do servidor em um servidor do AS do Azure. Você pode fazer isso usando SQL Server Management Studio ou um modelo do Resource Manager.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deverá ter uma entidade de serviço registrado no Azure Active Directory.

[Criar entidade de serviço-portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Como usar o SQL Server Management Studio.

Você pode configurar administradores de servidor usando o SQL Server Management Studio (SSMS). Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no servidor do AS do Azure. 

1. No SSMS, conecte o servidor do AS do Azure.
2. Em **Propriedades** > do servidor**segurança**, clique em **Adicionar**.
3. Em **Selecionar Usuário ou Grupo**, procure o aplicativo registrado pelo nome, selecione e clique em**Adicionar**.

    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta da entidade do serviço e clique em **OK**.
    
    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Usar um modelo do Resource Manager

Você também pode configurar os administradores de servidor implantando o servidor de Analysis Services usando um modelo de Azure Resource Manager. A identidade que executa a implantação deve pertencer à função de **colaborador** para o recurso no [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md).

> [!IMPORTANT]
> A entidade de serviço deve ser adicionada usando o `app:{service-principal-client-id}@{azure-ad-tenant-id}`formato.

O modelo do Resource Manager a seguir implanta um servidor de Analysis Services com uma entidade de serviço especificada adicionada à função de administrador de Analysis Services:

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

Uma identidade gerenciada também pode ser adicionada à lista de administradores de Analysis Services. Por exemplo, você pode ter um [aplicativo lógico com uma identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md)e deseja conceder a ele a capacidade de administrar seu servidor de Analysis Services.

Na maioria das partes do portal do Azure e das APIs, as identidades gerenciadas são identificadas usando sua ID de objeto de entidade de serviço. No entanto, Analysis Services exige que eles sejam identificados usando sua ID do cliente. Para obter a ID do cliente para uma entidade de serviço, você pode usar o CLI do Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Como alternativa, você pode usar o PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Você pode usar essa ID de cliente em conjunto com a ID de locatário para adicionar a identidade gerenciada à lista de administradores de Analysis Services, conforme descrito acima.

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


