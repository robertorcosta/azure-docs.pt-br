---
title: Criar um IP público-CLI do Azure
description: Saiba como criar um IP público usando CLI do Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89301359"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Início rápido: criar um endereço IP público usando CLI do Azure

Este artigo mostra como criar um recurso de endereço IP público usando CLI do Azure. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure instalada localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) chamado **MyResource** grupo no local **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**SKU padrão-usando zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público com redundância de zona padrão chamado **MyStandardZRPublicIP** no **MyResource**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Para versões da API com mais de 2020-08-01, execute o comando acima sem especificar um parâmetro de zona para criar um endereço IP com redundância de zona. 
>

Para criar um endereço IP público de zona padrão no Zona 2 chamado **myStandardZonalPublicIP** no **MyResource**, use o seguinte comando:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Observe que as opções acima para zonas são apenas seleções válidas em regiões com [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU padrão-sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público padrão como um recurso não zonal chamado **MyStandardPublicIP** no **MyResource**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Essa seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público estático básico chamado **MyBasicPublicIP** no **MyResource**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada alterando o método de alocação para ' dinâmico '.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [gerenciar endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associe um [endereço IP público a uma máquina virtual](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- Saiba mais sobre os [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).