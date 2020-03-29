---
title: Use cloud-init para configurar uma partição de swap em uma VM Linux
description: Como usar o cloud-init para configurar uma partição de swap em um VM Linux durante a criação com o Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969195"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Use cloud-init para configurar uma partição de swap em uma VM Linux
Este artigo mostra como usar [o cloud-init](https://cloudinit.readthedocs.io) para configurar a partição de swap em várias distribuições Linux. A partição de swap foi tradicionalmente configurada pelo Agente Linux (WALA) com base em quais distribuições exigiam uma.  Este documento delineará o processo de construção da partição de swap sob demanda durante o tempo de provisionamento usando cloud-init.  Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Criar partição de swap para imagens baseadas no Ubuntu
Por padrão no Azure, as imagens da galeria Ubuntu não criam partições de swap. Para habilitar a configuração de partição de swap durante o tempo de provisionamento da VM usando cloud-init - consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) na wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Criar partição de swap para imagens baseadas em Red Hat e CentOS

Crie um arquivo em seu shell atual chamado *cloud_init_swappart.txt* e cole a seguinte configuração. Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud_init_swappart.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Verifique se o arquivo cloud-init inteiro foi copiado corretamente, principalmente a primeira linha.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local eastus.*

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_swappart.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verificar partição de swap foi criada
Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Uma vez que você tenha SSH'ed no vm, verifique se a partição de swap foi criada

```bash
swapon -s
```

A saída desse comando deve ter esta aparência:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se você tiver uma imagem Azure existente que tenha uma partição de swap configurada e quiser alterar a configuração de partição de swap para novas imagens, você deve remover a partição de swap existente. Consulte o documento 'Personalizar imagens para provisionar por cloud-init' para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)
