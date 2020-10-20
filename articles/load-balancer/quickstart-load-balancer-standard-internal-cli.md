---
title: 'Início Rápido: Criar um balanceador de carga interno – CLI do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga interno usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: df1db5467dadcd127141708fa33147769f1f50a7
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047847"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga interno para balancear a carga de VMs usando a CLI do Azure

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

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

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

Crie dois adaptadores de rede com [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

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
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor.
* Duas máquinas virtuais a serem usadas como servidores back-end para o balanceador de carga.

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
    --admin-user azureuser \
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
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Pode levar alguns minutos para que as VMs sejam implantadas.

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
* Associado à rede virtual **myVNet**.
* Associado à sub-rede de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
>[!NOTE]
>As máquinas virtuais do pool de back-end não terão conectividade de saída com a Internet com essa configuração. </br> Para obter mais informações sobre como fornecer a conectividade de saída, confira: </br> **[Conexões de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga somente de saída](egress-only.md)** </br> **[O que é NAT de Rede Virtual?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

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

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

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

Crie dois adaptadores de rede com [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

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

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor. 
* Conjunto de disponibilidade para as máquinas virtuais
* Duas máquinas virtuais a serem usadas como servidores back-end para o balanceador de carga.

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
    --admin-user azureuser \
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
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Pode levar alguns minutos para que as VMs sejam implantadas.

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
* Associado à rede virtual **myVNet**.
* Associado à sub-rede de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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

---

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-azure-bastion-public-ip"></a>Criar um IP público do Azure Bastion

Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Criar uma sub-rede do Azure Bastion

Use [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) para criar uma sub-rede:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço **10.1.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **myResourceGroupLB**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group myResourceGroupLB \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Criar um host do Azure Bastion
Use [az network bastion create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) para criar um bastion host:

* Chamado **myBastionHost**
* Em **myResourceGroupLB**
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
  az network bastion create \
    --resource-group myResourceGroupLB \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Levará alguns minutos para que o bastion host seja implantado.

### <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Crie os adaptadores de rede com [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* Chamado **myNicTestVM**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Crie a máquina virtual com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* Chamada **myTestVM**.
* No grupo de recursos **myResourceGroupLB**.
* Anexada ao adaptador de rede **myNicTestVM**.
* Imagem de máquina virtual **Win2019Datacenter**.
* Escolha valores para **\<adminpass>** e **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Pode levar alguns minutos para que a máquina virtual seja implantada.

### <a name="test"></a>Teste

1. [Entre](https://portal.azure.com) no portal do Azure.

1. Encontre o endereço IP privado para o balanceador de carga na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer**.

2. Anote ou copie o endereço ao lado de **Endereço IP Privado** na **Visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, na lista de recursos, selecione **myTestVM**, que está localizada no grupo de recursos **myResourceGroupLB**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Insira o endereço IP da etapa anterior na barra de endereços do navegador. A página padrão do servidor Web do IIS é exibida no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Criar um balanceador de carga interno Standard" border="true":::
   
Para ver o balanceador de carga distribuir o tráfego entre as três VMs, personalize a página padrão do servidor Web do IIS de cada VM e force a atualização do navegador da Web no computador cliente.

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

Para saber mais sobre o Azure Load Balancer, vá para 
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)