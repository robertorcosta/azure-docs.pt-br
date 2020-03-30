---
title: Endereço IP estático para grupo de contêineres
description: Crie um grupo de contêineres em uma rede virtual e use um gateway de aplicativo do Azure para expor um endereço IP frontend estático a um aplicativo web contêiner
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481782"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Exponha um endereço IP estático para um grupo de contêineres

Este artigo mostra uma maneira de expor um endereço IP estático e público para um [grupo de contêineres](container-instances-container-groups.md) usando um [gateway de aplicativo](../application-gateway/overview.md)Azure . Siga essas etapas quando precisar de um ponto de entrada estático para um aplicativo contêiner voltado para o exterior que é executado em Instâncias de Contêiner do Azure. 

Neste artigo você usa o Azure CLI para criar os recursos para este cenário:

* Uma Rede virtual do Azure
* Um grupo de contêineres implantado [na rede virtual (visualização)](container-instances-vnet.md) que hospeda um pequeno aplicativo web
* Um gateway de aplicativo com um endereço IP frontend público, um ouvinte para hospedar um site no gateway e uma rota para o grupo de contêineres backend

Enquanto o gateway de aplicativo for executado e o grupo de contêineres expor um endereço IP privado estável na sub-rede delegada da rede, o grupo de contêineres está acessível neste endereço IP público.

> [!NOTE]
> O Azure cobra por um gateway de aplicativo com base na quantidade de tempo que o gateway está provisionado e disponível, bem como na quantidade de dados que processa. Veja [preços](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Criar rede virtual

Em um caso típico, você já pode ter uma rede virtual Azure. Se você não tiver um, crie um como mostrado com os seguintes comandos de exemplo. A rede virtual precisa de sub-redes separadas para o gateway de aplicativo e o grupo de contêineres.

Se você precisar de um, crie um grupo de recursos do Azure. Por exemplo: 

```azureci
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com o comando [az network vnet create][az-network-vnet-create]. Este comando cria a sub-rede *myAGSubnet* na rede.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Use o comando [a net vnet da rede az para][az-network-vnet-subnet-create] criar uma sub-rede para o grupo de contêineres backend. Aqui se chama *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Use o comando [az network public-ip create][az-network-public-ip-create] para criar um recurso IP público estático. Em uma etapa posterior, este endereço é configurado como a parte frontal do gateway do aplicativo.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Criar grupo de contêineres

Execute o seguinte [az container create][az-container-create] para criar um grupo de contêineres na rede virtual que você configurou na etapa anterior. 

O grupo é implantado na sub-rede *myACISubnet* e contém uma `aci-helloworld` única instância chamada *appcontainer* que puxa a imagem. Como mostrado em outros artigos na documentação, esta imagem embala um pequeno aplicativo web escrito em Node.js que serve uma página HTML estática. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Quando implantado com sucesso, o grupo de contêineres é atribuído a um endereço IP privado na rede virtual. Por exemplo, execute o seguinte comando [az container show][az-container-show] para recuperar o endereço IP do grupo:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

A saída é semelhante a: `10.0.2.4`.

Para usar em uma etapa posterior, salve o endereço IP em uma variável de ambiente:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Criar um gateway de aplicativo

Crie um gateway de aplicativo na rede virtual, seguindo as etapas do [gateway de aplicativo quickstart](../application-gateway/quick-create-cli.md). O comando [aseguir az network application-gateway create][az-network-application-gateway-create] cria um gateway com um endereço IP frontend público e uma rota para o grupo de contêiner backend. Consulte a [documentação](/azure/application-gateway/) do Application Gateway para obter detalhes sobre as configurações do gateway.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Pode levar até 15 minutos para o Azure criar o gateway de aplicativo. 

## <a name="test-public-ip-address"></a>Teste o endereço IP público
  
Agora você pode testar o acesso ao aplicativo web em execução no grupo de contêineres atrás do gateway do aplicativo.

Execute o comando [az network public-ip show][az-network-public-ip-show] para recuperar o endereço IP público do gateway:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Output é um endereço IP `52.142.18.133`público, semelhante a: .

Para exibir o aplicativo web em execução quando configurado com sucesso, navegue até o endereço IP público do gateway no seu navegador. O acesso bem-sucedido é semelhante a:

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Próximas etapas

* Veja um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) para criar um grupo de contêineres com uma instância de contêiner WordPress como um servidor back-end atrás de um gateway de aplicativo.
* Você também pode configurar um gateway de aplicativo com um certificado para rescisão SSL. Veja a [visão geral](../application-gateway/ssl-overview.md) e o [tutorial](../application-gateway/create-ssl-portal.md).
* Dependendo do seu cenário, considere usar outras soluções de balanceamento de carga do Azure com instâncias de contêiner do Azure. Por exemplo, use [o Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para distribuir tráfego em várias instâncias de contêineres e em várias regiões. Veja esta [postagem de blog](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show