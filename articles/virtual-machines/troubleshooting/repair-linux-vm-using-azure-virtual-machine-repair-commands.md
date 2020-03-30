---
title: Reparar um VM Linux usando os comandos de reparo da Máquina Virtual Do Azure | Microsoft Docs
description: Este artigo detalha como usar os comandos de reparo da Máquina Virtual do Azure para conectar o disco a outra VM Linux para corrigir quaisquer erros e, em seguida, reconstruir sua VM original.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796211"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Linux usando os comandos de reparo da Máquina Virtual do Azure

Se a sua máquina virtual Linux (VM) no Azure encontrar um erro de inicialização ou disco, talvez seja necessário realizar a mitigação no próprio disco. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo detalha como usar os comandos de reparo da Máquina Virtual do Azure para conectar o disco a outra VM Linux para corrigir quaisquer erros e, em seguida, reconstruir sua VM original.

> [!IMPORTANT]
> Os scripts deste artigo só se aplicam às VMs que usam [o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Visão geral do processo de reparo

Agora você pode usar os comandos de reparo da Máquina Virtual do Azure para alterar o disco do Sistema Operacional para uma VM, e você não precisa mais excluir e recriar a VM.

Siga estas etapas para solucionar problemas no problema da VM:

1. Iniciar o Azure Cloud Shell
2. Executar az extensão add/update
3. Executar az vm reparo criar
4. Executar etapas de mitigação
5. Executar restauração de reparo az vm

Para obter documentação adicional e instruções, consulte [aaz vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo do processo de reparo

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele inclui ferramentas comuns do Azure pré-instaladas e configuradas para uso com sua conta.

   Para abrir o Cloud Shell, **selecione Tente no** canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell [https://shell.azure.com](https://shell.azure.com)em uma guia de navegador separada indo para .

   Selecione **Copiar** para copiar os blocos de código, em seguida, cole o código no Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.30 ou posterior. Execute ``az --version`` para encontrar a versão. Se você precisar instalar ou atualizar seu Cli Do Azure, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se esta é a primeira `az vm repair` vez que você usou os comandos, adicione a extensão CLI de reparo em vm.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se você já usou `az vm repair` os comandos anteriormente, aplique quaisquer atualizações à extensão vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Este comando criará uma cópia do disco do SISTEMA OPERACIONAL para a VM não funcional, criará uma VM de reparo e anexará o disco.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute todas as etapas de mitigação necessárias na VM de reparo criada e, em seguida, prossiga para a etapa 5.

5. Execute `az vm repair restore`. Este comando trocará o disco do Sistema Operacional reparado com o disco do sistema operacional original da VM.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e habilitar o diagnóstico de boot

O seguinte exemplo habilita a extensão de diagnóstico na VM chamada ``myVMDeployed`` no grupo de recursos chamado ``myResourceGroup``:

CLI do Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Próximas etapas

* Se você estiver tendo problemas de conexão com sua VM, consulte [Conexões RDP de solução de problemas a uma máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Para obter problemas com o acesso a aplicativos em execução em sua VM, consulte [Problemas de conectividade de aplicativos em máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
