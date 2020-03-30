---
title: Erro de parada do Windows -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373356"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Erro de parada do Windows - #0x000000EF "Processo Crítico Morreu"

Este artigo fornece etapas para resolver problemas em que um processo crítico morre durante a inicialização em uma VM Azure.

## <a name="symptom"></a>Sintoma

Quando você usa [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você verá que a captura de tela exibe o erro *#0x000000EF* com a mensagem Critical *Process Died*.

!["Seu computador teve um problema e precisa ser reiniciado. Estamos apenas coletando algumas informações sobre o erro e, em seguida, você pode reiniciá-lo. (##% completo) Se você quiser saber mais, você pode pesquisar online mais tarde por este erro: 0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

Normalmente, isso é devido a uma falha no processo crítico do sistema durante a inicialização. Você pode ler mais sobre problemas críticos do processo em "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e acessar uma VM de reparo.
2. Corrigir qualquer corrupção do SISTEMA OPERACIONAL.
3. **Recomendação**: Antes de reconstruir a VM, habilite a coleção de consoles seriados e dump de memória.
4. Reconstrua a VM.

> [!NOTE]
> Ao encontrar esse erro de inicialização, o Sistema Operacional convidado não está operacional. Você estará solucionando problemas no modo Offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2. Usando a conexão remota da área de trabalho conecte-se à VM de reparo.

### <a name="fix-any-os-corruption"></a>Corrigir qualquer corrupção do sistema operacional

1. Abra um prompt de comandos com privilégios elevados.
2. Execute o seguinte comando SFC (System File Checker:

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Onde < DRIVE DE DISCO DE INICIALIZAÇÃO > é o volume de inicialização da VM de reparo (tipicamente "C:") e < UNIDADE DE DISCO QUEBRADO > será a letra de unidade para o disco conectado da VM quebrada. Substitua os símbolos maiores /menores que os símbolos, bem como o texto contido neles, por exemplo, "< texto aqui >", com a letra apropriada.

3. Em seguida, use [o passo 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM e ver se ele inicializa.
4. Se a VM ainda não estiver inicializando, continue a coletar o arquivo de despejo de memória.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Se o problema persistir após a execução do SFC, será necessária a análise de um arquivo de despejo de memória para determinar a causa do problema. Para coletar o arquivo de despejo de memória, siga estas etapas:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Conecte o disco do SISTEMA OPERACIONAL a uma nova VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma nova VM de reparo.
2. Usando a conexão remota da área de trabalho conecte-se à VM de reparo.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o arquivo de despejo e envie um bilhete de suporte

3. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional conectado. Se a letra do driver atribuída ao disco do sistema operacional anexado for *F,* você precisará ir para *F:\Windows*.
4. Localize o arquivo *memory.dmp* e envie [um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.

   > [!NOTE]
   > Se você não conseguir encontrar o arquivo de despejo, complete as etapas abaixo para ativar a coleta de despejo de memória e o Console Serial, então retorne a esta seção e repita as etapas na tarefa acima para coletar o arquivo de despejo de memória.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir a VM, habilite a coleção de consoles serial e de despejo de memória

Para habilitar a coleção de dump de memória e o Serial Console, execute o seguinte script:

1. Abra uma sessão de prompt de comando elevada (Execute como administrador).
2. Execute os seguintes comandos:

   Habilitar console serial

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Substitua qualquer símbolo maior ou menor que os símbolos, bem como o texto dentro deles, por exemplo, "< texto aqui >".

3. Verifique se o espaço livre no disco do SO é tanto quanto o tamanho da memória (RAM) na VM.

Se não houver espaço suficiente no disco do SISTEMA OPERACIONAL, você deve alterar o local onde o arquivo de despejo de memória será criado e encaminhá-lo para qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar a localização, substitua "%SystemRoot%" pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para ativar o dump do SISTEMA OPERACIONAL

**Carregar disco do sistema operacional quebrado:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Habilitar no ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Habilitar no ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Descarregar disco do sistema operacional quebrado:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Reconstruir o VM Original

Use [a etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.
