---
title: Implantar VMs do Linux em hosts dedicados usando a CLI
description: Implantar VMs em hosts dedicados usando a CLI do Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: dc772368de1a0f7d8a7d4f44b47ecafda70f0a70
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714841"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Implantar VMs em hosts dedicados usando a CLI do Azure
 

Este artigo orienta como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMs). 

Veja se você instalou a CLI do Azure versão 2.0.70 ou posterior e conecte-se a uma conta do Azure usando `az login`. 


## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-resource-group"></a>Criar grupo de recursos 
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie o grupo de recursos com az group create. O exemplo a seguir cria um grupo de recursos chamado *myDHResourceGroup* na localização *Leste dos EUA*.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Listar SKUs de host disponíveis em uma região
Nem todas as SKUs de host estão disponíveis em todas as regiões e zonas de disponibilidade. 

Liste a disponibilidade do host e quaisquer restrições de oferta antes de iniciar o provisionamento de hosts dedicados. 

```bash
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Criar um grupo de hosts 

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com hosts dedicados: 
- Alcance de várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você quer usar.
- Alcance de vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha ao seu grupo de hosts. Se você não quiser abranger domínios de falha no seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar tanto zonas de disponibilidade quanto domínios de falha. 

Neste exemplo, usaremos [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts usando zonas de disponibilidade e domínios de falha. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Outros exemplos

Você também pode usar [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts na zona de disponibilidade 1 (e sem domínios de falha).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
O exemplo a seguir usa [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts usando apenas domínios de falha (para uso em regiões onde as zonas de disponibilidades não têm suporte). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Criar um host 

Agora, vamos criar um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer o SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para o host dedicado.  

Para mais informações sobre preços e SKUs do host, consulte [Preços do Host Dedicado do Azure](https://aka.ms/ADHPricing).

Use [az vm host create](/cli/azure/vm/host#az-vm-host-create) para criar um host. Ao definir uma contagem de domínios de falha para seu grupo de hosts, você será solicitado a especificar o domínio de falha para o host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual 
Crie uma máquina virtual em um host dedicado usando [az vm create](/cli/azure/vm#az-vm-create). Se você especificou uma zona de disponibilidade ao criar o grupo de hosts, será necessário usar a mesma zona ao criar a máquina virtual.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> A máquina virtual será criada em estado de falha em um host que não tenha recursos suficientes. 


## <a name="check-the-status-of-the-host"></a>Verifique o status do host

Você pode verificar o status da integridade do host e quantas máquinas virtuais ainda poderá implantar no host com [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A saída parecerá com o seguinte:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportar como um modelo 
Você pode exportar um modelo se quiser criar um ambiente de desenvolvimento adicional usando os mesmos parâmetros ou um ambiente de produção corresponde. O Gerenciador de Recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Crie ambientes inteiros fazendo referência a esse modelo JSON. Você pode criar modelos JSON manualmente ou exportar um ambiente existente para criar o modelo JSON para você. Use [az group export](/cli/azure/group#az-group-export) para exportar seu grupo de recursos.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Esse comando cria o arquivo `myDHResourceGroup.json` no diretório de trabalho atual. Quando você cria um ambiente com base neste modelo, será solicitado que você informe todos os nomes de recursos. Você pode popular esses nomes em seu arquivo de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando `az group export`. Edite seu modelo JSON para especificar os nomes dos recursos, ou crie um arquivo parameters.json que especifica os nomes dos recursos.
 
Para criar um ambiente usando seu modelo, use [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Limpar 

Você é cobrado por hosts dedicados, mesmo se não houver máquinas virtuais implantadas. Você deve excluir os hosts que não está usando atualmente para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais que o utilizem. Exclua as VMs com [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Depois de excluir as VMs, você pode excluir o host com [az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Depois de excluir todos os hosts, você pode excluir os grupos de hosts com [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Você também pode excluir o grupo de recursos inteiro com um único comando. Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de hosts.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

- Para mais informações, consulte a visão geral de [hosts dedicados](dedicated-hosts.md).

- Você também pode criar hosts dedicados usando o [portal do Azure](dedicated-hosts-portal.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.
