---
title: Proteção de instância satiscadas para a escala de máquina virtual do Azure
description: Saiba como proteger as instâncias de conjunto de configuração de máquinas virtuais do Azure contra operações dimensionadas e dimensionadas.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254112"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Proteção de instância satiscadas para a escala de máquina virtual do Azure

Os conjuntos de escala de máquinavirtual do Azure permitem uma melhor elasticidade para suas cargas de trabalho através do [Autoscale,](virtual-machine-scale-sets-autoscale-overview.md)para que você possa configurar quando sua infra-estrutura for dimensionada e quando ela for dimensionada. Os conjuntos de escala também permitem gerenciar, configurar e atualizar centralmente um grande número de VMs através de diferentes configurações de diretiva de [upgrade.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Você pode configurar uma atualização no modelo de conjunto de escalas e a nova configuração é aplicada automaticamente a todas as instâncias de conjunto de escala, se você definir a diretiva de upgrade como Automática ou Rolando.

À medida que seu aplicativo processa o tráfego, pode haver situações em que você deseja que instâncias específicas sejam tratadas de forma diferente do resto da instância de conjunto de escalas. Por exemplo, certas instâncias no conjunto de escala saem realizando operações de longa duração, e você não quer que essas instâncias sejam dimensionadas até que as operações sejam concluídas. Você também pode ter especializado algumas instâncias na escala definida para executar tarefas adicionais ou diferentes do que os outros membros do conjunto de escala. Você exige que essas VMs 'especiais' não sejam modificadas com as outras instâncias no conjunto de escalas. A proteção por instâncias fornece os controles adicionais para habilitar esses e outros cenários para o seu aplicativo.

Este artigo descreve como você pode aplicar e usar os diferentes recursos de proteção de instâncias com instâncias definidas em escala.

## <a name="types-of-instance-protection"></a>Tipos de proteção de instâncias
Os conjuntos de escala fornecem dois tipos de recursos de proteção de instâncias:

-   **Proteger contra escala-in**
    - Ativado através **da propriedade protectFromScaleIn** na instância de conjunto de escalas
    - Protege a instância da escala iniciada em autoescala
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instâncias) não são **bloqueadas**
    - As operações iniciadas no conjunto de escalas (upgrade, reimage, deallocate, etc.) não estão **bloqueadas**

-   **Proteger contra ações de conjunto de escala**
    - Ativado por meio da propriedade **protectFromScaleSetActions** na instância de conjunto de escalas
    - Protege a instância da escala iniciada em autoescala
    - Protege a instância das operações iniciadas no conjunto de escala (como upgrade, reimage, deallocate, etc.)
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instâncias) não são **bloqueadas**
    - A exclusão do conjunto de escala completa não está **bloqueada**

## <a name="protect-from-scale-in"></a>Proteger contra escala-in
A proteção de instâncias pode ser aplicada a instâncias de conjunto de escala após a criação das instâncias. A proteção é aplicada e modificada apenas no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [escalas](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Existem várias maneiras de aplicar proteção de escala em instâncias definidas em sua escala, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Você pode aplicar proteção de escala através do portal Azure a uma instância no conjunto de escalas. Você não pode ajustar mais de uma instância de cada vez. Repita as etapas para cada instância que deseja proteger.
 
1. Vá para um conjunto de escala de máquina virtual existente.
1. Selecione **Instâncias** no menu à esquerda, em **Configurações**.
1. Selecione o nome da instância que deseja proteger.
1. Selecione a guia **Política de proteção.**
1. Na lâmina **Política de proteção,** selecione a **opção Proteger da opção de entrada de escala.**
1. Selecione **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica proteção de escala a uma instância no conjunto de escalas.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>A proteção por instância só é suportada com a versão API 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell

Use o [cmdlet Update-AzVmsVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção de escala à instância de conjunto de escalas.

O exemplo a seguir aplica proteção de escala a uma instância no conjunto de escala sustais ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Use [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar proteção de escala à instância de conjunto de escalas.

O exemplo a seguir aplica proteção de escala a uma instância no conjunto de escala sustais ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteger contra ações de conjunto de escala
A proteção de instâncias pode ser aplicada a instâncias de conjunto de escala após a criação das instâncias. A proteção é aplicada e modificada apenas no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [escalas](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Proteger uma instância de ações de conjunto de escala também protege a instância da escala iniciada em escala automática.

Existem várias maneiras de aplicar a proteção de ações de conjunto de escala em suas instâncias de conjunto de escala, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Você pode aplicar a proteção contra ações de conjunto de escala através do portal Azure a uma instância no conjunto de escalas. Você não pode ajustar mais de uma instância de cada vez. Repita as etapas para cada instância que deseja proteger.
 
1. Vá para um conjunto de escala de máquina virtual existente.
1. Selecione **Instâncias** no menu à esquerda, em **Configurações**.
1. Selecione o nome da instância que deseja proteger.
1. Selecione a guia **Política de proteção.**
1. Na lâmina **Política de proteção,** selecione a opção Proteger contra a opção **ações de conjunto de escala.**
1. Selecione **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica proteção contra ações de conjunto de escala a uma instância no conjunto de escalas.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>A proteção por instância só é suportada com a versão aPI 2019-03-01 ou superior.</br>
Proteger uma instância de ações de conjunto de escala também protege a instância da escala iniciada em escala automática. Não é possível especificar "protectFromScaleIn": falso ao definir "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Use o [cmdlet Update-AzVmsVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção contra ações de conjunto de escala à instância de conjunto de escalas.

O exemplo a seguir aplica proteção contra ações de conjunto de escala a uma instância no conjunto de escalas com id de ocorrência 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Use [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar proteção contra ações de conjunto de escala satisseu exemplo de conjunto de escalas.

O exemplo a seguir aplica proteção contra ações de conjunto de escala a uma instância no conjunto de escalas com id de ocorrência 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Solução de problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Sem proteçãoPolítica no modelo de conjunto de escala
A proteção de instâncias só é aplicável em instâncias definidas em escala e não no modelo de conjunto de escala.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Sem proteçãoPolítica no modelo de instância de conjunto de escala
Por padrão, a política de proteção não é aplicada a uma instância quando ela é criada.

Você pode aplicar proteção de instâncias a instâncias de dimensionar instâncias definidas após a criação das instâncias.

### <a name="not-able-to-apply-instance-protection"></a>Não é capaz de aplicar proteção de instâncias
A proteção por instância só é suportada com a versão aPI 2019-03-01 ou superior. Verifique a versão da API que está sendo usada e atualize conforme necessário. Você também pode precisar atualizar seu PowerShell ou CLI para a versão mais recente.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
