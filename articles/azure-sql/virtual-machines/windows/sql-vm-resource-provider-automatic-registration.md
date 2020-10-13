---
title: Registro automático com provedor de recursos de VM do SQL
description: Saiba como habilitar o recurso de registro automático para registrar automaticamente todas as VMs de SQL Server passadas e futuras com o provedor de recursos de VM do SQL usando o portal do Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996882"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Registro automático com provedor de recursos de VM do SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Habilite o recurso de registro automático no portal do Azure para registrar automaticamente todos os SQL Server atuais e futuros em VMs do Azure com o provedor de recursos de VM do SQL no modo leve.

Este artigo ensina a habilitar o recurso de registro automático. Como alternativa, você pode [registrar uma única VM](sql-vm-resource-provider-register.md)ou [registrar suas VMs em massa](sql-vm-resource-provider-bulk-register.md) com o provedor de recursos da VM do SQL. 

## <a name="overview"></a>Visão geral

O [provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md#overview) permite que você gerencie sua VM SQL Server do portal do Azure. Além disso, o provedor de recursos permite um conjunto de recursos robusto, incluindo [aplicação de patch automatizada](automated-patching.md), [backup automatizado](automated-backup.md), bem como recursos de monitoramento e gerenciamento. Também desbloqueia o [licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) e a flexibilidade da [edição](change-sql-server-edition.md). Anteriormente, esses recursos estavam disponíveis apenas para imagens de VM do SQL Server implantadas do Azure Marketplace. 

O recurso de registro automático permite que os clientes registrem automaticamente todas as VMs SQL Server atuais e futuras em sua assinatura do Azure com o provedor de recursos de VM do SQL. Isso é diferente do registro manual, que só se concentra nas VMs SQL Server atuais. 

O registro automático registra suas VMs SQL Server no modo leve. Você ainda precisa [atualizar manualmente para o modo de gerenciamento completo](sql-vm-resource-provider-register.md#upgrade-to-full) para aproveitar o conjunto de recursos completo. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [máquina virtual do Windows](../../../virtual-machines/windows/quick-create-portal.md) do modelo de recursos do azure com [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) implantada na nuvem pública ou do Azure governamental. 


## <a name="enable"></a>Habilitar

Para habilitar o registro automático de suas VMs de SQL Server no portal do Azure, siga as etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até a página de recursos de [**máquinas virtuais do SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecione **automático SQL Server registro de VM** para abrir a página de **registro automático** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Selecione o registro automático de SQL Server VM para abrir a página de registro automático":::

1. Escolha sua assinatura na lista suspensa. 
1. Leia os termos e, se concordar, selecione **aceito**. 
1. Selecione **registrar** para habilitar o recurso e registrar automaticamente todas as VMs de SQL Server atuais e futuras com o provedor de recursos de VM do SQL. Isso não reiniciará o serviço de SQL Server em nenhuma das VMs. 

## <a name="disable"></a>Desabilitar

Use o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps) para desabilitar o recurso de registro automático. Quando o recurso de registro automático está desabilitado, SQL Server VMs adicionadas à assinatura precisam ser registradas manualmente com o provedor de recursos de VM do SQL. Isso não cancelará o registro de VMs SQL Server existentes que já foram registradas.



# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desabilitar o registro automático usando CLI do Azure, execute o seguinte comando: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para desabilitar o registro automático usando Azure PowerShell, execute o seguinte comando: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Habilitar para várias assinaturas

Você pode habilitar o recurso de registro automático para várias assinaturas do Azure usando o PowerShell. 

Para fazer isso, siga estas etapas:

1. Salve [esse script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) em um `.ps1` arquivo, como `EnableBySubscription.ps1` . 
1. Navegue até onde você salvou o script usando um prompt de comando administrativo ou uma janela do PowerShell. 
1. Conecte-se ao Azure ( `az login` ).
1. Execute o script, passando SubscriptionIds como parâmetros como   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Por exemplo: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Erros de registro com falha são armazenados no `RegistrationErrors.csv` localizado no mesmo diretório em que você salvou e executou o `.ps1` script. 

## <a name="next-steps"></a>Próximas etapas

Atualize seu modo de gerenciamento para [completo](sql-vm-resource-provider-register.md#upgrade-to-full) para aproveitar o conjunto completo de recursos fornecido a você pelo provedor de recursos da VM do SQL. 
