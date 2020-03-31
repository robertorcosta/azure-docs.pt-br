---
title: Configure um grupo de disponibilidade (Azure CLI)
description: Use o Azure CLI para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balanceador de carga interna em um VM do Servidor SQL no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022358"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Use o Azure CLI para configurar um grupo de disponibilidade Always On para SQL Server em uma VM Azure
Este artigo descreve como usar o [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) para implantar um cluster de failover do Windows, adicionar VMs do Servidor SQL ao cluster e criar o balanceador de carga interna e o ouvinte para um grupo de disponibilidade Always On. A implantação do grupo de disponibilidade Always On ainda é feita manualmente através do SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a configuração de um grupo de disponibilidade Always On usando o Cli do Azure, você deve ter os seguintes pré-requisitos: 
- Uma [assinatura do Azure.](https://azure.microsoft.com/free/)
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs com domínio [no Azure executando o SQL Server 2016 (ou posterior) edição Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no mesmo conjunto de disponibilidade ou *diferentes zonas* de disponibilidade que foram [registradas no provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).  
- A [CLI Azure](/cli/azure/install-azure-cli). 
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Uma é para o balanceador de carga interna. O outro é para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede que o grupo de disponibilidade. Se você estiver usando um balanceador de carga existente, você precisa apenas de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões
Você precisa das seguintes permissões de conta para configurar o grupo de disponibilidade Always On usando o Cli do Azure: 

- Uma conta de usuário de domínio existente que tenha permissão **Criar objeto de computador** no domínio. Por exemplo, uma conta de administração de domínio normalmente account@domain.comtem permissão suficiente (por exemplo: ). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário de domínio que controla o serviço SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Passo 1: Crie uma conta de armazenamento como testemunha de nuvem
O cluster precisa de uma conta de armazenamento para agir como testemunha de nuvem. Você pode usar qualquer conta de armazenamento existente ou criar uma nova conta de armazenamento. Se você quiser usar uma conta de armazenamento existente, pule para a próxima seção. 

O seguinte trecho de código cria a conta de armazenamento: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Você pode ver `az sql: 'vm' is not in the 'az sql' command group` o erro se estiver usando uma versão desatualizada do Azure CLI. Baixe a [versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) para superar esse erro.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Passo 2: Definir metadados de cluster failover do Windows
O grupo de comando Azure CLI [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) gerencia os metadados do serviço WSFC (Windows Server Failover Cluster, cluster de failover do Windows Server) que hospeda o grupo de disponibilidade. Os metadados de cluster incluem o domínio active directory, contas de cluster, contas de armazenamento a serem usadas como testemunha na nuvem e versão do SQL Server. Use [o grupo az sql vm create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) para definir os metadados do WSFC para que, quando o primeiro VM do Servidor SQL for adicionado, o cluster seja criado conforme definido. 

O seguinte trecho de código define os metadados para o cluster:
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Passo 3: Adicionar VMs do servidor SQL ao cluster
Adicionar o primeiro VM do SQL Server ao cluster cria o cluster. O comando [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) cria o cluster com o nome dado anteriormente, instala a função de cluster nas VMs do Servidor SQL e as adiciona ao cluster. Os usos `az sql vm add-to-group` subsequentes do comando adicionam mais VMs do Servidor SQL ao cluster recém-criado. 

O seguinte trecho de código cria o cluster e adiciona o primeiro VM do Servidor SQL a ele: 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Use este comando para adicionar quaisquer outras VMs do Servidor SQL ao cluster. Modifique `-n` apenas o parâmetro para o nome VM do Servidor SQL. 

## <a name="step-4-create-the-availability-group"></a>Passo 4: Crie o grupo de disponibilidade
Crie manualmente o grupo de disponibilidade como você normalmente faria, usando [o SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento porque isso é feito através do Azure CLI nas seções a seguir.  

## <a name="step-5-create-the-internal-load-balancer"></a>Passo 5: Crie o balanceador de carga interna

O ouvinte do grupo always on requer uma instância interna do Azure Load Balancer. O balanceador de carga interna fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite um failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga Básico. Caso contrário, você precisa usar um balanceador de carga Padrão.  

> [!NOTE]
> O balanceador de carga interna deve estar na mesma rede virtual que as instâncias vm do Servidor SQL. 

O seguinte trecho de código cria o balanceador de carga interna:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> O recurso IP público para cada VM do Servidor SQL deve ter um SKU padrão para ser compatível com o balanceador de carga Padrão. Para determinar o SKU do recurso IP público da VM, vá para **O Grupo de Recursos,** selecione o recurso **Endereço IP Público** para o VM desejado do SQL Server e localize o valor em **SKU** no painel **Visão Geral.**  

## <a name="step-6-create-the-availability-group-listener"></a>Passo 6: Crie o ouvinte do grupo de disponibilidade
Depois de criar manualmente o grupo de disponibilidade, você pode criar o ouvinte usando [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

O *ID de recurso de sub-rede* é o valor de `/subnets/<subnetname>` anexo ao ID de recurso do recurso de rede virtual. Para identificar o ID de recurso da sub-rede:
   1. Vá para o seu grupo de recursos no [portal Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **Propriedades** no painel **Configurações.** 
   1. Identifique o ID de recurso para `/subnets/<subnetname>` a rede virtual e apêndice até o final dela para criar o ID de recurso da sub-rede. Por exemplo: 
      - Seu ID de recurso de rede virtual é:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Seu nome de sub-rede é:`default`
      - Portanto, seu ID de recurso de sub-rede é:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O seguinte trecho de código cria o ouvinte do grupo de disponibilidade:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modificar o número de réplicas em um grupo de disponibilidade
Há uma camada adicional de complexidade quando você está implantando um grupo de disponibilidade para VMs do SQL Server hospedados no Azure. O provedor de recursos e o grupo de máquinas virtuais agora gerenciam os recursos. Como tal, quando você está adicionando ou removendo réplicas no grupo de disponibilidade, há uma etapa adicional de atualização dos metadados do ouvinte com informações sobre as VMs do SQL Server. Quando você está modificando o número de réplicas no grupo de disponibilidade, você também deve usar o comando [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) para atualizar o ouvinte com os metadados das VMs do SQL Server. 


### <a name="add-a-replica"></a>Adicionar uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade:

1. Adicione o VM do Servidor SQL ao cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Use o SQL Server Management Studio para adicionar a instância do SQL Server como uma réplica dentro do grupo de disponibilidade.
1. Adicione os metadados vm do Servidor SQL ao ouvinte:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica do grupo de disponibilidade:

1. Remova a réplica do grupo de disponibilidade usando o SQL Server Management Studio. 
1. Remova os metadados vm do servidor SQL do ouvinte:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Remova o VM do Servidor SQL do cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Remova o ouvinte do grupo de disponibilidade
Se você precisar remover o ouvinte de grupo de disponibilidade configurado com o Azure CLI, você deve passar pelo provedor de recursos SQL VM. Como o ouvinte está registrado através do provedor de recursos SQL VM, excluí-lo através do SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo através do provedor de recursos SQL VM usando o seguinte trecho de código no Cli Do Zure. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos SQL VM. Ele também exclui fisicamente o ouvinte do grupo de disponibilidade. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral das VMs do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para VMs do servidor SQL](virtual-machines-windows-sql-server-iaas-faq.md)
* [Notas de versão para VMs do Servidor SQL](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md)
* [Visão geral dos grupos de disponibilidade Always On &#40;&#41;do SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para grupos de disponibilidade Always On &#40;sql server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade &#40;&#41;do SQL Server](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoramento de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral das instruções Transact-SQL para grupos de disponibilidade Always On &#40;sql server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral dos cmdlets do PowerShell para grupos de disponibilidade Always On &#40;sql server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
