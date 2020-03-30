---
title: Recuperar VMs Linux usando chroot onde LVM (Logical Volume Manager) é usado - VMs Azure
description: Recuperação de VMs Linux com LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684142"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Solução de problemas de um VM Linux quando não há acesso ao console serial Azure e o layout do disco está usando LVM (Logical Volume Manager)

Este guia de solução de problemas é benéfico para cenários em que uma VM Linux não está inicializando, ssh não é possível e o layout do sistema de arquivos subjacente é configurado com LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Tire o instantâneo da VM em falha

Tire uma foto da VM afetada. 

O instantâneo será então anexado a uma VM **de resgate.** Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) sobre como tirar uma **foto**.

## <a name="create-a-rescue-vm"></a>Criar uma VM de resgate
Normalmente, recomenda-se uma VM de resgate da mesma versão do sistema operacional ou similar. Use a mesma **região** e grupo de **recursos** da VM afetada

## <a name="connect-to-the-rescue-vm"></a>Conecte-se à VM de resgate
Conecte-se usando ssh na VM **de resgate.** Elevar privilégios e tornar-se super usuário usando

`sudo su -`

## <a name="attach-the-disk"></a>Anexar o disco
Anexar um disco à VM de **resgate** feita a partir do instantâneo tirado anteriormente.

Portal Azure -> selecionar os **discos** de > de **vm** de resgate 

![Criar disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Preencha os campos. Atribuir um nome ao seu novo disco, selecionar o mesmo Grupo de recursos que o snapshot, a VM afetada e a VM de resgate.

O **tipo de origem** é **Snapshot** .
O **instantâneo Fonte** é o nome do **instantâneo** criado anteriormente.

![criar disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Crie um ponto de montagem para o disco conectado.

`mkdir /rescue`

Execute o comando **fdisk -l** para verificar se o disco de snapshot foi anexado e liste todos os dispositivos e partições disponíveis

`fdisk -l`

Na maioria dos cenários, o disco de instantâneo conectado será visto como **/dev/sdc** exibindo duas partições **/dev/sdc1** e **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

O **\*** indica uma partição de inicialização, ambas as partições devem ser montadas.

Execute o **comando lsblk** para ver os LVMs da VM afetada

`lsblk`

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verifique se os LVMs da VM afetada são exibidos.
Caso assim, use os comandos abaixo para habilitá-los e reexecutar **lsblk**.
Certifique-se de que os LVMs do disco anexado são visíveis antes de prosseguir.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Localize o caminho para montar o Volume Lógico que contém a partição / (raiz). Ele tem os arquivos de configuração como /etc/default/grub

Neste exemplo, tirar a saída do comando **lsblk** anterior **rootvg-rootlv** é a **raiz** correta lv para montar e pode ser usado no próximo comando.

A saída do próximo comando mostrará o caminho para montar para o LV **raiz**

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Continue a montar este dispositivo no diretório/resgate

`mount /dev/rootvg/rootlv /rescue`

Monte a partição que tem o **sinalizador Boot** definido em /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Verifique se os sistemas de arquivos do disco anexado estão agora corretamente montados usando o comando **lsblk**

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

ou o **comando df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Obtendo acesso chroot

Obtenha acesso **chroot,** o que permitirá que você execute várias correções, pequenas variações existem para cada distribuição Linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se um erro for experimentado, como:

**chroot: falha ao executar o comando '/bin/bash': Nenhum arquivo ou diretório**

tentativa de montar o **usr** Volume Lógico

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Ao executar comandos em um ambiente **chroot,** observe que eles são executados contra o disco do SISTEMA OPERACIONAL conectado e não contra a VM de **resgate** local. 

Os comandos podem ser usados para instalar, remover e atualizar software. Solucionar problemas de VMs para corrigir erros.


Execute o comando lsblk e o /rescue é agora ![/ e /rescue/boot é /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Executar correções

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exemplo 1 - configurar a VM para inicializar a partir de um kernel diferente

Um cenário comum é forçar uma VM a inicializar a partir de um kernel anterior, pois o kernel instalado atual pode ter ficado corrompido ou uma atualização não foi concluída corretamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Walkthrough*

O comando **grep** lista os núcleos que **grub.cfg** está ciente.
![Kernels](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv lista** exibe qual kernel será ![carregado no próximo padrão de inicialização Kernel](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2 set-default** é usado para ![mudar para outro conjunto de kernel Grub2](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** lista exibe qual kernel será ![carregado na próxima inicialização Novo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** reconstrói grub.cfg usando ![as versões requeridas Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exemplo 2 - pacotes de atualização

Uma atualização do kernel com falha pode tornar o VM não inicializador.
Monte todos os volumes lógicos para permitir que os pacotes sejam removidos ou reinstalados

Execute o comando **lvs** para verificar quais **LVs** estão disponíveis para montagem, cada VM, que foi migrada ou vem de outro Provedor de Nuvem, variará na configuração.

Saia do ambiente **chroot** montar o **LV** necessário

![Avançado](./media/chroot-logical-volume-manager/advanced.png)

Agora acesse o ambiente **chroot** novamente executando

`chroot /rescue`

Todos os LVs devem ser visíveis como partições montadas

![Avançado](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Consultar o **kernel** instalado

![Avançado](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessário, remova ou atualize o **kernel**
![Avançado](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exemplo 3 - habilitar console serial
Se o acesso não for possível para o console serial Azure, verifique os parâmetros de configuração do GRUB para sua VM Linux e corrija-os. Informações detalhadas podem ser encontradas [neste doc](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Exemplo 4 - carregamento do kernel com volume de swap LVM problemático

Uma VM pode não inicializar totalmente e cair no **prompt dracut.**
Mais detalhes sobre a falha podem ser localizados no console serial do Azure ou navegar até o portal Azure -> diagnósticos de inicialização -> log serial


Um erro semelhante a este pode estar presente:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

O grub.cfg está configurado neste exemplo para carregar um LV com o nome de **rd.lvm.lv=VG/SwapVol** e a VM não consegue localizá-lo. Esta linha mostra como o kernel está sendo carregado fazendo referência ao LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Remova o LV ofensivo da configuração /etc/default/grub e reconstrua grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Saia chroot e troque o disco do sistema operacional

Depois de reparar o problema, proceda para desmontar e desprender o disco da VM de resgate, permitindo que ele seja trocado com o disco vm do sistema operacional VM afetado.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Retire o disco da VM de resgate e execute um Disk Swap.

Selecione a VM nos discos do portal e selecione
![ **desapegar**o disco Despato **Disks**](./media/chroot-logical-volume-manager/detach-disk.png) 

Salvar as ![alterações Salvar desapego](./media/chroot-logical-volume-manager/save-detach.png) 

O disco agora estará disponível permitindo que ele seja trocado com o disco original do SISTEMA OPERACIONAL da VM afetada.

Navegue no portal Azure até a VM com falha e selecione **Discos** -> **Swap disco do OS Disk**
![Swap](./media/chroot-logical-volume-manager/swap-disk.png) 

Complete os campos o **disco Escolha** é o disco de instantâneo saqueado na etapa anterior. O nome VM da VM afetada também é necessário e selecione **OK**

![Novo disco do sistema operacional](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se a VM estiver em execução, o Disk Swap irá desligá-lo, reinicie a VM assim que a operação de troca de disco estiver concluída.


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

 [Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Modo de usuário único](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
