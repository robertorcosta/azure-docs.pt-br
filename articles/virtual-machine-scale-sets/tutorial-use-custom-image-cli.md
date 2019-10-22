---
title: Tutorial - Usar uma imagem de VM personalizada em um conjunto de dimensionamento com a CLI 2.0 do Azure | Microsoft Docs
description: Aprenda a usar a CLI 2.0 do Azure para criar uma imagem de VM personalizada que pode ser usada para implantar um conjunto de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6368a36eaa2e6832f22a13f20bc35d66c4425b4b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311571"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Criar e usar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. Para reduzir a quantidade de tarefas depois que as instâncias de VM forem implantadas, é possível usar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui todas as instalações ou configurações de aplicativo necessárias. Todas as instâncias de VM criadas no conjunto de dimensionamento usam a imagem de VM personalizada e estão prontas para atender ao tráfego do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada
> * Implantar um conjunto de dimensionamento que usa a imagem de VM personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.29 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Criar e configurar uma VM de origem

>[!NOTE]
> Este tutorial analisa o processo de criação e uso de uma imagem de VM generalizada. Não há suporte para criar um conjunto de dimensionamento a partir de uma imagem de VM especializada.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group); em seguida, crie uma VM com [az vm create](/cli/azure/vm). Essa VM é usada como a origem para uma imagem de VM personalizada. O exemplo abaixo inicia uma VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

O endereço IP público da VM é mostrado na saída do comando [az vm create](/cli/azure/vm). SSH para o endereço IP público da VM da seguinte maneira:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Para personalizar sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implantada, terá todos os pacotes necessários para executar um aplicativo Web. Use `apt-get` para instalar o *NGINX* da seguinte forma:

```bash
sudo apt-get install -y nginx
```

A etapa final para preparar sua VM para usar como uma imagem personalizada é desprovisionar a VM. Essa etapa remove informações específicas de computador da VM e possibilita a implantação de várias VMs de uma única imagem. Durante o desprovisionamento da VM, o nome do host é redefinido como *localhost.localdomain*. As chaves de host de SSH, as configurações de nameserver, a senha raiz e as concessões de DHCP em cache também são excluídas.

Para desprovisionar a VM, use o agente de VM do Azure (*waagent*). O agente de VM do Azure é instalado em cada VM e usado para se comunicar com a plataforma Azure. O parâmetro `-force` manda o agente aceitar solicitações para redefinir as informações específicas do computador.

```bash
sudo waagent -deprovision+user -force
```

Feche a conexão SSH com a máquina virtual:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Criar uma imagem de VM personalizada da VM de origem
A VM de origem agora é personalizada com o servidor Web Nginx instalado. Vamos criar a imagem de VM personalizada para usar com um conjunto de dimensionamento.

Para criar uma imagem, a VM precisará ser desalocada. Desaloque a VM com [az vm deallocate](/cli//azure/vm). Depois, defina o estado da VM como generalizado usando [az vm generalize](/cli//azure/vm) para que a plataforma do Azure saiba que a VM está pronta para usar uma imagem personalizada. Você só pode criar uma imagem por meio de uma VM generalizada:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Pode levar alguns minutos para desalocar e generalizar a VM.

Agora crie uma imagem da VM com [az image create](/cli//azure/image). O exemplo abaixo cria uma imagem chamada *myImage* da sua VM:

> [OBSERVAÇÃO] Se o grupo de recursos e a localização da máquina virtual forem diferentes, você poderá adicionar o parâmetro `--location` aos comandos abaixo para especificar a localização da VM de origem usada para criar a imagem. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Crie um conjunto de dimensionamento com [az vmss create](/cli/azure/vmss#az-vmss-create). Em vez de uma imagem de plataforma, como *UbuntuLTS* ou *CentOS*, especifique o nome da sua imagem de VM personalizada. O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que usa a imagem personalizada denominada *myImage* da etapa anterior:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para permitir que o tráfego alcance o conjunto de dimensionamento e verifique se o servidor Web está funcionando corretamente, crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo abaixo cria uma regra denominada *myLoadBalancerRuleWeb* que permite o tráfego na porta *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver seu conjunto de dimensionamento em ação, obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O seguinte exemplo obtém o endereço IP de *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Digite o endereço IP público no seu navegador da Web. A página da Web padrão do NGINX é exibida da seguinte forma:

![NGINX sendo executado a partir de uma imagem de VM personalizada](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar e usar uma imagem de VM personalizada para seus conjuntos de dimensionamento com a CLI 2.0 do Azure:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada
> * Implantar um conjunto de dimensionamento que usa a imagem de VM personalizada

Siga para o próximo tutorial para saber como implantar aplicativo ao seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implantar aplicativos em seus conjuntos de dimensionamento](tutorial-install-apps-cli.md)
