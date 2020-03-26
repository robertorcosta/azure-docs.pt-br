---
title: 'Início Rápido: Criar um Load Balancer público – CLI do Azure'
titleSuffix: Azure Load Balancer
description: O início rápido mostra como criar um balanceador de carga público usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79477758"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um Standard Load Balancer para balancear a carga de VM usando a CLI do Azure

Este início rápido mostra como criar um Load Balancer público. Para testar o balanceador de carga, implante duas máquinas virtuais (VMs) executando o servidor do Ubuntu e balanceie a carga de um aplicativo Web entre as duas VMs.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupSLB* no local *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um endereço IP Público com redundância de zona Standard denominado *myPublicIP* em *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Para criar um endereço IP Público zonal no uso da Zona 1:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Use `-SKU Basic` para criar um IP Público Básico. Os IPs públicos Básicos não são compatíveis com o **Standard** Load Balancer. A Microsoft recomenda usar o **Standard** para cargas de trabalho de produção.

> [!IMPORTANT]
> O restante deste início rápido pressupõe que o SKU **Standard** é escolhido durante o processo de seleção de SKU acima.

## <a name="create-azure-load-balancer"></a>Criar o Azure Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:
  - um pool de IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - um pool de IP de back-end no qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  - uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  - uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Criar um Azure Load Balancer com [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) chamado **myLoadBalancer** que inclua um pool de front-end chamado **myFrontEnd**, um pool de back-end chamado **myBackEndPool** associado com o endereço IP público **myPublicIP** que você criou na etapa anterior. Use `--sku basic` para criar um IP Público Básico. A Microsoft recomenda o SKU Standard para cargas de trabalho de produção.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> O restante deste início rápido pressupõe que o SKU **Standard** é escolhido durante o processo de seleção de SKU acima.

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma investigação de integridade com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorar a integridade das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no pool de front-end *myFrontEnd* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar algumas VMs e poder testar o balanceador de carga, crie os recursos de suporte de rede virtual.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet* no *myResourceGroup* usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, as VMs no endereço de back-end precisam ter NICs que pertençam a um grupo de segurança de rede. Crie um grupo de segurança de rede para definir conexões de entrada para sua rede virtual.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede para permitir conexões de entrada pela porta 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>Criar NICs

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe-as com o endereço IP público e o grupo de segurança de rede. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você criará três máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga. Para verificar se o balanceador de carga foi criado com êxito, instale também o NGINX nas máquinas virtuais.

Se você estiver criando um Load Balancer Básico com um IP Público Básico, será necessário criar um Conjunto de Disponibilidade usando ([az vm availabilityset create](/cli/azure/network/nic) ao qual adicionar suas máquinas virtuais. Os Standard Load Balancers não exigem essa etapa adicional. A Microsoft recomenda usar o Standard.

### <a name="create-three-virtual-machines"></a>Criar três máquinas virtuais

Você pode usar um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js “Olá, Mundo” em uma máquina virtual Linux. No shell atual, crie um arquivo chamado cloud-init.txt e copie e cole a seguinte configuração no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

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

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Pode demorar alguns minutos para as VMs serem implantadas.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Testar o balanceador de carga](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um Standard Load Balancer, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, a investigação de integridade e testou o balanceador de carga. Para obter mais informações sobre o Azure Load Balancer, prossiga para os [tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Saiba mais sobre o [Load Balancer e as Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).