---
title: Habilitar a criptografia baseada em host no serviço kubernetes do Azure (AKS)
description: Saiba como configurar uma criptografia baseada em host em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 6b23bf285d89a5f3285825feef849b3d168ed62f
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762036"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Criptografia baseada em host no serviço kubernetes do Azure (AKS) (visualização)

Com a criptografia baseada em host, os dados armazenados no host da VM de suas VMs dos nós do agente AKS são criptografados em repouso e fluem criptografados para o serviço de armazenamento. Isso significa que os discos temporários são criptografados em repouso com chaves gerenciadas pela plataforma. O cache do sistema operacional e dos discos de dados é criptografado em repouso com chaves gerenciadas por plataforma ou chaves gerenciadas pelo cliente, dependendo do tipo de criptografia definido nesses discos. Por padrão, ao usar AKS, o sistema operacional e OS discos de dados são criptografados em repouso com chaves gerenciadas pela plataforma, o que significa que os caches desses discos também são criptografados por padrão em repouso com chaves gerenciadas pela plataforma.  Você pode especificar suas próprias chaves gerenciadas seguindo [BYOK (Traga suas próprias chaves) com discos do Azure no serviço kubernetes do Azure](azure-disk-customer-managed-keys.md). O cache desses discos também será criptografado usando a chave que você especificar nesta etapa.


## <a name="before-you-begin"></a>Antes de começar

Esse recurso só pode ser definido na criação do cluster ou no momento da criação do pool de nós.

> [!NOTE]
> A criptografia baseada em host está disponível em [regiões do Azure][supported-regions] que dão suporte à criptografia do lado do servidor de Azure Managed disks e somente com [tamanhos de VM com suporte][supported-sizes]específicos.

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se você tem a `aks-preview` extensão da CLI v 0.4.55 ou superior instalada
- Verifique se você tem o `EnableEncryptionAtHostPreview` sinalizador de recurso em `Microsoft.ContainerService` habilitado.

Para poder usar a criptografia no host para suas VMs ou conjuntos de dimensionamento de máquinas virtuais, você deve obter o recurso habilitado em sua assinatura. Envie um email para encryptionAtHost@microsoft.com com suas IDs de assinatura para obter o recurso habilitado para suas assinaturas.

### <a name="register-encryptionathost--preview-features"></a>Registrar `EncryptionAtHost`  recursos de visualização

> [!IMPORTANT]
> Você deve enviar encryptionAtHost@microsoft o email. com com suas IDs de assinatura para obter o recurso habilitado para recursos de computação. Você mesmo não pode habilitá-lo para esses recursos. Você mesmo pode habilitá-lo no serviço de contêiner.

Para criar um cluster AKS que usa criptografia baseada em host, você deve habilitar os `EnableEncryptionAtHostPreview` `EncryptionAtHost` sinalizadores de recurso e em sua assinatura.

Registre o `EncryptionAtHost` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro dos `Microsoft.ContainerService` `Microsoft.Compute` provedores de recursos e usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que tenha criptografia baseada em host, você precisa da extensão mais recente da CLI do *AKs-Preview* . Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] ou verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

- Só pode ser habilitado em novos pools de nós ou em novos clusters.
- Só pode ser habilitado em [regiões do Azure][supported-regions] que dão suporte à criptografia do lado do servidor de Azure Managed disks e somente com [tamanhos de VM com suporte][supported-sizes]específicos.
- Requer um cluster AKS e um pool de nós baseados em VMSS (conjuntos de dimensionamento de máquinas virtuais) como *tipo de conjunto de VMs*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Usar a criptografia baseada em host em novos clusters (visualização)

Configure os nós do agente de cluster para usar a criptografia baseada em host quando o cluster for criado. Use o `--aks-custom-headers` sinalizador para definir o `EnableEncryptionAtHost` cabeçalho.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Se você quiser criar clusters sem criptografia baseada em host, poderá fazer isso omitindo o `--aks-custom-headers` parâmetro personalizado.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Usar criptografia baseada em host em clusters existentes (visualização)

Você pode habilitar a criptografia baseada em host em clusters existentes adicionando um novo pool de nós ao cluster. Configure um novo pool de nós para usar a criptografia baseada em host usando o `--aks-custom-headers` sinalizador.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Se você quiser criar novos pools de nós sem o recurso de criptografia baseado em host, poderá fazer isso omitindo o `--aks-custom-headers` parâmetro personalizado.

## <a name="next-steps"></a>Próximas etapas

Examine [as práticas recomendadas para segurança de cluster AKs][best-practices-security] Leia mais sobre [criptografia baseada em host](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register