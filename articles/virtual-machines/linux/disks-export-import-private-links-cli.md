---
title: CLI do Azure – Restringir o acesso de importação/exportação aos discos gerenciados com Links Privados (versão prévia)
description: Habilite Links Privados (versão prévia) para seus discos gerenciados com a CLI do Azure. Permitindo que você exporte e importe discos com segurança somente na sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9184dc78f0f9f8d7997e0bb64bc4521e19364cfe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535458"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>CLI do Azure – Restringir o acesso de importação/exportação aos discos gerenciados com Links Privados (versão prévia)

Use [pontos de extremidade privados](../../private-link/private-endpoint-overview.md) (versão prévia) para restringir a exportação e a importação de discos gerenciados e acesse dados com segurança em um [Link Privado](../../private-link/private-link-overview.md) em clientes na sua rede virtual do Azure. O ponto de extremidade privado usa um endereço IP do espaço de endereço de rede virtual para o serviço de discos gerenciados. O tráfego de rede entre os clientes na rede virtual e os discos gerenciados atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição na Internet pública. 

Para usar Links Privados para exportar/importar discos gerenciados, primeiro crie um recurso de acesso a disco e vincule-o a uma rede virtual na mesma assinatura criando um ponto de extremidade privado. Em seguida, associe um disco ou um instantâneo a uma instância de acesso a disco. Por fim, defina a propriedade NetworkAccessPolicy do disco ou o instantâneo como `AllowPrivate`. Isso limitará o acesso à sua rede virtual. 

Você pode definir a propriedade NetworkAccessPolicy como `DenyAll` para impedir que qualquer pessoa exporte os dados de um disco ou de um instantâneo. O valor padrão da propriedade NetworkAccessPolicy é `AllowAll`.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para usar pontos de extremidade privados para exportar e importar discos gerenciados, habilite o recurso na sua assinatura. Envie um email para mdprivatelinks@microsoft.com com as suas IDs de assinatura para habilitar o recurso para suas assinaturas.

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Faça logon na sua assinatura e defina as variáveis

```azurecli-interactive

subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=CentralUSEUAP
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Criar um acesso a disco usando a CLI do Azure
```azurecli-interactive
az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Não há suporte para políticas de rede, como NSG (grupos de segurança de rede), em pontos de extremidade privados. Para implantar um ponto de extremidade privado em determinada sub-rede, uma configuração de desabilitação explícita é necessária nessa sub-rede. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede

O Azure implanta recursos em uma sub-rede dentro de uma rede virtual. Portanto, você precisa atualizar a sub-rede para desabilitar as políticas de rede do ponto de extremidade privado. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Criar um ponto de extremidade privado para o objeto de acesso a disco

```azurecli-interactive
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Configurar a Zona DNS Privada

Crie uma Zona DNS Privada para o domínio do blob de armazenamento, crie um link de associação com a Rede Virtual e crie um Grupo de Zona DNS para associar o ponto de extremidade privado à Zona DNS Privada. 

```azurecli-interactive
az network private-dns zone create --resource-group $resourceGroupName \ 
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```
## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Criar um instantâneo de um disco protegido com Links Privados
   ```cli
   diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)
   
   az group deployment create -g $resourceGroupName \
      --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateSnapshotWithExportViaPrivateLink.json" \
      --parameters "snapshotNameSecuredWithPL=$snapshotNameSecuredWithPL" \
      "sourceResourceId=$diskId" \
      "diskAccessId=$diskAccessId" \
      "region=$region" \
      "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre os Links Privados](faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportar/copiar instantâneos gerenciados como VHD para uma conta de armazenamento em outa região com o PowerShell](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)
