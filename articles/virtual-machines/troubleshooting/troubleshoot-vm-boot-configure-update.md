---
title: A inicialização da VM está parada em “Preparando o Windows. Não desligue o computador” no Azure | Microsoft Docs
description: Apresenta as etapas para solucionar o problema no qual a inicialização da VM fica paralisada em “Preparando o Windows. Não desligue o computador."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 4263afe33caa4d6471848c8e7dbf9bc1eeec4bee
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332517"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A inicialização da VM está parada em “Preparando o Windows. Não desligue o computador” no Azure

Este artigo descreve as telas "preparando-se" e "preparando o Windows" que você pode encontrar ao inicializar uma VM (máquina virtual) do Windows no Microsoft Azure. Ele fornece as etapas para ajudar você a coletar dados para um tíquete de suporte.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Sintomas

Uma VM do Windows não é inicializada. Ao usar o **diagnóstico de inicialização** para obter a captura de tela da VM, você pode ver que a VM exibe a mensagem "preparando-se" ou "preparando o Windows".

![Exemplo de mensagem para o Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Normalmente, esse problema ocorre quando o servidor está fazendo a reinicialização final depois que a configuração foi alterada. A alteração na configuração pode ter sido iniciada por atualizações do Windows ou alterações nos recursos ou nas funções do servidor. Para o Windows Update, quando o tamanho das atualizações é grande, o sistema operacional precisa de mais tempo para reconfigurar as alterações.

## <a name="collect-an-os-memory-dump"></a>Coletar um despejo de memória do sistema operacional

Se o problema não for resolvido depois de aguardar as alterações serem processadas, você precisará coletar um arquivo de despejo de memória e entrar em contato com o suporte. Para coletar o arquivo de despejo, siga estas etapas:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. Tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco de SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
3. Área de trabalho remota para a VM de recuperação. 
4. Se o disco do sistema operacional for criptografado, você deverá desligar a criptografia antes de passar para a próxima etapa. Para obter mais informações, consulte [descriptografar o disco do sistema operacional criptografado na VM que não pode ser inicializada](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localize o arquivo de despejo e envie um tíquete de suporte

1. Na VM de recuperação, vá até a pasta do Windows no disco do SO anexado. Se a letra da unidade atribuída ao disco do SO anexado for F, você precisará ir até F:\Windows.
2. Localize o arquivo Memory. dmp e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo. 

Se não encontrar o arquivo de despejo, passe para a próxima etapa para habilitar o log de despejo e o Console Serial.

### <a name="enable-dump-log-and-serial-console"></a>Habilitar o log de despejo e o Console Serial

Para habilitar o log de despejo e o Console Serial, execute o script a seguir.

1. Abra a sessão de prompt de comandos com privilégios elevados (executar como administrador).
2. Execute o seguinte script:

    Nesse script, presumimos que a letra da unidade atribuída ao disco do SO anexado é F. Substitua a letra pelo valor apropriado em sua VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Certifique-se de que haja espaço suficiente no disco para alocar a mesma quantidade de memória que a RAM, o que depende do tamanho que você está selecionando para essa VM.
    2. Se não houver espaço suficiente ou se a VM for grande (série G, GS ou E), você poderá alterar o local em que o arquivo será criado e fazer a referência para qualquer outro disco de dados anexado à VM. Para fazer isso, você precisará alterar a seguinte chave:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Desanexe o disco do SO e, em seguida, anexe-o novamente na VM afetada](../windows/troubleshoot-recovery-disks-portal.md).
4. Inicie a VM e acesse o console serial.
5. Selecione **Enviar NMI (interrupção não mascarada)** para disparar o despejo de memória.
    imagem ![the sobre onde enviar interrupção não mascarável @ no__t-1
6. Anexe o disco do sistema operacional a uma VM de recuperação novamente, colete o arquivo de despejo.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Depois de coletar o arquivo de despejo, entre em contato com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para executar a análise da causa raiz.