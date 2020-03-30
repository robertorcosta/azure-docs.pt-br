---
title: A máquina virtual do Windows não pode inicializar devido ao gerenciador de inicialização do Windows
description: Este artigo fornece etapas para resolver problemas onde o Windows Boot Manager impede a inicialização de uma Máquina Virtual Do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373343"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>O Windows VM não pode inicializar devido ao Gerenciador de Inicialização do Windows

Este artigo fornece etapas para resolver problemas onde o Windows Boot Manager impede a inicialização de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

A VM está presa à espera de um prompt de usuário e não inicializa a menos que seja instruído manualmente.

Quando você usa [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a captura de tela da VM, você verá que a captura de tela exibe o Gerenciador de inicialização do Windows com a mensagem *Escolha um sistema operacional para iniciar ou pressione TAB para selecionar uma ferramenta:*.

A figura 1
 
![Gerenciador de inicialização do Windows informando "Escolha um sistema operacional para iniciar ou pressione TAB para selecionar uma ferramenta:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

O erro deve-se a um menu de *inicialização de* sinalizador BCD no Gerenciador de Inicialização do Windows. Quando o sinalizador está ativado, o Windows Boot Manager solicita ao usuário, durante o processo de inicialização, que selecione qual carregador deseja executar, causando um atraso na inicialização. No Azure, esse recurso pode adicionar ao tempo que leva para inicializar uma VM.

## <a name="solution"></a>Solução

Visão geral do processo:

1. Configure para tempo de inicialização mais rápido usando o console serial.
2. Criar e acessar uma VM de reparo.
3. Configure para tempo de inicialização mais rápido em uma VM de reparo.
4. **Recomendação**: Antes de reconstruir a VM, habilite a coleção de consoles seriados e dump de memória.
5. Reconstrua a VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configure para tempo de inicialização mais rápido usando o console serial

Se você tem acesso ao console serial, há duas maneiras de conseguir tempos de inicialização mais rápidos. Diminua o tempo de espera do *menu displayboot* ou remova completamente o sinalizador.

1. Siga as instruções para acessar [o Azure Serial Console para Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) para obter acesso ao console baseado em texto.

   > [!NOTE]
   > Se você não conseguir acessar o console serial, pule para [criar e acessar uma VM de reparo](#create-and-access-a-repair-vm).

2. **Opção A**: Reduzir o tempo de espera

   a. O tempo de espera é definido em 30 segundos por padrão, mas pode ser alterado para um tempo mais rápido (por exemplo, 5 segundos).

   b. Use o seguinte comando no console serial para ajustar o valor de tempo:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opção B**: Remova a bandeira BCD

   a. Para evitar que o menu de inicialização de exibição se insira completamente, digite o seguinte comando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se você não conseguiu usar o console serial para configurar um tempo de inicialização mais rápido nas etapas acima, você pode continuar com as seguintes etapas. Agora você estará solucionando problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2. Use a conexão de área de trabalho remota conectada à VM de reparo.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configure para tempo de inicialização mais rápido em uma VM de reparo

1. Abra um prompt de comandos com privilégios elevados.
2. Digite o seguinte para ativar DisplayBootMenu:

   Use este comando para **VMs de geração 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Use este comando para **VMs de geração 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Substitua qualquer símbolo maior ou menor que os símbolos, bem como o texto dentro deles, por exemplo, "< texto aqui >".

3. Alterar o valor de tempo para 5 segundos:

   Use este comando para **VMs de geração 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Use este comando para **VMs de geração 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Substitua qualquer símbolo maior ou menor que os símbolos, bem como o texto dentro deles, por exemplo, "< texto aqui >".

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