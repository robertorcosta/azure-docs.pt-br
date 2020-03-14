---
title: Capturar uma imagem de uma VM do Linux usando CLI do Azure
description: Capture uma imagem de uma VM do Azure a ser usada para implantações em massa usando a CLI do Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250394"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou de um VHD

Para criar várias cópias de uma máquina virtual (VM) para uso no Azure, capture uma imagem da VM ou do VHD do sistema operacional. Para criar uma imagem para implantação, você precisará remover informações pessoais da conta. Nas etapas a seguir, desprovisione uma VM existente, desaloque e crie uma imagem. Você pode usar essa imagem para criar VMs em qualquer grupo de recursos dentro da sua assinatura.

Para criar uma cópia da VM Linux existente para backup ou depuração ou então carregar um VHD Linux especializado de uma VM local, consulte [Carregar e criar uma VM Linux com base em uma imagem de disco personalizada](upload-vhd.md).  

Você pode usar o serviço do **Construtor de imagem de VM do Azure (visualização pública)** para criar sua imagem personalizada, sem necessidade de aprender sobre as ferramentas ou instalar pipelines de compilação, simplesmente fornecendo uma configuração de imagem, e o construtor de imagem criará a imagem. Para obter mais informações, consulte [introdução com o construtor de imagem de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Você precisará dos seguintes itens antes de criar uma imagem:

* Uma VM do Azure criada no modelo de implantação do Gerenciador de Recursos usando discos gerenciados. Se você ainda não criou uma VM Linux, você pode usar o [portal](quick-create-portal.md), da [CLI do Azure](quick-create-cli.md), ou os modelos do [Gerenciador de Recursos ](create-ssh-secured-vm-from-template.md). Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md), aplicar atualizações e instalar aplicativos. 

* A versão mais recente da[CLI do Azure](/cli/azure/install-az-cli2) instalada e estar conectado a uma conta do Azure com [login az](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Prefere um tutorial?

Para uma versão simplificada deste artigo e para testar, avaliando ou aprendendo sobre VMs no Azure, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).  Caso contrário, continue lendo aqui para obter o panorama completo.


## <a name="step-1-deprovision-the-vm"></a>Etapa 1: desprovisionar a VM
Primeiro, você vai desprovisionar a VM usando o agente de VM do Azure para excluir dados e arquivos específicos do computador. Use o comando `waagent` com o `-deprovision+user` parâmetro em sua VM do Linux de origem. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](../extensions/agent-linux.md).

1. Conecte-se à VM do Linux com um cliente SSH.
2. Na janela SSH, digite o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Execute este comando apenas em uma VM que você irá capturar como uma imagem. Esse comando não garante que a imagem é declarada com relação a todas as informações confidenciais ou está disponível para redistribuição. O `+user` parâmetro também remove a última conta de usuário provisionada. Para manter as credenciais de conta de usuário na VM, use apenas `-deprovision`.
 
3. Insira **y** para continuar. Você pode adicionar o parâmetro `-force` para evitar esta etapa de confirmação.
4. Depois de concluir o comando, insira **sair** para fechar o cliente SSH.  A VM ainda estará em execução neste ponto.

## <a name="step-2-create-vm-image"></a>Etapa 2: Criar a imagem de VM
Use a CLI do Azure para marcar a VM como generalizada e capturar a imagem. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myVnet* e *myVM*.

1. Desaloque a VM desprovisionada com [az vm deallocate](/cli/azure/vm). O exemplo a seguir desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Aguarde até que a VM seja completamente desalocada antes de prosseguir. Isso pode demorar alguns minutos para ser concluído.  A VM é desligada durante a desalocação.

2. Marque a VM como generalizada com [az vm generalize](/cli/azure/vm). O exemplo a seguir marca a VM denominada *myVM* no grupo de recursos chamado *myResourceGroup* como generalizada.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Uma VM que foi generalizada não pode mais ser reiniciada.

3. Crie uma imagem do recurso da VM com [az image create](/cli/azure/image#az-image-create). O exemplo a seguir cria uma imagem chamada *myImage* no grupo de recursos denominado *myResourceGroup* usando o recurso VM denominado *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos da VM de origem. Você pode criar VMs em qualquer grupo de recursos em sua assinatura desde esta imagem. De uma perspectiva de gerenciamento, você poderá criar um grupo de recursos específicos para seus recursos de máquina virtual e imagens.
   >
   > Se você quiser armazenar a imagem no armazenamento com resiliência de zona, será necessário criá-la em uma região que dê suporte para [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro `--zone-resilient true`.
   
Esse comando retorna JSON que descreve a imagem da VM. Salve essa saída para referência posterior.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Etapa 3: criar uma VM com base na imagem capturada
Criar uma VM usando a imagem criada com [criar vm az](/cli/azure/vm). O exemplo a seguir cria uma VM denominada *myVMDeployed* da imagem denominada *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criação da VM em outro grupo de recursos 

Com os discos gerenciados, você pode criar VMs de uma imagem em qualquer grupo de recursos em sua assinatura. Para criar uma máquina virtual em um grupo de recursos diferente do da imagem, especifique a ID de recurso completa para a imagem. Use [az image list](/cli/azure/image#az-image-list) para exibir uma lista de imagens. A saída deverá ser semelhante ao seguinte exemplo:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo a seguir usa [criar vm az](/cli/azure/vm#az-vm-create) para criar uma VM em um grupo de recursos que não seja a imagem de origem, especificando a ID do recurso de imagem.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Etapa 4: verificar a implantação

SSH para a máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP ou o FQDN da VM com [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Próximas etapas
Você pode criar várias máquinas virtuais da sua imagem de VM de origem. Para fazer alterações em sua imagem: 

- Crie uma VM usando sua imagem.
- Faça quaisquer atualizações ou alterações de configuração.
- Siga as etapas novamente para desprovisionar, desalocar, generalizar e criar uma imagem.
- Use essa nova imagem para implantações futuras. Você pode excluir a imagem original.

Para saber mais sobre como gerenciar suas VMs com a CLI, veja [CLI do Azure](/cli/azure).
