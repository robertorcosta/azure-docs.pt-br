---
title: Botas VM Linux para Grub Rescue
description: A máquina virtual falhou porque a máquina virtual entrou em um console de resgate
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561942"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Botas VM Linux para Grub Rescue

Identificamos que sua Máquina Virtual (VM) entrou em um console de resgate. O problema ocorre quando o Linux VM teve alterações no kernel aplicadas recentemente, como uma atualização do kernel, e não está mais sendo iniciado corretamente devido a erros no kernel durante o processo de inicialização. Durante o processo de inicialização, quando o carregador de inicialização tenta localizar o kernel Linux e entregar o controle de inicialização para ele, a VM entra em um console de resgate quando o handoff falha.

Se você descobrir que não pode se conectar a uma VM no futuro, você pode visualizar uma captura de tela de sua VM usando a lâmina de diagnóstico de inicialização no portal Azure. Isso pode ajudá-lo a diagnosticar o problema e determinar se um erro de inicialização semelhante é a causa.

## <a name="recommended-steps"></a>Etapas recomendadas

Siga as etapas de mitigação abaixo, dependendo do erro recebido:

### <a name="error---unknown-filesystem"></a>Erro - Sistema de arquivos desconhecido

* Se você estiver recebendo o erro **Sistema de arquivos desconhecido,** esse erro pode resultar de uma corrupção no sistema de arquivos na partição de inicialização ou de uma configuração incorreta do kernel.

   * Para problemas no sistema de arquivos, siga as etapas do artigo [Linux Recovery: Não é possível SSH para Linux VM devido a erros no sistema de arquivos (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Para problemas no kernel, siga as etapas do artigo [Linux Recovery: Corrigindo manualmente problemas de não inicialização relacionados a problemas de kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)ou Recuperação do [Linux: Corrigindo problemas de não inicialização relacionados a problemas de kernel usando chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Erro - Arquivo não encontrado

* Se você estiver recebendo o erro **Erro 15: Arquivo não encontrado ou disco RAM inicial** ou arquivo **initrd/initramfs não encontrado,** siga as etapas abaixo.

    * Para o `/boot/grub2/grub.cfg` arquivo `initrd/initramfs` ausente ou prosseguir com o seguinte processo:

    1. Certifique-se `/etc/default/grub` de existir e ter configurações corretas/desejadas. Se você não sabe quais são as configurações padrão, você pode verificar com uma VM em funcionamento.

    2. Em seguida, execute o seguinte comando para regenerar sua configuração:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se o arquivo `/boot/grub/menu.lst`ausente for, este erro é para versões mais antigas do sistema operacional (**RHEL 6.x**, **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam diferir. Você terá que girar um servidor antigo e testar para garantir que os comandos corretos sejam fornecidos.

### <a name="error---no-such-partition"></a>Erro - Não existe tal partição

* Se você estiver recebendo o erro **Nenhuma partição,** consulte [o caso cenário: erro "sem tal partição" ao tentar iniciar a VM depois de tentar estender a unidade do Sistema Operacional](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Erro - arquivo grub.cfg não encontrado

* Se você estiver recebendo o **arquivo erro /boot/grub2/grub.cfg não encontrado,** siga as etapas abaixo.

    * Para o `/boot/grub2/grub.cfg` arquivo `initrd/initramfs` ausente ou prosseguir com o seguinte processo:

    1. Certifique-se `/etc/default/grub` de existir e ter configurações corretas/desejadas. Se você não sabe quais são as configurações padrão, você pode verificar com uma VM em funcionamento.

    2. Em seguida, execute o seguinte comando `grub2-mkconfig -o /boot/grub2/grub.cfg`para regenerar sua configuração: .

   * Se o arquivo `/boot/grub/menu.lst`ausente estiver, este erro é para versões mais antigas do sistema operacional **(RHEL 6.x,** **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam adiar. Gire um servidor antigo e teste-o para garantir que os comandos corretos sejam fornecidos.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Agente de Máquina Virtual do Azure](../extensions/agent-windows.md)
* [Recursos e extensões da máquina virtual para Windows](../extensions/features-windows.md)

