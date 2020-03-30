---
title: O desligamento do Azure Virtual Machines está preso nos serviços de reinicialização, desligamento ou parada | Microsoft Docs
description: Este artigo ajuda você a solucionar problemas de serviço em Máquinas Virtuais do Windows do Azure.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371364"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>O desligamento do Azure Windows VM está preso em "Reinicialização", "Desligamento" ou "Serviços de parada"

Este artigo fornece etapas para resolver os problemas de mensagens "Reinicialização", "Desligar" ou "Parar serviços" que você pode encontrar ao reiniciar uma máquina virtual do Windows (VM) no Microsoft Azure.

## <a name="symptoms"></a>Sintomas

Quando você usa [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você pode ver que a captura de tela exibe a mensagem "Reinicialização", "Desligamento" ou "Serviços de interrupção".

![Reinicialização, desligamento e parada de telas de serviços](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

O Windows usa o processo de desligamento para executar operações de manutenção do sistema e processa alterações, como atualizações, funções e recursos. Não é recomendável interromper este processo crítico até que ele complete. Dependendo do número de atualizações/alterações e do tamanho da VM, o processo pode levar muito tempo. Se o processo for interrompido, é possível que o Sistema Operacional se torne corrupto. Só interrompa o processo se estiver demorando muito.

## <a name="solution"></a>Solução

### <a name="collect-a-process-memory-dump"></a>Coletar um despejo de memória process

1. Baixe [a ferramenta Procdump](http://download.sysinternals.com/files/Procdump.zip) em um disco de dados novo ou existente, que é anexado a uma VM de funcionamento da mesma região.

2. Desconecte o disco contendo os arquivos necessários da VM em funcionamento e conecte o disco à sua VM quebrada. Estamos chamando este disco de **disco de utilitário**.

Use [o Console Serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) para completar as seguintes etapas:

1. Abra um Powershell administrativo e verifique o serviço que está pendurado ao parar.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Em um CMD administrativo, obter o PID do serviço suspenso.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Obtenha uma amostra de despejo <STOPPING SERVICE>de memória do processo de suspensão.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Agora mate o processo suspenso para desbloquear o processo de desligamento.

   ``
   taskkill /PID <PID> /t /f
   ``

Uma vez que o sistema operacional recomece, se ele inicializar normalmente, então apenas certifique-se de que a consistência do sistema operacional está ok. Se a corrupção for relatada, execute o seguinte comando até que o disco esteja livre de corrupção:

``
dism /online /cleanup-image /restorehealth
``

Se você não conseguir coletar um despejo de memória de processo, ou este problema for recursivo e você precisar de uma análise de causa raiz, prossiga com a coleta de um dump de memória do SISTEMA OPERACIONAL abaixo, o proceder para abrir uma solicitação de suporte.

### <a name="collect-an-os-memory-dump"></a>Coletar um despejo de memória do sistema operacional

Se o problema não se resolver depois de esperar que as alterações se processem, você precisará coletar um arquivo de despejo de memória e suporte de contato. Para coletar o arquivo de despejo, siga estas etapas:

**Conecte o disco do SISTEMA OPERACIONAL a uma VM de recuperação**

1. Tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Conecte o disco do SISTEMA OPERACIONAL a uma VM de recuperação](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Área de trabalho remota para a VM de recuperação.

4. Se o disco do SISTEMA OPERACIONAL estiver criptografado, você deve desativar a criptografia antes de passar para o próximo passo. Para obter mais informações, consulte [Descriptografar o disco do SISTEMA OPERACIONAL criptografado na VM que não pode inicializar](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Localize o arquivo de despejo e envie um tíquete de suporte**

1. Na VM de recuperação, vá até a pasta do Windows no disco do SO anexado. Se a letra da unidade atribuída ao disco do SO anexado for F, você precisará ir até F:\Windows.

2. Localize o arquivo memory.dmp e envie [um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo.

Se não encontrar o arquivo de despejo, passe para a próxima etapa para habilitar o log de despejo e o Console Serial.

**Habilitar o log de despejo e o Console Serial**

Para habilitar o log de despejo e o Console Serial, execute o script a seguir.

1. Abra a sessão de prompt de comandos com privilégios elevados (executar como administrador).

2. Execute o seguinte script:

   Neste script, assumimos que a letra de unidade atribuída ao disco do SISTEMA OPERACIONAL anexado é F. Substitua-a pelo valor apropriado em sua VM.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Verifique se há espaço suficiente no disco para alocar tanta memória quanto a RAM, que depende do tamanho que você está selecionando para esta VM.

4. Se não houver espaço suficiente ou a VM for grande (sérieG, GS ou E), você pode alterar o local onde este arquivo será criado e encaminhá-lo para qualquer outro disco de dados, que está conectado à VM. Para alterar o local, você deve alterar a seguinte tecla:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Desconecte o disco do sistema operacional e, em seguida, reconecte o disco do sistema operacional à VM afetada](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Inicie a VM e acesse o Console Serial.

7. Selecione Enviar Interrupção Não-Mascarada (NMI) para acionar o despejo de memória.

   ![Enviar interrupção não-mascarada](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Conecte o disco do SISTEMA OPERACIONAL a uma VM de recuperação novamente, colete o arquivo de despejo.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Depois de coletar o arquivo de despejo, entre em contato com o suporte da Microsoft para determinar a causa raiz.
