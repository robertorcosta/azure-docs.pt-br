---
title: Reparar uma VM do Windows usando os comandos de reparo da Máquina Virtual do Azure | Microsoft Docs
description: Este artigo detalha como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, reconstruir sua VM original.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 6520d508a025aeeecf0c1890224a0691eae09f74
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774419"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Windows usando os comandos de reparo da Máquina Virtual do Azure

Se sua máquina virtual (VM) do Windows no Azure encontrar um erro de inicialização ou disco, talvez seja necessário executar a atenuação no próprio disco. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo detalha como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, reconstruir sua VM original.

> [!IMPORTANT]
> Os scripts neste artigo se aplicam somente às VMs que usam [Disco Gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Visão geral do processo de reparo

Agora você pode usar os comandos de reparo da VM do Azure para alterar o disco do SO de uma VM e não é mais necessário excluir e recriar a VM.

Siga estas etapas para solucionar o problema da VM:

1. Iniciar o Azure Cloud Shell
2. Execute az extension add/update.
3. Execute az vm repair create.
4. Execute az vm repair run.
5. Execute az vm repair restore.

Para obter a documentação e as instruções adicionais, confira [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo de processo de reparo

> [!NOTE]
> * A conectividade de saída da VM (porta 443) é necessária para que o script seja executado.
> * Somente um script pode ser executado de cada vez.
> * Não é possível cancelar um script em execução.
> * O tempo máximo que um script pode ser executado é 90 minutos. Após esse tempo, ele atingirá o tempo limite.

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

   Para abrir o Cloud Shell, selecione **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador acessando [https://shell.azure.com](https://shell.azure.com).

   Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.30 ou posterior. Execute ``az --version`` para encontrar a versão. Se você precisar instalar ou atualizar sua CLI do Azure, confira como [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se esta é a primeira vez que você usa os comandos `az vm repair`, adicione a extensão da CLI vm-repair.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se você já usou os comandos `az vm repair`, aplique as atualizações na extensão vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Esse comando criará uma cópia do disco do sistema operacional para a VM não funcional, criará uma VM de reparo em um novo grupo de recursos e anexará a cópia de disco do sistema operacional.  A VM de reparo terá o mesmo tamanho e região que a VM não funcional especificada.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute `az vm repair run`. Este comando executará o script de reparo especificado no disco anexado por meio da VM de reparo.  Se o guia de solução de problemas que você está usando especificou uma ID de execução, use-a aqui. Caso contrário, você pode usar `az vm repair list-scripts` para ver os scripts de reparo disponíveis.

   ```azurecli-interactive

   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id win-hello-world --verbose
   ```

5. Execute `az vm repair restore`. Este comando irá trocar o disco do SO reparado pelo disco do SO original da VM.

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

* Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection) (Solucionar conexões RDP a uma VM do Azure).
* Para problemas com o acesso a aplicativos em execução na sua VM, consulte [Solucionar problemas de conectividade de aplicativos em máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
