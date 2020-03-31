---
title: Solucionar problemas de início do Linux VM devido a erros no sistema de arquivos | Microsoft Docs
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842394"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Solucionando problemas de início do Linux VM devido a erros no sistema de arquivos

Você não pode se conectar a uma máquina virtual Do Zure Linux (VM) usando o Secure Shell (SSH). Quando você executa o recurso Boot Diagnostics no [portal Azure,](https://portal.azure.com/)você verá entradas de log que se assemelham aos exemplos a seguir.

## <a name="examples"></a>Exemplos

A seguir, exemplos de possíveis erros.

### <a name="example-1"></a>Exemplo 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Exemplo 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Exemplo 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Exemplo 4 

Este exemplo é causado por um fsck limpo. Neste caso, há também discos de dados adicionais anexados à VM (/dev/sdc1 e /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Esse problema pode ocorrer se o sistema de arquivos não tiver sido desligado de forma limpa ou problemas relacionados ao armazenamento. Os problemas incluem erros de hardware ou software, problemas com drivers ou programas, erros de gravação, etc. É sempre importante ter um backup de dados críticos. As ferramentas que descrevem neste artigo podem ajudar a recuperar sistemas de arquivos, mas é perda de dados ainda pode ocorrer.

O Linux tem vários verificadores de sistema de arquivos disponíveis. As distribuições mais comuns no Azure são: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)e [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Resolução

Para resolver esse problema, inicie a VM no modo de emergência usando o [console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) e use essa ferramenta para reparar o sistema de arquivos. Se o console serial não estiver habilitado em sua VM ou não funcionar, consulte a [seção Reparador a VM offline](#repair-the-vm-offline) deste artigo.

## <a name="use-the-serial-console"></a>Usar o console serial

1. Conecte-se ao console serial.

   > [!Note]
   > Para obter mais informações sobre como usar o console serial para Linux, consulte:
   > * [Use o console serial para acessar grub e modo de usuário único](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Use console serial para chamadas SysRq e NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Selecione o botão Ícone de poder e selecione Reiniciar VM. (Se o console serial não estiver habilitado ou não conectado com sucesso, você não verá o botão.)

   ![IMAGE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Inicie o VM para o modo de emergência.

4. Digite a senha de sua conta raiz para entrar no modo de emergência.

5. Use xfs_repair com a opção -n para detectar os erros no sistema de arquivos. Na amostra a seguir, assumimos que a partição do sistema é /dev/sda1. Substitua-o pelo valor apropriado para sua VM:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Execute o seguinte comando para reparar o sistema de arquivos:

   ```
   xfs_repair /dev/sda1
   ```

7. Se você receber a mensagem de erro "ERRO: o sistema de arquivos tem alterações de metadados valiosas em um registro que precisa ser reproduzido", crie um diretório temporário e monte o sistema de arquivos:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Se o disco não for montado, execute o comando xfs_repair com a opção -L (zeragem do registro de força):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Em seguida, tente montar o sistema de arquivos. Se o disco for montado com sucesso, você receberá a seguinte saída:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Reinicie a VM e verifique se o problema está resolvido.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Repare a VM offline

1. Conecte o disco do sistema da VM como um disco de dados a uma VM de recuperação (qualquer VM Linux em funcionamento). Para fazer isso, você pode usar [comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) ou pode automatizar a configuração da VM de recuperação usando os [comandos de reparo](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)da VM .

2. Localize a etiqueta da unidade do disco do sistema que você anexou. Neste caso, assumimos que a etiqueta do disco do sistema que você anexou é /dev/sdc1. Substitua-o pelo valor apropriado para sua VM.

3. Use xfs_repair com a opção -n para detectar os erros no sistema de arquivos.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Execute o seguinte comando para reparar o sistema de arquivos:

   ```
   xfs_repair /dev/sdc1
   ```

5. Se você receber a mensagem de erro "ERRO: o sistema de arquivos tem alterações de metadados valiosas em um registro que precisa ser reproduzido", crie um diretório temporário e monte o sistema de arquivos:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Se o disco não for montado, execute o comando xfs_repair com a opção -L (zeragem do registro de força):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Em seguida, tente montar o sistema de arquivos. Se o disco for montado com sucesso, você receberá a seguinte saída:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Desmontar e desprender o disco rígido virtual original e, em seguida, criar uma VM a partir do disco do sistema original. Para fazer isso, você pode usar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou os [comandos de reparo](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) da VM se você os usou para criar a VM de recuperação.

8. Verifique se o problema está resolvido.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma VM Linux anexando o disco do SO a uma VM de recuperação com a CLI 2.0 do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Utilize o portal para anexar um disco de dados a uma VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

