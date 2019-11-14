---
title: Terminação SSL usando Aplicativo Azure gateway de CLI
description: Saiba como criar um gateway de aplicativo e adicionar um certificado para a terminação SSL usando a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6d1decdda096fb902ff605205d7193e1264e5170
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048007"
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Criar um gateway de aplicativo com terminação SSL usando a CLI do Azure

Você pode usar a CLI do Azure para criar um [gateway de aplicativo](application-gateway-introduction.md) com um certificado de [terminação de SSL](application-gateway-backend-ssl.md) que usa [conjunto de dimensionamentos de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para servidores back-end. Neste exemplo, o conjunto de dimensionamento contém duas instâncias de máquina virtual que são adicionadas ao pool de back-end padrão do gateway de aplicativo.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Configurar uma rede
> * Criar um gateway de aplicativo com o certificado
> * Criar um conjunto de dimensionamento de máquinas virtuais com o pool de back-end padrão

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou fazer upgrade, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para uso em produção, você deve importar um certificado válido assinado por um fornecedor confiável. Para este tutorial, você pode criar um certificado autoassinado e o arquivo pfx usando o comando openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Insira os valores que fazem sentido para o seu certificado. Você pode aceitar os valores padrão.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Insira a senha para o certificado. Neste exemplo, *Azure123456!* está sendo usado.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos usando [az group create](/cli/azure/group).

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupAG* no local *eastus*.

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

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Você pode usar [az network application-gateway creat](/cli/azure/network/application-gateway) para criar o gateway do aplicativo. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. 

O gateway de aplicativo é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que você criou anteriormente. Neste exemplo, você associa o certificado que você criou e sua senha ao criar o gateway de aplicativo. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Pode levar vários minutos para o gateway de aplicativo ser criado. Depois de criar o gateway de aplicativo, você pode ver esses novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra de roteamento padrão que está associada com *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais que fornece servidores para o pool de back-end padrão no gateway de aplicativo. As máquinas virtuais no conjunto de dimensionamento são associadas com *myBackendSubnet* e *appGatewayBackendPool*. Para criar um conjunto de dimensionamento, você pode usar [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, você pode usar [az network public-ip show](/cli/azure/network/public-ip). Copie o endereço IP público e, em seguida, cole-o na barra de endereços do seu navegador.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Aviso de segurança](./media/application-gateway-ssl-cli/application-gateway-secure.png)

Para aceitar o aviso de segurança se você usou um certificado autoassinado, selecione **Detalhes** e depois **Prosseguir para a página da Web**. Seu site de NGINX protegido é exibido, como no exemplo a seguir:

![Testar a URL de base no gateway do aplicativo](./media/application-gateway-ssl-cli/application-gateway-nginx.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Configurar uma rede
> * Criar um gateway de aplicativo com o certificado
> * Criar um conjunto de dimensionamento de máquinas virtuais com o pool de back-end padrão

Para saber mais sobre os gateways de aplicativo e seus recursos associados, prossiga até os artigos de instrução.
