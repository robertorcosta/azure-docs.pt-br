---
title: Execute scripts PowerShell em um VM do Windows no Azure
description: Este tópico descreve como executar scripts PowerShell dentro de uma máquina virtual do Azure Windows usando o recurso Executar comando
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749231"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Execute scripts PowerShell em sua VM do Windows usando o Comando Executar

O recurso Executar comando usa o agente de máquina virtual (VM) para executar scripts PowerShell dentro de uma VM do Windows Azure. Você pode usar esses scripts para gerenciamento geral de máquinas ou aplicativos. Eles podem ajudá-lo a diagnosticar e remediar rapidamente os problemas de acesso e rede da VM e levar a VM de volta a um bom estado.

 

## <a name="benefits"></a>Benefícios

Você pode acessar suas máquinas virtuais de várias maneiras. O Comando de Execução pode executar scripts em suas máquinas virtuais remotamente usando o agente VM. Você usa o Comando executar através do portal Azure, [Rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para VMs Windows.

Esse recurso é útil em todos os cenários onde você deseja executar um script dentro de uma máquina virtual. É uma das únicas maneiras de solucionar e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta por causa da configuração inadequada da rede ou do usuário administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições se aplicam quando você está usando o Comando de Execução:

* A saída é limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um script é de cerca de 20 segundos.
* Scripts executados como Sistema no Windows.
* Um script de cada vez pode ser executado.
* Scripts que solicitam informações (modo interativo) não têm suporte.
* Você não pode cancelar um script em execução.
* O tempo máximo que um script pode executar é de 90 minutos. Depois disso, vai acabar.
* A conectividade de saída da VM é necessária para retornar os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos finais, os scripts podem ser executados com sucesso, mas não retornar os resultados. Se você estiver bloqueando o tráfego na máquina virtual, você pode usar tags de `AzureCloud` [serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para endereços IP públicos do Azure usando a tag.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Windows. Você pode usar o comando **RunPowerShellScript** para executar qualquer script personalizado que você quiser. Quando você estiver usando o Azure CLI ou PowerShell para executar `--command-id` um `-CommandId` comando, o valor que você fornece para o parâmetro deve ser um dos seguintes valores listados. Quando você especifica um valor que não é um comando disponível, você recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um script PowerShell.|
|**EnableRemotePS**|Configura o computador para habilitar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta do administrador local está desativada e, se assim for, habilita-a.|
|**IPConfig**| Mostra informações detalhadas para o endereço IP, a máscara de sub-rede e o gateway padrão para cada adaptador vinculado ao TCP/IP.|
|**RDPSettings**|Verifica as configurações do registro e de política de domínio. Sugere ações de diretiva se a máquina faz parte de um domínio ou modifica as configurações para valores padrão.|
|**ResetRDPCert**|Remove o certificado SSL vinculado ao ouvinte RDP e restaura a segurança do ouvinte RDP como padrão. Use este script se houver algum problema com o certificado.|
|**SetRDPPort**|Define o número de porta padrão ou especificado pelo usuário para conexões de desktop remota. Habilita regras de firewall para acesso de entrada à porta.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir usa o comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script shell em uma VM do Windows Azure.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portal do Azure

Vá para uma VM no [portal Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Você vê uma lista dos comandos disponíveis para executar na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, você pode visualizar o script que está sendo executado expandindo **o script View**. **RunPowerShellScript** é diferente dos outros comandos, porque permite que você forneça seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não podem ser editados.

Depois de escolher o comando, **selecione Executar** para executar o script. Após o término do script, ele retorna a saída e quaisquer erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **RDPSettings**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

O exemplo a seguir usa o [cmdlet Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell em uma VM Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja o local onde o cmdlet é executado.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou `Microsoft.Compute/locations/runCommands/read` mostrar os detalhes de um comando requer a permissão no nível de assinatura. A função [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporada e os níveis mais altos têm essa permissão.

Executar um comando `Microsoft.Compute/virtualMachines/runCommand/action` requer a permissão no nível de assinatura. A função [contribuinte da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais altos têm essa permissão.

Você pode usar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar uma [função personalizada](../../role-based-access-control/custom-roles.md) para usar o Comando de Execução.

## <a name="next-steps"></a>Próximas etapas

Para aprender sobre outras maneiras de executar scripts e comandos remotamente em sua VM, consulte [Executar scripts em sua VM do Windows](run-scripts-in-vm.md).
