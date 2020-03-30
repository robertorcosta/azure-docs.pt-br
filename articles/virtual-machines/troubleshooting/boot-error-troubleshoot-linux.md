---
title: Solucionando problemas de inicialização em Máquinas Virtuais Do Azure Linux | Microsoft Docs
description: Este artigo ajuda a vinculá-lo a artigos para solucionar erros de inicialização em Máquinas Virtuais Do Azure Linux.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408736"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Solucionar problemas de inicialização de máquinas virtuais do Azure Linux

Este artigo lista os erros comuns de inicialização que você pode receber ao iniciar uma máquina virtual Linux (VM) no Microsoft Azure. Para obter mais informações sobre os erros, consulte os artigos na seção **erros e soluções de inicialização**.

## <a name="boot-errors-and-solutions"></a>Erros e soluções de inicialização

* [Resgate grub](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Próximas etapas

- [Console Serial da máquina virtual](serial-console-linux.md)

Solucionando problemas de uma VM Linux anexando o disco do sistema operacional a uma VM de recuperação usando o Azure:

- [Reparo de VM azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap – isso pode ser automatizado usando qualquer um:
- [Scripts de recuperação da concha de energia](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [scripts de recuperação bash](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Portal Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Vídeo de troca de disco:

Se você não tiver acesso ao GRUB assista [a este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja, como você pode automatizar facilmente o procedimento de troca de disco para recuperar sua VM

## <a name="unofficial-solution"></a>Solução não oficial

A recuperação de uma VM também pode ser tentada com o script BETA não suportado [ALAR](https://github.com/malachma/azure-auto-recover)