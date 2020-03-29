---
title: Crie e gerencie máquinas virtuais no DevTest Labs com o Azure CLI
description: Saiba como usar o Azure DevTest Labs para criar e gerenciar máquinas virtuais com a CLI do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167062"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure
Esse quickstart irá guiá-lo através da criação, partida, conexão, atualização e limpeza de uma máquina de desenvolvimento em seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, as instruções poderão ser encontradas [aqui](devtest-lab-create-lab.md).

* [Instale a CLI do Azure](/cli/azure/install-azure-cli). Para iniciar, execute az login para criar uma conexão com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Antes de executar os comandos relacionados ao DevTest Labs, `az account set` defina o contexto azure apropriado usando o comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma `az lab vm create`máquina virtual é: . O grupo de recursos para o laboratório, nome do laboratório e nome da máquina virtual são todos necessários. O resto dos argumentos mudam dependendo do tipo de máquina virtual.

O comando a seguir cria uma imagem baseada no Windows do Azure Market Place. O nome da imagem é o mesmo que você veria ao criar uma máquina virtual usando o portal Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O comando a seguir cria uma máquina virtual baseada em uma imagem personalizada disponível no laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O argumento **do tipo de imagem** mudou de **galeria** para **personalizado**. O nome da imagem corresponde ao que você vê se você fosse criar a máquina virtual no portal Azure.

O comando a seguir cria uma VM a partir de uma imagem de mercado com autenticação ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Você também pode criar máquinas virtuais baseadas em fórmulas definindo o parâmetro **tipo imagem** para **fórmula**. Se você precisar escolher uma rede virtual específica para sua máquina virtual, use os parâmetros **de nome vnet** e **sub-rede.** Para obter mais informações, consulte [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verifique se a VM está disponível.
Use `az lab vm show` o comando para verificar se a VM está disponível antes de iniciar e se conectar a ela. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e conectar-se à máquina virtual
O comando exemplo a seguir inicia uma VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Conectar-se a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Área de Trabalho Remota](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
O comando de amostra a seguir aplica artefatos a uma VM:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Listar artefatos disponíveis no laboratório

Para listar artefatos disponíveis em uma VM em um laboratório, execute os seguintes comandos.

**Cloud Shell - PowerShell**: observe o\`uso do backtick ( ) antes do $ em $expand (ou seja, '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: observe\\o uso do caractere barra ( ) na frente de $ no comando. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Saída de exemplo: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e excluir a máquina virtual    
O seguinte comando de amostra pára um VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Exclua uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
Veja o conteúdo a seguir: [Documentação do Azure CLI para Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
