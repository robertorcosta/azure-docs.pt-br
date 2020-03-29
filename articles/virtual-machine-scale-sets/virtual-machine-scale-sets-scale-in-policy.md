---
title: Use políticas de scale-in personalizadas com conjuntos de escala de máquinas virtuais do Azure
description: Aprenda a usar políticas de scale-in personalizadas com conjuntos de escala de máquinavirtual do Azure que usam configuração de escala automática para gerenciar a contagem de instâncias
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919831"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Use políticas de scale-in personalizadas com conjuntos de escala de máquinas virtuais do Azure

Uma implantação de conjunto de escala de máquina virtual pode ser dimensionada ou dimensionada com base em uma série de métricas, incluindo métricas personalizadas definidas pela plataforma e pelo usuário. Enquanto uma escala cria novas máquinas virtuais baseadas no modelo de conjunto de escalas, uma escala afeta a execução de máquinas virtuais que podem ter configurações e/ou funções diferentes à medida que a carga de trabalho definida em escala evolui. 

O recurso de diretiva de entrada em escala fornece aos usuários uma maneira de configurar a ordem na qual as máquinas virtuais são dimensionadas, por meio de três configurações de escala: 

1. Padrão
2. Mais novoVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Política de escala padrão

Por padrão, o conjunto de escala de máquina virtual aplica essa diretiva para determinar em quais instâncias serão dimensionadas. Com a *diretiva Padrão,* as VMs são selecionadas para a entrada em escala na seguinte ordem:

1. Balance máquinas virtuais em zonas de disponibilidade (se o conjunto de escalafore implantado na configuração zonal)
2. Equilibrar máquinas virtuais em domínios de falhas (melhor esforço)
3. Exclua a máquina virtual com a iD de instância mais alta

Os usuários não precisam especificar uma diretiva de escala se quiserem apenas que o pedido padrão seja seguido.

Observe que o balanceamento entre zonas de disponibilidade ou domínios de falhas não move instâncias através de zonas de disponibilidade ou domínios de falhas. O equilíbrio é alcançado através da exclusão de máquinas virtuais das zonas de disponibilidade desequilibradas ou domínios de falhas até que a distribuição de máquinas virtuais se torne equilibrada.

### <a name="newestvm-scale-in-policy"></a>Política de scale-in mais recenteVM

Essa diretiva excluirá a máquina virtual criada mais recente no conjunto de escalas, depois de equilibrar VMs entre zonas de disponibilidade (para implantações zonais). A ativação desta diretiva requer uma alteração de configuração no modelo de conjunto de escala de máquina virtual.

### <a name="oldestvm-scale-in-policy"></a>Política de scale-in mais antigaVM

Essa diretiva excluirá a máquina virtual criada mais antiga no conjunto de escalas, depois de equilibrar VMs entre zonas de disponibilidade (para implantações zonais). A ativação desta diretiva requer uma alteração de configuração no modelo de conjunto de escala de máquina virtual.

## <a name="enabling-scale-in-policy"></a>Habilitando a política de dimensionamento

Uma política de escala é definida no modelo de conjunto de escala de máquina virtual. Como observado nas seções acima, uma definição de diretiva de escala é necessária ao usar as políticas 'NewestVM' e 'OldestVM'. O conjunto de escalade máquina virtual usará automaticamente a diretiva de escala 'Padrão' se não houver definição de diretiva de escala encontrada no modelo de conjunto de escalas. 

Uma política de dimensionar pode ser definida no modelo de conjunto de escala de máquina virtual das seguintes maneiras:

### <a name="azure-portal"></a>Portal do Azure
 
As etapas a seguir definem a diretiva scale-in ao criar um novo conjunto de escalas. 
 
1. Vá para **conjuntos de escala de máquinas virtuais**.
1. Selecione **+ Adicione** para criar um novo conjunto de escalas.
1. Vá para a guia **Scaling.** 
1. Localize a seção **de diretiva Scale-in.**
1. Selecione uma diretiva de escala a partir do drop-down.
1. Quando terminar de criar o novo conjunto de escalas, selecione **'Revisar + criar'.**

### <a name="using-api"></a>Usando a API

Execute um PUT no conjunto de escala sinuosa da máquina virtual usando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Crie um grupo de recursos e crie um novo conjunto de escalas com a diretiva de dimensionamento definida como *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir adiciona uma diretiva de escala ao criar um novo conjunto de escalas. Primeiro crie um grupo de recursos e crie um novo conjunto de escalas com a diretiva de dimensionamento como *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando modelo

Em seu modelo, em "propriedades", adicione o seguinte:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Os blocos acima especificam que o conjunto de escalas da máquina virtual excluirá a VM mais antiga em um conjunto de escala balanceado de zona, quando uma escala é acionada (através de Autoscale ou exclusão manual).

Quando um conjunto de escala de máquina virtual não estiver equilibrado na zona, o conjunto de escalas primeiro excluirá VMs através das zonas desequilibradas. Dentro das zonas desequilibradas, o conjunto de escalas usará a diretiva de escala especificada acima para determinar em qual VM escalar. Neste caso, dentro de uma zona desequilibrada, o conjunto de escala selecionará a VM mais antiga nessa região a ser excluída.

Para o conjunto de escala de máquina virtual não zonal, a diretiva seleciona a VM mais antiga em toda a escala definida para exclusão.

O mesmo processo se aplica ao usar 'NewestVM' na diretiva de escala acima.

## <a name="modifying-scale-in-policies"></a>Modificando políticas de escala

Modificar a diretiva de escala segue o mesmo processo que aplicar a diretiva de escala. Por exemplo, se no exemplo acima, você quiser alterar a política de 'OldestVM' para 'NewestVM', você pode fazê-lo por:

### <a name="azure-portal"></a>Portal do Azure

Você pode modificar a diretiva de escala de uma escala existente definida através do portal Azure. 
 
1. Em um conjunto de escala de máquina virtual existente, selecione **Dimensionamento** no menu à esquerda.
1. Selecione a guia **Diretiva de escala.**
1. Selecione uma diretiva de escala a partir do drop-down.
1. Quando terminar, selecione **Salvar**. 

### <a name="using-api"></a>Usando a API

Execute um PUT no conjunto de escala sinuosa da máquina virtual usando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Atualize a diretiva de escala de um conjunto de escalaexistente:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

A seguir, um exemplo para atualizar a política de escala de um conjunto de escala existente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando modelo

Em seu modelo, em "propriedades", modifique o modelo abaixo e reimplante: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

O mesmo processo será aplicado se você decidir mudar 'NewestVM' para 'Default' ou 'OldestVM'

## <a name="instance-protection-and-scale-in-policy"></a>Proteção de instâncias e política de scale-in

Os conjuntos de escala de máquinas virtuais fornecem dois tipos de [proteção](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)de instâncias:

1. Proteger contra escala-in
2. Proteger contra ações dimensionadas

Uma máquina virtual protegida não é excluída por meio de uma ação de escala, independentemente da política de scale-in aplicada. Por exemplo, se VM_0 (VM mais antigo no conjunto de escala) estiver protegido contra escala-in, e o conjunto de escala tiver a política de escala 'OldestVM' habilitada, VM_0 não serão consideradas por serem dimensionadas, embora seja a VM mais antiga do conjunto de escalas. 

Uma máquina virtual protegida pode ser excluída manualmente pelo usuário a qualquer momento, independentemente da política de escala ativada no conjunto de escalas. 

## <a name="usage-examples"></a>Exemplos de uso 

Os exemplos abaixo demonstram como um conjunto de escala de máquina virtual selecionará VMs a serem excluídos quando um evento de escala for acionado. As máquinas virtuais com as IDs de instância mais alta são assumidas como as mais novas VMs no conjunto de escalas e as VMs com as menores IDs de instância são assumidas como as VMs mais antigas no conjunto de escalas. 

### <a name="oldestvm-scale-in-policy"></a>Política de scale-in mais antigaVM

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona3  | Seleção de escala                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Scale-in              | 3, 4, 5, 10            | ***2,*** 6, 9, 11      | 1, 7, 8                | Escolha entre a Zona 1 e a 2, mesmo que a Zona 3 tenha a VM mais antiga. Exclua o VM2 da Zona 2, pois é a VM mais antiga dessa região.   |
| Scale-in              | ***3,*** 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Escolha a Zona 1, embora a Zona 3 tenha a VM mais antiga. Exclua o VM3 da Zona 1, pois é a VM mais antiga dessa região.                  |
| Scale-in              | 4, 5, 10               | 6, 9, 11               | ***1,*** 7, 8          | As zonas estão equilibradas. Exclua o VM1 na Zona 3, pois é a VM mais antiga do conjunto de escalas.                                               |
| Scale-in              | ***4,*** 5, 10         | 6, 9, 11               | 7, 8                   | Escolha entre a Zona 1 e a Zona 2. Exclua o VM4 na Zona 1, pois é a VM mais antiga entre as duas Regiões.                              |
| Scale-in              | 5, 10                  | ***6,*** 9, 11         | 7, 8                   | Escolha a Zona 2, embora a Zona 1 tenha a VM mais antiga. Exclua o VM6 na Zona 1, pois é a VM mais antiga dessa região.                    |
| Scale-in              | ***5,*** 10            | 9, 11                  | 7, 8                   | As zonas estão equilibradas. Exclua o VM5 na Zona 1, pois é a VM mais antiga do conjunto de escalas.                                                |

Para conjuntos de escala de máquinavirtual não zonal, a diretiva seleciona a VM mais antiga em toda a escala definida para exclusão. Qualquer VM "protegido" será pulado para exclusão.

### <a name="newestvm-scale-in-policy"></a>Política de scale-in mais recenteVM

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona3  | Seleção de escala                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Scale-in              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Escolha entre a Zona 1 e a 2. Exclua o VM11 da Zona 2, pois é a mais nova VM entre as duas regiões.                                |
| Scale-in              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Escolha a Zona 1, pois tem mais VMs do que as outras duas zonas. Exclua o VM10 da Zona 1, pois é a mais nova VM nessa Região.          |
| Scale-in              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | As zonas estão equilibradas. Exclua o VM9 na Zona 2, pois é a mais nova VM no conjunto de escalas.                                                |
| Scale-in              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Escolha entre a Zona 1 e a Zona 3. Exclua o VM8 na Zona 3, pois é a mais nova VM nessa Zona.                                      |
| Scale-in              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Escolha a Zona 1, embora a Zona 3 tenha a mais nova VM. Exclua o VM5 na Zona 1, pois é a mais nova VM nessa Zona.                    |
| Scale-in              | 3, 4                   | 2, 6                   | 1, ***7***             | As zonas estão equilibradas. Exclua o VM7 na Zona 3, pois é a mais nova VM no conjunto de escalas.                                                |

Para conjuntos de escala de máquinavirtual não zonal, a diretiva seleciona a VM mais recente em toda a escala definida para exclusão. Qualquer VM "protegido" será pulado para exclusão. 

## <a name="troubleshoot"></a>Solução de problemas

1. Falha ao habilitar escalaInPolicy Se você receber um erro 'BadRequest' com uma mensagem de erro informando "Não foi possível encontrar 'scaleInPolicy' do membro no objeto de 'propriedades' do tipo", verifique a versão da API usada para o conjunto de escala da máquina virtual. Para este recurso é necessária a versão aPI 2019-03-01 ou superior.

2. Seleção errada de VMs para scale-in Consulte os exemplos acima. Se o conjunto de escala da máquina virtual for uma implantação zonal, a diretiva de escala é aplicada primeiro às Regiões desequilibradas e, em seguida, em toda a escala definida uma vez que esteja equilibrada na região. Se a ordem de dimensionar não for consistente com os exemplos acima, levante uma consulta com a equipe de conjunto de escala de máquina virtual para solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais