---
title: 'Início Rápido: Criar um balanceador de carga público – CLI do Azure'
titleSuffix: Azure Load Balancer
description: O início rápido mostra como criar um balanceador de carga público usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: d78b67cbd811ae0f3b7cea8aec119d05464c124a
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047787"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga público para balancear cargas de VMs usando a CLI do Azure

Veja uma introdução ao Azure Load Balancer usando a CLI do Azure para criar um balanceador de carga público e três máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- CLI do Azure instalada localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

* Chamado **myResourceGroupLB**. 
* Na localização **eastus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **myResourceGroupLB**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Chamado **myNSG**.
* No grupo de recursos **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **myResourceGroupLB**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Chamada **myNicVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamada **myNicVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Chamada **myNicVM3**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor.
* Três máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga.

### <a name="create-cloud-init-configuration-file"></a>Criar arquivo de configuração cloud-init

Use um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. 

No shell atual, crie um arquivo chamado cloud-init.txt. Copie e cole a configuração a seguir no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamada **myVM3**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM3**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Pode levar alguns minutos para que as VMs sejam implantadas.

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. 

Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para:

* Criar um endereço IP público com redundância de zona padrão chamado **myPublicIP**.
* Em **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Para criar um endereço IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Criar Standard Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** criado na etapa anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Habilite a SNAT (conversão de endereços de rede de origem) de saída usando o endereço IP de front-end.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):

#### <a name="vm1"></a>VM1
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM1** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM2** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM3** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Criar configurações de regra de saída
As regras de saída do balanceador de carga configuram o SNAT de saída para as VMs no pool de back-end. 

Para saber mais sobre as conexões de saída, confira [Conexões de saída no Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Criar endereço IP público de saída ou prefixo do endereço IP.

Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um IP para a conectividade de saída.  

Use [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) para criar um prefixo de IP público para a conectividade de saída.

Para saber mais sobre como dimensionar o NAT de saída e a conectividade de saída, confira [Dimensionar o NAT de saída com vários endereços IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>IP público

* Chamado **myPublicIPOutbound**.
* Em **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Para criar um endereço IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Chamado **myPublicIPPrefixOutbound**.
* Em **myResourceGroupLB**.
* Comprimento do prefixo de **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Para criar um prefixo de IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Criar configuração de IP de front-end de saída

Crie uma configuração de IP de front-end com [az network lb frontend-ip create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Selecione os comandos do IP público ou do prefixo de IP público com base na decisão na etapa anterior.

#### <a name="public-ip"></a>IP público

* Chamada **myFrontEndOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao endereço IP público **myPublicIPOutbound**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Chamada **myFrontEndOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associado ao prefixo do IP público **myPublicIPPrefixOutbound**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Criar pool de saída

Crie um pool de saída com [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create):

* Chamado **myBackEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Criar uma regra de saída

Crie uma regra de saída para o pool de back-end de saída com [az network lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* Chamada **myOutboundRule**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao balanceador de carga **myLoadBalancer**
* Associada ao front-end **myFrontEndOutbound**.
* Protocolo **Todos**.
* Tempo limite de ociosidade de **15**.
* **10000** portas de saída.
* Associada ao pool de back-end **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Adicionar máquinas virtuais a um pool de saída

Adicione as máquinas virtuais ao pool de saída com [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* No pool de endereços de back-end **myBackEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM1** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* No pool de endereços de back-end **myBackEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM2** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* No pool de endereços de back-end **myBackEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM3** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **myResourceGroupLB**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Chamado **myNSG**.
* No grupo de recursos **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **myResourceGroupLB**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Chamada **myNicVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamada **myNicVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Chamada **myNicVM3**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor. 
* Conjunto de disponibilidade para as máquinas virtuais
* Três máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga.


Para verificar se o balanceador de carga foi criado com êxito, instale o NGINX nas máquinas virtuais.

### <a name="create-cloud-init-configuration-file"></a>Criar arquivo de configuração cloud-init

Use um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. 

No shell atual, crie um arquivo chamado cloud-init.txt. Copie e cole a configuração a seguir no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie a conjunto de disponibilidade com [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Chamado **myAvSet**.
* No grupo de recursos **myResourceGroupLB**.
* Localização **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* No conjunto de disponibilidade **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamada **myVM3**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicVM3**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Pode levar alguns minutos para que as VMs sejam implantadas.


## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. 

Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para:

* Criar um endereço IP público com redundância de zona padrão chamado **myPublicIP**.
* Em **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** criado na etapa anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM1** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM2** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associada ao adaptador de rede **myNicVM3** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, use [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testar o balanceador de carga" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Use o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando deixarem de ser necessários.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido

* Você criou um balanceador de carga público padrão ou público
* Anexou máquinas virtuais. 
* Configurou a regra de tráfego e a investigação de integridade do balanceador de carga.
* Testou o balanceador de carga.

Para saber mais sobre o Azure Load Balancer, vá para...
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)
