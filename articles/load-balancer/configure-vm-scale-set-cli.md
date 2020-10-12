---
title: Configurar conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente-CLI do Azure
description: Saiba como configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 990380d553cc12d1a87b2e1c7ca9b09864801294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333967"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure

Neste artigo, você aprenderá a configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga de SKU padrão existente na assinatura em que o conjunto de dimensionamento de máquinas virtuais será implantado.
- Uma rede virtual do Azure para o conjunto de dimensionamento de máquinas virtuais.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por usar a CLI localmente, este artigo exigirá que você tenha uma versão do CLI do Azure versão 2.0.28 ou posterior instalada. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Entre no Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar um conjunto de dimensionamento de máquinas virtuais com o balanceador de carga existente

Substitua os valores entre colchetes pelos nomes dos recursos em sua configuração.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

O exemplo abaixo implanta um conjunto de dimensionamento de máquinas virtuais com:

- Conjunto de dimensionamento de máquinas virtuais chamado **myVMSS**
- Azure Load Balancer chamado **myLoadBalancer**
- Pool de back-end do balanceador de carga chamado **myBackendPool**
- Rede virtual do Azure chamada **myVnet**
- Sub-rede chamada **Mysubnet**
- Grupo de recursos chamado **MyResource** Group
- Imagem do servidor Ubuntu para o conjunto de dimensionamento de máquinas virtuais

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais do Azure, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.  Para saber mais sobre conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, confira:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
                                