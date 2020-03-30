---
title: Implantar VMs Linux para hosts dedicados usando a CLI
description: Implante VMs para hosts dedicados usando o Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127685"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Implantar VMs para hosts dedicados usando o Azure CLI
 

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) ao Azure para hospedar suas máquinas virtuais (VMs). 

Certifique-se de que você instalou a versão 2.0.70 do Azure CLI `az login`ou posterior, e fez o fizer o fizer em uma conta do Azure usando . 


## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em hosts dedicados.
- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.
 

## <a name="create-resource-group"></a>Criar grupo de recursos 
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie o grupo de recursos com az group create. O exemplo a seguir cria um grupo de recursos chamado *myDHResourceGroup* na localização *leste dos EUA.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Criar um grupo de hosts 

Um **grupo anfitrião** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de host em uma região e uma região de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Span em várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de hospedagem em cada uma das zonas que deseja usar.
- Spane vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínio de falha para o seu grupo de host. Se você não quiser cobrir domínios de falha em seu grupo, use uma contagem de domínios de falha de 1. 

Você também pode decidir usar zonas de disponibilidade e domínios de falhas. 

Neste exemplo, usaremos [acriação do grupo host az vm](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de host usando zonas de disponibilidade e domínios de falha. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Outros exemplos

Você também pode usar [a criação de um grupo de host az vm](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de host na zona de disponibilidade 1 (e sem domínios de falha).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
O grupo [de host az vm é criado](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de host usando apenas domínios de falha (para serem usados em regiões onde as zonas de disponibilidade não são suportadas). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Criar um host 

Agora vamos criar um host dedicado no grupo anfitrião. Além de um nome para o host, você é obrigado a fornecer o SKU para o host. O Host SKU captura a série VM suportada, bem como a geração de hardware para o seu host dedicado.  

Para obter mais informações sobre as SKUs host e preços, consulte [os preços do Host dedicado do Azure](https://aka.ms/ADHPricing).

Use [acriação de host az vm](/cli/azure/vm/host#az-vm-host-create) para criar um host. Se você definir uma contagem de domínio de falha para o seu grupo de host, será solicitado que você especifique o domínio de falha para o host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual 
Crie uma máquina virtual dentro de um host dedicado usando [az vm create](/cli/azure/vm#az-vm-create). Se você especificou uma zona de disponibilidade ao criar seu grupo de host, você será obrigado a usar a mesma região ao criar a máquina virtual.

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
> Se você criar uma máquina virtual em um host que não tenha recursos suficientes, a máquina virtual será criada em um estado FALHO. 


## <a name="check-the-status-of-the-host"></a>Verifique o status do host

Você pode verificar o estado de saúde do host e quantas máquinas virtuais você ainda pode implantar no host usando [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

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
Você pode exportar um modelo se agora quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros ou um ambiente de produção que corresponda a ele. O Gerenciador de Recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Crie ambientes inteiros fazendo referência a esse modelo JSON. Você pode criar modelos JSON manualmente ou exportar um ambiente existente para criar o modelo JSON para você. Use [a exportação do grupo AZ](/cli/azure/group#az-group-export) para exportar seu grupo de recursos.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Esse comando cria o arquivo `myDHResourceGroup.json` no diretório de trabalho atual. Quando você cria um ambiente com base neste modelo, será solicitado que você informe todos os nomes de recursos. Você pode popular esses nomes em seu arquivo de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando `az group export`. Edite seu modelo JSON para especificar os nomes dos recursos, ou crie um arquivo parameters.json que especifica os nomes dos recursos.
 
Para criar um ambiente a partir do seu modelo, use [a criação de implantação de grupo AZ](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Limpar 

Você está sendo cobrado por seus hosts dedicados mesmo quando nenhuma máquina virtual é implantada. Você deve excluir todos os hosts que você não está usando no momento para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais usando-o. Exclua as VMs usando [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Depois de excluir as VMs, você pode excluir o host usando [az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Depois de excluir todos os hosts, você pode excluir o grupo host usando [a exclusão do grupo host az vm](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Você também pode excluir todo o grupo de recursos em um único comando. Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de host.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte a visão geral dos [hosts dedicados.](dedicated-hosts.md)

- Você também pode criar hosts dedicados usando o [portal Azure](dedicated-hosts-portal.md).

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas quanto domínios de falha para máxima resiliência em uma região.