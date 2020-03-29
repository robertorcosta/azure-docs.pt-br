---
title: Usar uma VM de solução de problemas do Linux no Portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas da máquina virtual do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: e45de5c12f0d93645a0b1253acf8300527cafdbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374634"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM do Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se a VM (máquina virtual) do Linux tiver um erro de disco ou de inicialização, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM possa ser inicializada corretamente. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar o disco rígido virtual a outra VM do Linux para corrigir erros e recriar a VM original.

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
1. Tire uma foto para o disco do sistema operacional da VM.
1. Crie um disco rígido virtual a partir do snapshot.
1. Anexe e monte o disco rígido virtual em outra VM Windows para fins de solução de problemas.
1. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
1. Troque o disco do Sistema Operacional pela VM.

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine o diagnóstico de inicialização e a captura de tela da VM para determinar por que a VM não pode ser inicializada corretamente. Um exemplo comum seria uma entrada inválida em `/etc/fstab` ou a exclusão ou movimentação do disco rígido virtual subjacente.

Selecione a VM no portal e role para baixo até a seção **Suporte + Solução de problemas**. Clique em **Diagnóstico de inicialização** para exibir as mensagens do console transmitidas pela VM. Examine os logs do console para ver se você pode determinar por que a VM está com problemas. O seguinte exemplo mostra uma VM parada no modo de manutenção que exige interação manual:

![Exibindo os logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também é possível clicar em **Captura de tela** na parte superior do log do diagnóstico de inicialização para baixar uma captura de tela da VM.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do disco do sistema operacional
Um instantâneo é uma cópia completa, somente leitura de um disco rígido virtual (VHD). Recomendamos que você desligue a VM de forma limpa antes de tirar um instantâneo, para limpar todos os processos que estão em andamento. Para tirar um instantâneo de um disco do SISTEMA OPERACIONAL, siga estas etapas:

1. Vá para o [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e selecione a VM que tem problema.
1. No painel esquerdo, selecione **Discos**e selecione o nome do disco do sistema operacional.
    ![Imagem sobre o nome do disco do Sistema Operacional](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na **página Visão geral** do disco do SISTEMA OPERACIONAL e, em seguida, selecione **Criar instantâneo**.
1. Crie um instantâneo no mesmo local que o disco do sistema operacional.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo
Para criar um disco a partir do snapshot, siga estas etapas:

1. Selecione **Cloud Shell** no portal Azure.

    ![Imagem sobre Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Execute os seguintes comandos PowerShell para criar um disco gerenciado a partir do snapshot. Você deve substituir esses nomes de amostra pelos nomes apropriados.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se os comandos forem executados com sucesso, você verá o novo disco no grupo de recursos fornecido.

## <a name="attach-disk-to-another-vm"></a>Anexar disco a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode navegar e editar o conteúdo do disco. Este processo permite corrigir quaisquer erros de configuração ou revisar arquivos adicionais de registro de aplicativos ou de sistema. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **Discos,** selecione **Editar**e clique **em Adicionar disco de dados:**

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **de discos de dados,** selecione o disco do SISTEMA OPERACIONAL da VM que você identificou. Se você não ver o disco do SISTEMA OPERACIONAL, certifique-se de que a VM e o disco do sistema operacional estão na mesma região (localização). 
3. Selecione **Salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir fornecem detalhes das etapas necessárias em uma VM do Ubuntu. Se você estiver usando outra distribuição Linux, como Red Hat Enterprise Linux ou SUSE, as localizações do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação da distribuição específica para obter as alterações apropriadas aos comandos.

1. SSH para a VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à VM de solução de problemas, provavelmente ele estará conectado a `/dev/sdc`. Use `dmseg` para listar os discos anexados:

    ```bash
    dmesg | grep SCSI
    ```
    A saída deverá ser semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do sistema operacional está em `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb`. Se você tiver vários discos de dados, eles deverão estar em `/dev/sdd`, `/dev/sde` e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O seguinte exemplo cria um diretório chamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se você tiver várias partições no disco rígido virtual existente, monte a partição necessária. O seguinte exemplo monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A prática recomendada é montar os discos de dados em VMs no Azure usando o UUID (identificador universal exclusivo) do disco rígido virtual. Para este cenário de solução de problemas breve, não é necessário montar o disco rígido virtual usando o UUID. No entanto, no uso normal, editar `/etc/fstab` para montar os discos rígidos virtuais usando o nome do dispositivo em vez do UUID poderá causar falha de inicialização da VM.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH da VM de solução de problemas, desmonte o disco rígido virtual existente. Altere do diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal e clique em **Discos**. Selecione o disco rígido virtual existente e clique em **Desanexar**:

    ![Desanexar um disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexo o disco de dados corretamente antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Troque o disco do Sistema Operacional pela VM

O portal Azure agora suporta alterar o disco do Sistema Operacional da VM. Para fazer isso, siga estas etapas:

1. Vá para o [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e selecione a VM que tem problema.
1. No painel esquerdo, selecione **Discos**e selecione **Swap OS Disk**.
        ![A imagem sobre o disco Swap OS no portal Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que você reparou e digite o nome da VM para confirmar a alteração. Se você não ver o disco na lista, espere 10 ~ 15 minutos depois de separar o disco da VM de solução de problemas. Certifique-se também de que o disco está no mesmo local que a VM.
1. Selecione OK.

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à VM, consulte [Solucionar problemas de conexões SSH com uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para ver problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
