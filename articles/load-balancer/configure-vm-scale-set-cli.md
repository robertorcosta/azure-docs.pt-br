---
title: Configure o conjunto de escala da máquina virtual com um Azure Load Balancer existente - Azure CLI
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349746"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configure um conjunto de escala de máquina virtual com um Azure Load Balancer existente usando o Azure CLI

Neste artigo, você aprenderá como configurar um conjunto de escala de máquina virtual com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga sku padrão existente na assinatura onde o conjunto de escala da máquina virtual será implantado.
- Uma Rede Virtual Azure para o conjunto de escala de máquinavirtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por usar a CLI localmente, este artigo requer que você tenha uma versão da versão 2.0.28 do Azure CLI ou posterior instalada. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Inscreva-se no Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar um conjunto de escala de máquina virtual com balanceador de carga existente

Substitua os valores entre parênteses com os nomes dos recursos em sua configuração.

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

O exemplo abaixo implanta um conjunto de escala de máquina virtual com:

- Conjunto de escala de máquina virtual chamado **myVMSS**
- Azure Load Balancer chamado **myLoadBalancer**
- Pool de back-end do balanceador de carga chamado **myBackendPool**
- Rede Virtual Azure chamada **myVnet**
- Sub-rede chamada **mySubnet**
- Grupo de recursos chamado **myResourceGroup**
- Imagem do Servidor Ubuntu para o conjunto de escala de máquina virtual

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
> Depois que o conjunto de escalas for criado, a porta backend não pode ser modificada para uma regra de balanceamento de carga usada por uma sonda de saúde do balanceador de carga. Para alterar a porta, você pode remover o teste de saúde atualizando o conjunto de escala da máquina virtual do Azure, atualizar a porta e, em seguida, configurar o teste de saúde novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e balanceador de carga, consulte:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
                                