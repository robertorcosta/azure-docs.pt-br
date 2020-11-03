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
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286117"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Registro automático com provedor de recursos de VM do SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Habilite o recurso de registro automático no portal do Azure para registrar automaticamente todos os SQL Server atuais e futuros em VMs (máquinas virtuais) do Azure com o provedor de recursos de VM do SQL no modo leve. O registro com o provedor de recursos da VM do SQL instala a [extensão do agente IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md).

Este artigo ensina a habilitar o recurso de registro automático. Como alternativa, você pode [registrar uma única VM](sql-vm-resource-provider-register.md)ou [registrar suas VMs em massa](sql-vm-resource-provider-bulk-register.md) com o provedor de recursos da VM do SQL. 

## <a name="overview"></a>Visão geral

Registrar seu SQL Server VM com o provedor de recursos da VM do SQL instala a [extensão do SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). 

Quando o registro automático está habilitado, um trabalho é executado diariamente para detectar se SQL Server está instalado ou não em todas as VMs não registradas na assinatura. Isso é feito copiando os binários de extensão do agente IaaS do SQL para a VM e, em seguida, executando um utilitário único que verifica o hive do registro de SQL Server. Se o hive SQL Server for detectado, a máquina virtual será registrada com o [provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md) no modo leve. Se não existir SQL Server Hive no registro, os binários serão removidos.

Quando o registro automático estiver habilitado para uma assinatura, todas as VMs atuais e futuras que têm o SQL Server instalado serão registradas com o provedor de recursos de VM do SQL no modo leve. Você ainda precisa [atualizar manualmente para o modo de gerenciamento completo](sql-vm-resource-provider-register.md#upgrade-to-full) para aproveitar o conjunto de recursos completo. 

> [!IMPORTANT]
> A extensão do agente IaaS do SQL coleta dados para a finalidade expressa de fornecer aos clientes benefícios opcionais ao usar SQL Server nas máquinas virtuais do Azure. A Microsoft não usará esses dados para auditorias de licenciamento sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma máquina virtual do modelo de recurso do Azure [Windows Server 2008 R2 (ou posterior)](../../../virtual-machines/windows/quick-create-portal.md) com [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) implantada na nuvem pública ou do Azure governamental. Não há suporte para o Windows Server 2008. 


## <a name="enable"></a>Habilitar

Para habilitar o registro automático de suas VMs de SQL Server no portal do Azure, siga estas etapas:

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

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

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
