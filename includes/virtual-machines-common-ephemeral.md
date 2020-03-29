---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155466"
---
Os discos do Sistema Operacional Efêmero são criados no armazenamento da máquina virtual local (VM) e não salvos no armazenamento remoto do Azure. Os discos efêmeros do Sistema Operacional funcionam bem para cargas de trabalho apátridas, onde os aplicativos são tolerantes a falhas individuais de VM, mas são mais afetados pelo tempo de implantação da VM ou pela reimagem das instâncias individuais de VM. Com o disco efêmero do sistema operacional, você recebe menor latência de leitura/gravação para o disco do sistema operacional e uma reimagem VM mais rápida. 
 
As principais características dos discos efêmeros são: 
- Ideal para aplicações apátridas.
- Eles podem ser usados tanto com o Marketplace quanto com imagens personalizadas.
- Capacidade de redefinir ou revisualizar vMs rapidamente e dimensionar instâncias de conjunto para o estado inicial de inicialização original.  
- Latência mais baixa, semelhante a um disco temporário. 
- Os discos do Sistema Operacional Efêmero são gratuitos, você não tem custo de armazenamento para o disco do Sistema Operacional.
- Eles estão disponíveis em todas as regiões do Azure. 
- O Disco efêmero do OS é suportado pela [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Principais diferenças entre discos de sistema operacional persistentes e efêmeros:

|                             | Disco do Sistema Operacional Persistente                          | Disco do SO Efêmero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho para disco do SISTEMA OPERACIONAL      | 2 TiB                                                                                        | Tamanho do cache para o tamanho da VM ou 2TiB, o que for menor. Para o tamanho do **cache em GiB,** consulte [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Tamanhos de VM suportados          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte ao tipo de disco           | Disco do sistema operacional gerenciado e não gerenciado                                                                | Somente disco do Sistema Operacional Gerenciado                                                               |
| Suporte de regiões              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Os dados do disco do SISTEMA OPERACIONAL gravados no disco DO SSão São armazenados no Azure Storage                                  | Os dados gravados no disco DO SSão São armazenados no armazenamento VM local e não são persistidos no Armazenamento Azure. |
| Estado stop-deallocated      | As VMs e as instâncias de conjunto de escala podem ser desalocadas e reiniciadas a partir do estado stop-deallocated | As VMs e as instâncias de conjunto de escala não podem ser desalocadas                                  |
| Suporte especializado de disco do SISTEMA OPERACIONAL | Sim                                                                                          | Não                                                                                 |
| Redimensionamento do disco DO S              | Suportado durante a criação de VM e depois que a VM é stop-deallocated                                | Suportado apenas durante a criação de VM                                                  |
| Redimensionamento para um novo tamanho de VM   | Os dados do disco do SISTEMA OPERACIONAL são preservados                                                                    | Os dados do disco do Sistema Operacional são excluídos, o sistema operacional é reprovisionado                                      |

## <a name="size-requirements"></a>Requisitos de tamanho

Você pode implantar imagens de VM e instância até o tamanho do cache da VM. Por exemplo, as imagens padrão do Windows Server do mercado são de cerca de 127 GiB, o que significa que você precisa de um tamanho de VM que tenha um cache maior que 127 GiB. Neste caso, o [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Neste caso, o Standard_DS3_v2 é o menor tamanho da série DSv2 que você pode usar com esta imagem. As imagens básicas do Linux no Marketplace `[smallsize]` e no Windows Server que são denotadas por tendem a ser em torno de 30 GiB e podem usar a maioria dos tamanhos de VM disponíveis.

Os discos efêmeros também exigem que o tamanho da VM suporte o armazenamento Premium. Os tamanhos geralmente (mas nem `s` sempre) têm um no nome, como DSv2 e EsV3. Para obter mais informações, consulte [os tamanhos da VM do Azure](../articles/virtual-machines/linux/sizes.md) para obter detalhes sobre quais tamanhos suportam o armazenamento Premium.

## <a name="powershell"></a>PowerShell

Para usar um disco efêmero para uma implantação de VM PowerShell, use [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) na configuração vm. Defina `-DiffDiskSetting` `Local` o `-Caching` `ReadOnly`para e para .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implantações de conjunto de escala, use o [cmdlet Set-AzVmsStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) em sua configuração. Defina `-DiffDiskSetting` `Local` o `-Caching` `ReadOnly`para e para .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar um disco efêmero para uma `--ephemeral-os-disk` implantação de VM CLI, `true` defina o `ReadOnly`parâmetro em [az vm criar](/cli/azure/vm#az-vm-create) para e o `--os-disk-caching` parâmetro para .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Para conjuntos de escala, `--ephemeral-os-disk true` você usa o mesmo parâmetro para [az-vmss-create](/cli/azure/vmss#az-vmss-create) e define o `--os-disk-caching` parâmetro para `ReadOnly`.

## <a name="portal"></a>Portal   

No portal Azure, você pode optar por usar discos efêmeros ao implantar uma VM abrindo a seção **Avançado** da guia **Discos.** Para **usar o disco efêmero do sistema operacional** selecione **Sim**.

![Captura de tela mostrando o botão de rádio para escolher usar um disco efêmero do sistema operacional](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção de usar um disco efêmero estiver apagada, você pode ter selecionado um tamanho de VM que não tenha um tamanho de cache maior que a imagem do SISTEMA OPERACIONAL ou que não suporte armazenamento Premium. Volte para **a** página básica e tente escolher outro tamanho de VM.

Você também pode criar conjuntos de escala com discos efêmeros do sistema operacional usando o portal. Apenas certifique-se de selecionar um tamanho de VM com um tamanho de cache grande o suficiente e, em seguida, em **Usar disco efêmero do sistema operacional** selecione **Sim**.

![Captura de tela mostrando o botão de rádio para escolher usar um disco efêmero do Sistema Operacional para o seu conjunto de escalas](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implantação do modelo de conjunto de escalas  
O processo para criar um conjunto de escalas que usa `diffDiskSettings` um disco `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` efêmero do SISTEMA OPERACIONAL é adicionar a propriedade ao tipo de recurso no modelo. Além disso, a política de `ReadOnly` cache deve ser definida para o disco efêmero do sistema operacional. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Implantação do modelo VM 
Você pode implantar uma VM com um disco efêmero do sistema operacional usando um modelo. O processo para criar uma VM que usa discos efêmeros do Sistema Operacional é adicionar a `diffDiskSettings` propriedade ao tipo de recurso Microsoft.Compute/virtualMachines no modelo. Além disso, a política de `ReadOnly` cache deve ser definida para o disco efêmero do sistema operacional. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Reimagem de uma VM usando REST
Você pode reimaginar uma instância da Máquina Virtual com disco efêmero do SISTEMA OPERACIONAL usando a API REST como descrito abaixo e via Portal Azure indo para o painel Visão Geral da VM. Para conjuntos de escala, a reimagem já está disponível através do Powershell, CLI e do portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Qual é o tamanho dos discos de sistema operacional locais?**

R: Nós suportamos plataforma e imagens personalizadas, até o tamanho do cache vm, onde todas as leituras/gravações para o disco do SO serão locais no mesmo nó que a Máquina Virtual. 

**P: O disco efêmero do sistema operacional pode ser redimensionado?**

R: Não, uma vez que o disco do sistema operacional efêmero seja provisionado, o disco do sistema operacional não pode ser redimensionado. 

**P: Posso anexar um disco gerenciado a um VM efêmero?**

R: Sim, você pode anexar um disco de dados gerenciado a uma VM que usa um disco efêmero do sistema operacional. 

**P: Todos os tamanhos de VM serão suportados para discos efêmeros do sistema operacional?**

R: Não, todos os tamanhos de VM de armazenamento premium são suportados (DS, ES, FS, GS e M) exceto os tamanhos série B, Série N e Série H.  
 
**P: O disco efêmero do Sistema Operacional pode ser aplicado às VMs e aos conjuntos de escala existentes?**

R: Não, o disco efêmero do sistema operacional só pode ser usado durante a criação de VM e conjunto de escalas. 

**P: Você pode misturar discos efêmeros e normais do sistema operacional em um conjunto de escalas?**

R: Não, você não pode ter uma mistura de instâncias de disco do Sistema Operacional efêmeras e persistentes dentro do mesmo conjunto de escala. 

**P: O disco efêmero do sistema operacional pode ser criado usando Powershell ou CLI?**

R: Sim, você pode criar VMs com disco efêmero do SISTEMA OPERACIONAL usando REST, Templates, PowerShell e CLI.

**P: Quais recursos não são suportados com disco efêmero do sistema operacional?**

A: Os discos efêmeros não suportam:
- Capturando imagens de VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Serviço de Backup do Azure
- Azure Site Recovery  
- Troca de discos do SISTEMA OPERACIONAL 
