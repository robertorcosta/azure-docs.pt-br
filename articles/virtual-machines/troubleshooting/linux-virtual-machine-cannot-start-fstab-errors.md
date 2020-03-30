---
title: Solucionando problemas de início do Linux VM devido a erros de fstab | Microsoft Docs
description: Explica por que o Linux VM não pode começar e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351144"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Solucionando problemas de início do Linux VM devido a erros de fstab

Você não pode se conectar a uma VM (Azure Linux Virtual Machine) usando uma conexão Secure Shell (SSH). Quando você [executa](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) o recurso Boot Diagnostics no [portal Azure,](https://portal.azure.com/)você verá entradas de log que se assemelham aos seguintes exemplos:

## <a name="examples"></a>Exemplos

A seguir, exemplos de possíveis erros.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exemplo 1: Um disco é montado pelo ID SCSI em vez do identificador universalmente único (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exemplo 2: Falta um dispositivo não conectado no CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Exemplo 3: Uma VM não pode ser iniciada por causa de uma configuração errada ou porque o disco não está mais conectado

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Exemplo 4: Uma entrada de log serial mostra um UUID incorreto

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Esse problema pode ocorrer se a sintaxe da tabela de sistemas de arquivos (fstab) estiver incorreta ou se um disco de dados necessário que for mapeado para uma entrada no arquivo "/etc/fstab" não estiver anexado à VM.

## <a name="resolution"></a>Resolução

Para resolver esse problema, inicie a VM no modo de emergência usando o console serial para Máquinas Virtuais Azure. Em seguida, use a ferramenta para reparar o sistema de arquivos. Se o console serial não estiver habilitado em sua VM, vá para a [seção Reparar a vm offline.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Usar o console serial

### <a name="using-single-user-mode"></a>Usando o modo de usuário único

1. Conecte-se [ao console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Use o console serial para tomar o modo [único do usuário modo de usuário único](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. Uma vez que o vm tenha inicializado no modo de usuário único. Use seu editor de texto favorito para abrir o arquivo fstab. 

   ```
   # nano /etc/fstab
   ```

4. Revise os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando do homem fstab. Para solucionar uma falha inicial, revise cada linha para ter certeza de que ela está correta tanto na estrutura quanto no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por guias ou espaços. Linhas em branco são ignoradas. Linhas que têm um sinal numéu (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. Recomendamos que você comente linhas de fstab que você não tem certeza em vez de remover as linhas.
   > * Para que a VM se recupere e inicie, as partições do sistema de arquivos devem ser as únicas partições necessárias. O VM pode sofrer erros de aplicativo sobre partições comentou adicionais. No entanto, o VM deve começar sem as partições adicionais. Mais tarde, você pode descomentar qualquer linha comentada.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, comande o comando do homem blkid.
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo se o sistema de arquivos estiver corrompido ou o sistema de arquivos não existir na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para permitir que a inicialização continue após erros ocorrerem em partições que não são necessárias para que a VM seja iniciada.

5. Alterar ou comentar quaisquer linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM comece corretamente.

6. Salve as alterações no arquivo fstab.

7. Reinicie o vm usando o comando abaixo.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Você também pode usar o comando "ctrl+x", que também reiniciaria o vm.


8. Se o comentário ou correção das entradas foi bem sucedido, o sistema deve chegar a um prompt de bash no portal. Verifique se você pode se conectar à VM.

### <a name="using-root-password"></a>Usando senha raiz

1. Conecte-se [ao console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Faça login no sistema usando um usuário e senha locais.

   > [!Note]
   > Você não pode usar uma chave SSH para entrar no sistema no console serial.

3. Procure o erro que indica que o disco não foi montado. No exemplo a seguir, o sistema estava tentando anexar um disco que não estava mais presente:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Conecte-se à VM usando a senha raiz (VMs baseadas em Red Hat).

5. Use seu editor de texto favorito para abrir o arquivo fstab. Depois que o disco for montado, execute o seguinte comando para Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Revise os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando do homem fstab. Para solucionar uma falha inicial, revise cada linha para ter certeza de que ela está correta tanto na estrutura quanto no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por guias ou espaços. Linhas em branco são ignoradas. Linhas que têm um sinal numéu (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. Recomendamos que você comente linhas de fstab que você não tem certeza em vez de remover as linhas.
   > * Para que a VM se recupere e inicie, as partições do sistema de arquivos devem ser as únicas partições necessárias. O VM pode sofrer erros de aplicativo sobre partições comentou adicionais. No entanto, o VM deve começar sem as partições adicionais. Mais tarde, você pode descomentar qualquer linha comentada.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, comande o comando do homem blkid.
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo se o sistema de arquivos estiver corrompido ou o sistema de arquivos não existir na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para permitir que a inicialização continue após erros ocorrerem em partições que não são necessárias para que a VM seja iniciada.

7. Alterar ou comentar quaisquer linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM comece corretamente.

8. Salve as alterações no arquivo fstab.

9. Reinicie a máquina virtual.

10. Se o comentário ou correção das entradas foi bem sucedido, o sistema deve chegar a um prompt de bash no portal. Verifique se você pode se conectar à VM.

11. Verifique os pontos de montagem quando testar qualquer mudança de fstab executando a montagem – um comando. Se não houver erros, seus pontos de montagem devem ser bons.

## <a name="repair-the-vm-offline"></a>Repare a VM offline

1. Conecte o disco do sistema da VM como um disco de dados a uma VM de recuperação (qualquer VM Linux em funcionamento). Para fazer isso, você pode usar [comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) ou pode automatizar a configuração da VM de recuperação usando os [comandos de reparo](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)da VM .

2. Depois de montar o disco do sistema como um disco de dados na VM de recuperação, faça backup do arquivo fstab antes de fazer alterações e siga os próximos passos para corrigir o arquivo fstab.

3.    Procure o erro que indica que o disco não foi montado. No exemplo a seguir, o sistema estava tentando anexar um disco que não estava mais presente:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Conecte-se à VM usando a senha raiz (VMs baseadas em Red Hat).

5. Use seu editor de texto favorito para abrir o arquivo fstab. Depois que o disco for montado, execute o seguinte comando para Nano. Certifique-se de que você está trabalhando no arquivo fstab que está localizado no disco montado e não o arquivo fstab que está na VM de resgate.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Revise os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando do homem fstab. Para solucionar uma falha inicial, revise cada linha para ter certeza de que ela está correta tanto na estrutura quanto no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por guias ou espaços. Linhas em branco são ignoradas. Linhas que têm um sinal numéu (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. Recomendamos que você comente linhas de fstab que você não tem certeza em vez de remover as linhas.
   > * Para que a VM se recupere e inicie, as partições do sistema de arquivos devem ser as únicas partições necessárias. O VM pode sofrer erros de aplicativo sobre partições comentou adicionais. No entanto, o VM deve começar sem as partições adicionais. Mais tarde, você pode descomentar qualquer linha comentada.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, comande o comando do homem blkid. Observe que o disco que você deseja recuperar está agora montado em uma nova VM. Embora os UUIDs devam ser consistentes, os IDs de partição do dispositivo (por exemplo, "/dev/sda1") são diferentes nesta VM. As partições do sistema de arquivos da VM falha original que estão localizadas em um VHD não-sistema não estão disponíveis para a VM de recuperação [usando comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo se o sistema de arquivos estiver corrompido ou o sistema de arquivos não existir na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para permitir que a inicialização continue após erros ocorrerem em partições que não são necessárias para que a VM seja iniciada.

7. Alterar ou comentar quaisquer linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM comece corretamente.

8. Salve as alterações no arquivo fstab.

9. Reinicie a máquina virtual ou reconstrua a VM original.

10. Se o comentário ou correção das entradas foi bem sucedido, o sistema deve chegar a um prompt de bash no portal. Verifique se você pode se conectar à VM.

11. Verifique os pontos de montagem quando testar qualquer mudança de fstab executando a montagem – um comando. Se não houver erros, seus pontos de montagem devem ser bons.

12. Desmontar e desprender o disco rígido virtual original e, em seguida, criar uma VM a partir do disco do sistema original. Para fazer isso, você pode usar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou os [comandos de reparo](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) da VM se você os usou para criar a VM de recuperação.

13. Depois de criar a VM novamente e você pode se conectar a ela através do SSH, tome as seguintes ações:
    * Reveja qualquer uma das linhas de fstab que foram alteradas ou comentadas durante a recuperação.
    * Certifique-se de que está usando UUID e a opção nofail apropriadamente.
    * Teste qualquer alteração de fstab antes de reiniciar a VM. Para fazer isso, use o seguinte comando:``$ sudo mount -a``
    * Crie uma cópia adicional do arquivo fstab corrigido para uso em cenários futuros de recuperação.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma VM Linux anexando o disco do SO a uma VM de recuperação com a CLI 2.0 do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Solucionar problemas de uma VM do Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

