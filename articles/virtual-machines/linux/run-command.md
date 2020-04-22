---
title: Execute scripts shell em um VM Linux no Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual do Azure Linux usando o recurso Executar comando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758610"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Execute scripts shell em sua VM Linux usando o Comando run

O recurso Executar comando usa o agente de máquina virtual (VM) para executar scripts shell dentro de uma VM Azure Linux. Você pode usar esses scripts para gerenciamento geral de máquinas ou aplicativos. Eles podem ajudá-lo a diagnosticar e remediar rapidamente os problemas de acesso e rede da VM e levar a VM de volta a um bom estado.

## <a name="benefits"></a>Benefícios

Você pode acessar suas máquinas virtuais de várias maneiras. O Comando de Execução pode executar scripts em suas máquinas virtuais remotamente usando o agente VM. Você usa o Comando run através do portal Azure, [Rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para VMs Linux.

Esse recurso é útil em todos os cenários onde você deseja executar um script dentro de uma máquina virtual. É uma das únicas maneiras de solucionar e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta por causa da configuração inadequada da rede ou do usuário administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições se aplicam quando você está usando o Comando de Execução:

* A saída é limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um script é de cerca de 20 segundos.
* Scripts executados por padrão como um usuário elevado no Linux.
* Você pode executar um script de cada vez.
* Scripts que solicitam informações (modo interativo) não têm suporte.
* Você não pode cancelar um script em execução.
* O tempo máximo que um script pode executar é de 90 minutos. Depois disso, o roteiro vai se essegundo inverter.
* A conectividade de saída da VM é necessária para retornar os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos finais, os scripts podem ser executados com sucesso, mas não retornar os resultados. Se você estiver bloqueando o tráfego na máquina virtual, você pode usar tags de `AzureCloud` [serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para endereços IP públicos do Azure usando a tag.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Linux. Você pode usar o comando **RunShellScript** para executar qualquer script personalizado que você quiser. Quando você estiver usando o Azure CLI ou PowerShell para executar `--command-id` um `-CommandId` comando, o valor que você fornece para o parâmetro deve ser um dos seguintes valores listados. Quando você especifica um valor que não é um comando disponível, você recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um script de shell Linux.|
|**ifconfig**| Obtém a configuração de todas as interfaces de rede.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir usa o comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script shell em um VM Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um `sudo -u` usuário diferente, digite para especificar uma conta de usuário.

## <a name="azure-portal"></a>Portal do Azure

Vá para uma VM no [portal Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Você vê uma lista dos comandos disponíveis para executar na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, você pode visualizar o script que está sendo executado expandindo **o script View**. **O RunShellScript** é diferente dos outros comandos, porque permite que você forneça seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não podem ser editados.

Depois de escolher o comando, **selecione Executar** para executar o script. Após o término do script, ele retorna a saída e quaisquer erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **ifconfig**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

O exemplo a seguir usa o [cmdlet Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell em uma VM Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja o local onde o cmdlet é executado.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou `Microsoft.Compute/locations/runCommands/read` mostrar os detalhes de um comando requer a permissão no nível de assinatura. A função [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporada e os níveis mais altos têm essa permissão.

Executar um comando `Microsoft.Compute/virtualMachines/runCommand/action` requer a permissão no nível de assinatura. A função [contribuinte da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais altos têm essa permissão.

Você pode usar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar uma [função personalizada](../../role-based-access-control/custom-roles.md) para usar o Comando de Execução.

## <a name="next-steps"></a>Próximas etapas

Para aprender sobre outras maneiras de executar scripts e comandos remotamente em sua VM, consulte [Executar scripts em sua VM Linux](run-scripts-in-vm.md).
