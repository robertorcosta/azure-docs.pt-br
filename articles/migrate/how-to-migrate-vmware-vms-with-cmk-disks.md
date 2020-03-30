---
title: Migre as máquinas virtuais VMware para o Azure com criptografia do lado do servidor (SSE) e chaves gerenciadas pelo cliente (CMK) usando a migração do servidor Azure Migrate
description: Saiba como migrar vms vmware para o Azure com criptografia do lado do servidor (SSE) e chaves gerenciadas pelo cliente (CMK) usando a migração do servidor Azure Migrate
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269478"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrar VMMs VMware para VMs do Azure ativado suscetidos com criptografia do lado do servidor e chaves gerenciadas pelo cliente

Este artigo descreve como migrar vms VMware para máquinas virtuais Do Zure com discos criptografados usando criptografia do lado do servidor (SSE) com chaves gerenciadas pelo cliente (CMK), usando o Azure Migrate Server Migration (replicação sem agente).

A experiência do portal migração do servidor Azure migrate permite [migrar vMs VMware para o Azure com replicação sem agente.](tutorial-migrate-vmware.md) A experiência do portal atualmente não oferece a capacidade de ativar o SSE com cmk para seus discos replicados no Azure. A capacidade de ativar o SSE com o CMK para os discos replicados está disponível atualmente apenas através da API REST. Neste artigo, você verá como criar e implantar um [modelo do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para replicar um VMware VMm e configurar os discos replicados no Azure para usar o SSE com cmk.

Os exemplos deste artigo usam [o Azure PowerShell](/powershell/azure/new-azureps-module-az) para executar as tarefas necessárias para criar e implantar o modelo Gerenciador de recursos.

[Saiba mais](../virtual-machines/windows/disk-encryption.md) sobre a criptografia do lado do servidor (SSE) com chaves gerenciadas pelo cliente (CMK) para discos gerenciados.

## <a name="prerequisites"></a>Pré-requisitos

- [Revise o tutorial](tutorial-migrate-vmware.md) sobre a migração de VMs VMware para o Azure com replicação sem agente para entender os requisitos da ferramenta.
- [Siga estas instruções](how-to-add-tool-first-time.md) para criar um projeto do Azure Migrate e adicione a ferramenta **Azure Migrate: Server Migration** ao projeto.
- [Siga estas instruções](how-to-set-up-appliance-vmware.md) para configurar o aparelho Azure Migrate para VMware em seu ambiente local e detecção completa.

## <a name="prepare-for-replication"></a>Preparar a replicação

Uma vez concluída a descoberta do VM, a linha Servidores Descobertos no bloco de migração do servidor mostrará uma contagem de VMs VMware descobertos pelo aparelho.

Antes de começar a replicar VMs, a infra-estrutura de replicação precisa ser preparada.

1. Crie uma instância de Barramento de Serviço na região de destino. O Ônibus de Serviço é usado pelo dispositivo Azure Migrate no local para se comunicar com o serviço de migração de servidores para coordenar a replicação e a migração.
2. Crie uma conta de armazenamento para transferência de logs de operação da replicação.
3. Crie uma conta de armazenamento para a a que o dispositivo Azure Migrate envia dados de replicação.
4. Crie um Key Vault e configure o Key Vault para gerenciar tokens de assinatura de acesso compartilhado para acesso blob nas contas de armazenamento criadas nas etapas 3 e 4.
5. Crie um token de assinatura de acesso compartilhado para o barramento de serviço criado na etapa 1 e crie um segredo para o token no Key Vault criado na etapa anterior.
6. Crie uma política de acesso do Key Vault para dar ao aparelho Azure Migrate no local (usando o aplicativo AAD do aparelho) e ao Serviço de Migração do Servidor acesso ao Cofre de Chaves.
7. Crie uma política de replicação e configure o serviço de migração do servidor com detalhes da infra-estrutura de replicação criada na etapa anterior.

A infra-estrutura de replicação deve ser criada na região alvo do Azure para a migração e na assinatura alvo do Azure para a a que as VMs estão sendo migradas.

A experiência do portal de migração do servidor simplifica a preparação da infra-estrutura de replicação, fazendo isso automaticamente para você quando você replica uma VM pela primeira vez em um projeto. Neste artigo, assumiremos que você já replicou uma ou mais VMs usando a experiência do portal e que a infra-estrutura de replicação já foi criada. Veremos como descobrir detalhes da infra-estrutura de replicação existente e como usar esses detalhes como entradas para o modelo resource manager que será usado para configurar a replicação com cmk.

### <a name="identifying-replication-infrastructure-components"></a>Identificação de componentes da infra-estrutura de replicação

1. No portal Azure, acesse a página de grupos de recursos e selecione o grupo de recursos no qual o projeto Azure Migrate foi criado.
2. Selecione **Implantações** no menu esquerdo e procure um nome de implantação que começa com a seqüência *"Microsoft.MigrateV2.VMwareV2EnableMigrate"*. Você verá uma lista de modelos do Gerenciador de Recursos criados pela experiência do portal para configurar a replicação de VMs neste projeto. Vamos baixar um desses modelos e usá-lo como base para preparar o modelo para replicação com CMK.
3. Para baixar o modelo, selecione qualquer implantação que corresponda ao padrão de seqüência na etapa anterior > selecionar **Modelo** no menu esquerdo > Clique em **Baixar** no menu superior. Salve o arquivo template.json localmente. Você editará este arquivo de modelo na última etapa.

## <a name="create-a-disk-encryption-set"></a>Criar um conjunto de criptografia de disco

Uma criptografia de disco define os mapas de objetos gerenciados em um cofre de chaves que contém o CMK para usar no SSE. Para replicar VMs com CMK, você criará um conjunto de criptografia de disco e o passará como uma entrada para a operação de replicação.

Siga o exemplo [aqui](../virtual-machines/windows/disk-encryption.md#powershell) para criar um conjunto de criptografia de disco usando o Azure PowerShell. Certifique-se de que o conjunto de criptografia de disco seja criado na assinatura de destino para a a que as VMs estão sendo migradas e na região alvo do Azure para a migração.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Obtenha detalhes da VMware VMM para migrar

Nesta etapa, você usará o Azure PowerShell para obter os detalhes da VM que precisa ser migrada. Esses detalhes serão usados para construir o modelo resource manager para replicação. Especificamente, as duas propriedades de interesse são:

- A máquina de recursos id para as VMs descobertas.
- A lista de discos para a VM e seus identificadores de disco.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Copie o valor da seqüência SiteId correspondente ao aparelho Azure Migrate que a VM é descoberta através. No exemplo acima, o SiteId é *"/assinaturas/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Copie os valores de ResourceId, nome e disco uuid para que a máquina seja migrada.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Criar modelo de gerenciador de recursos para replicação

- Abra o arquivo de modelo do Gerenciador de recursos que você baixou na **etapa de infra-estrutura de replicação de identificação** em um editor de sua escolha.
- Remova todas as definições de recursos do modelo, exceto os recursos do tipo *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContêineres/replicationMigrationItems"*
- Se houver várias definições de recursos do tipo acima, remova todos, menos um. Remova quaisquer definições de propriedade **dependSOn** da definição de recurso.
- No final desta etapa, você deve ter um arquivo que se parece com o exemplo abaixo e tem o mesmo conjunto de propriedades.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Editar a propriedade **nome** na definição de recurso. Substitua a seqüência que segue o último "/" na propriedade nome pelo valor de *$machine. Nome*(da etapa anterior).
- Alterar o valor da **propriedade.providerSpecificDetails.vmwareMachineId** com valor de *$machine. ResourceId*(da etapa anterior).
- Defina os valores para **targetResourceGroupId,** **targetNetworkId,** **targetSubnetName** para o ID do grupo de recursos de destino, iD de recurso de rede virtual de destino e nome de sub-rede de destino, respectivamente.
- Defina o valor da **licençaType** como "WindowsServer" para aplicar o Azure Hybrid Benefit para esta VM. Se esta VM não for elegível para o Azure Hybrid Benefit, defina o valor da **licençaType** como NoLicenseType.
- Alterar o valor da propriedade **targetVmName** para o nome da máquina virtual Azure desejada para a VM migrada.
- Adicionar opcionalmente uma propriedade chamada **targetVmSize** abaixo da propriedade **targetVmName.** Defina o valor da propriedade **targetVmSize** para o tamanho desejado da máquina virtual DoZure para a VM migrada.
- A propriedade **disksToInclude** é uma lista de entradas de disco para replicação com cada item da lista representando um disco no local. Crie tantos itens de lista quanto o número de discos na VM no local. Substitua a propriedade **diskId** no item da lista ao uuid dos discos identificados na etapa anterior. Defina o valor **isOSDisk** como "verdadeiro" para o disco do sistema operacional da VM e "falso" para todos os outros discos. Deixe inalteradas as propriedades **logStorageAccountAccountId** e **logStorageAccountSasSecretName.** Defina o valor **diskType** para o tipo de disco gerenciado do Azure *(Standard_LRS, Premium_LRS, StandardSSD_LRS)* para usar no disco. Para os discos que precisam ser criptografados com cmk, adicione uma propriedade chamada **diskEncryptionSetId** e defina o valor para o ID de recurso do conjunto de criptografia de disco criado **($des. Id**) na *etapa Criar um conjunto de criptografia de disco*
- Salvar o arquivo de modelo editado. Para o exemplo acima, o arquivo de modelo editado é o seguinte:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Configurar a replicação

Agora, você pode implantar o modelo de gerenciador de recursos editado no grupo de recursos do projeto para configurar a replicação da VM. Saiba como [implantar recursos com os modelos do Azure Resource Manager e do Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Próximas etapas

[Monitore](tutorial-migrate-vmware.md#track-and-monitor) o status de replicação através da experiência do portal e execute migrações de teste e migração.
