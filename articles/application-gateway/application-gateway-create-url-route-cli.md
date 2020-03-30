---
title: Regras de roteamento baseadas em caminho susceita do URL usando o CLI - Azure Application Gateway
description: Saiba como criar regras de roteamento com base em caminhos de URL para um gateway de aplicativo e conjunto de dimensionamento de máquina virtual usando a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 5f75ae1104297c461584e061f5a94aecd987caad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246778"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Criar um gateway de aplicativo com regras de roteamento com base em caminhos de URL usando a CLI do Azure

Você pode usar a CLI do Azure para configurar as [Regras de roteamento baseadas em caminhos de URL](application-gateway-url-route-overview.md) quando você cria um [gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você cria pools de back-end usando um [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Em seguida, você criará regras de roteamento para certificar-se de que o tráfego da Web chega aos servidores apropriados nos pools.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicativo com o mapa de URL
> * Criar conjuntos de dimensionamento de máquinas virtuais com pools de back-end

![Exemplo de roteamento de URL](./media/application-gateway-create-url-route-cli/scenario.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Criar um grupo de recursos usando [az group create](/cli/azure/group).

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Criar a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet). Você pode adicionar a sub-rede denominada *myBackendSubnet* que é necessária para os servidores de back-end usando [az network vnet subnet create](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* usando [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Criar o gateway de aplicativo com o mapa de URL

Você pode usar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway do aplicativo denominado *myAppGateway*. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que você criou anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 O gateway de aplicativo pode demorar vários minutos para ser criado. Depois de criar o gateway de aplicativo, você pode ver esses novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Adicionar porta e pools de back-end de imagem e vídeo

Você pode adicionar os pools de back-end denominados *imagesBackendPool* e *videoBackendPool* para o seu gateway de aplicativo usando [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create). Adicionar a porta de front-end para os pools usando [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Adicionar o listener de back-end

Adicione o listener de back-end denominado *backendListener* que é necessário para rotear o tráfego usando [az network application-gateway http-listener create](/cli/azure/network/application-gateway).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Adicionar mapa de caminho da URL

Os mapas de caminho de URL certificam que as URLs específicas sejam roteadas para pools de back-end específicos. Você pode criar mapas de caminho de URL chamados *imagePathRule* e *videoPathRule* usando [az network application-gateway url-path-map create](/cli/azure/network/application-gateway) e [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Adicionar regra de roteamento

A regra de roteamento associa o mapa de URL ao listener que você criou. Você pode adicionar a regra denominada *rule2* usando [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, você cria três conjuntos de dimensionamento de máquinas virtuais que oferecem suporte a três pools de back-end que você criou. Os conjuntos de dimensionamento que você cria são denominados *myvmss1*, *myvmss2*, e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual nas quais você instala o NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, você pode usar [az network public-ip show](/cli/azure/network/public-ip). Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Como `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` ou `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar a URL de base no gateway de aplicativo](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Altere a `http://<ip-address>:8080/video/test.html` URL para o final da URL base e você deve ver algo como o seguinte exemplo:

![Testar a URL de imagens no gateway de aplicativo](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Altere a `http://<ip-address>:8080/video/test.html` URL para e você deve ver algo como o exemplo a seguir.

![Testar a URL de vídeo no gateway de aplicativo](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicativo com o mapa de URL
> * Criar conjuntos de dimensionamento de máquinas virtuais com pools de back-end

Para saber mais sobre os gateways de aplicativo e seus recursos associados, continue para os artigos de instrução.
