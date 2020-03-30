---
title: Sub-rede do Azure Traffic Manager é uma substituição usando o Azure CLI | Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a substituição da sub-rede do Gerenciador de Tráfego pode ser usada para substituir o método de roteamento de um perfil do Gerenciador de Tráfego para direcionar o tráfego para um ponto final com base no endereço IP do usuário final via intervalo IP predefinido para mapeamentos de ponto final.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938464"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Substituição da sub-rede do Gerenciador de Tráfego usando o Azure CLI

A substituição da sub-rede do Gerenciador de Tráfego permite alterar o método de roteamento de um perfil.  A adição de uma substituição direcionará o tráfego com base no endereço IP do usuário final com um intervalo IP predefinido para mapeamento de ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a substituição da sub-rede

Quando as substituições de sub-rede forem adicionadas a um perfil de gerenciador de tráfego, o Gerenciador de Tráfego primeiro verificará se há uma substituição de sub-rede para o endereço IP do usuário final. Se um for encontrado, a consulta de DNS do usuário será direcionada para o ponto final correspondente.  Se um mapeamento não for encontrado, o Gerenciador de tráfego voltará ao método de roteamento original do perfil. 

As faixas de endereço IP podem ser especificadas como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). As faixas de IP associadas a cada ponto final devem ser exclusivas desse ponto final. Qualquer sobreposição de faixas de IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gerenciador de Tráfego.

Existem dois tipos de perfis de roteamento que suportam substituições de sub-rede:

* **Geographic** - Se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele encaminhará a consulta para o ponto final, independentemente da saúde do ponto final.
* **Desempenho** - Se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele só encaminhará o tráfego para o ponto final se estiver saudável.  O Gerenciador de Tráfego voltará à heurística de roteamento de desempenho se o ponto final de substituição da sub-rede não estiver saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma substituição de sub-rede do Gerenciador de Tráfego

Para criar uma substituição de sub-rede do Gerenciador de tráfego, você pode usar o Azure CLI para adicionar as sub-redes para a substituição ao ponto final do Gerenciador de tráfego.

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, consulte [Install Azure CLI]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Atualize o ponto final do Gerenciador de tráfego com substituição de sub-rede.
Use o Azure CLI para atualizar seu ponto final com [a atualização de ponto final do gerenciador de tráfego de rede az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Você pode remover os intervalos de endereços IP executando a atualização de ponto final do [gerenciador de tráfego de rede az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) com a opção **--remover.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

Conheça o [método de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) da Subnet