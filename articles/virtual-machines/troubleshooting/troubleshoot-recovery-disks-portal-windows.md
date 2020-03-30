---
title: Usar uma VM Windows de solução de problemas no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquinas virtuais Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249991"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se ocorrer um erro de disco ou de inicialização na VM (máquina virtual) Windows no Azure, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar o disco rígido virtual a outra VM Windows para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
1. Crie um instantâneo para o disco do SISTEMA OPERACIONAL da VM.
1. Crie um disco rígido virtual a partir do snapshot.
1. Anexe e monte o disco rígido virtual em outra VM Windows para fins de solução de problemas.
1. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
1. Troque o disco do Sistema Operacional pela VM.

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

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
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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

## <a name="attach-the-disk-to-another-vm"></a>Conecte o disco a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode navegar e editar o conteúdo do disco. Este processo permite corrigir quaisquer erros de configuração ou revisar arquivos adicionais de registro de aplicativos ou de sistema. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **Discos,** selecione **Editar**e clique **em Adicionar disco de dados:**

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **de discos de dados,** selecione o disco do SISTEMA OPERACIONAL da VM que você identificou. Se você não ver o disco do SISTEMA OPERACIONAL, certifique-se de que a VM e o disco do sistema operacional estão na mesma região (localização). 
3. Selecione **Salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Monte o disco de dados conectado à VM

1. Abra uma conexão de desktop remota para a VM de solução de problemas. 
2. Na vm de solução de problemas, o Open **Server Manager**e, em seguida, selecione **Serviços de arquivo e armazenamento**. 

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é detectado e anexado automaticamente. Para ver uma lista dos discos conectados, selecione **Discos**. É possível selecionar o disco de dados para exibir informações de volume, incluindo a letra da unidade. O seguinte exemplo mostra o disco de dados anexado e usando **F:**:

    ![Disco anexado e informações de volume no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão RDP da VM, abra **Gerenciador do Servidor** e selecione **Serviços de Arquivo e Armazenamento**:

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **Discos** e, em seguida, o disco de dados. Clique com o botão direito do mouse no disco de dados e selecione **Colocar Offline**:

    ![Definir o disco de dados como offline no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal do Azure e clique em **Discos**. 
4. Selecione **Editar,** selecionar o disco do SISTEMA OPERACIONAL que você anexou e, em seguida, clicar **em Desapegar**:

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
Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).


