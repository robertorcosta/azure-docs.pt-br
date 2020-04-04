---
title: Azure VM não responde com erro C01A001D ao aplicar o Windows Update
description: Este artigo fornece etapas para resolver problemas onde a atualização do Windows gera um erro e fica sem resposta em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633951"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>VM não responde com erro "C01A001D" ao aplicar o Windows Update

Este artigo fornece etapas para resolver problemas onde o Windows Update (KB) gera um erro e fica sem resposta em uma VM Azure.

## <a name="symptoms"></a>Sintomas

Ao usar [os diagnósticos de Inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a captura de tela da VM, a Atualização do Windows (KB) em andamento é exibida, mas falha com o código de erro: 'C01A001D'.

![Atualização do Windows sem resposta](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Um arquivo central não pode ser criado no sistema de arquivos. O sistema operacional não consegue gravar arquivos no disco.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e acessar uma VM de reparo](#create-and-access-a-repair-vm).
2. [Liberar espaço no disco rígido.](#free-up-space-on-the-hard-disk)
3. [Recomendado: Antes de reconstruir a VM, habilite a coleção de consoles serial e de despejo de memória](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Reconstruir o VM](#rebuild-the-vm).

> [!NOTE]
> Quando esse erro ocorre, o Sistema Operacional convidado não está operacional. Você deve solucionar problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Siga [as etapas 1-3 dos Comandos de Reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2. Conecte-se à VM de reparo usando a conexão remota da área de trabalho.

### <a name="free-up-space-on-the-hard-disk"></a>Liberar espaço no disco rígido

Se o disco já não é de 1 Tb, você deve redimensioná-lo. Uma vez que o disco seja de 1 TB, execute uma limpeza de disco e uma desfragmentação da unidade.

1. Verifique se o disco está cheio. Se o disco estiver abaixo de 1 Tb, [expanda-o para um máximo de 1 Tb usando powerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Uma vez que o disco seja de 1 Tb, execute uma limpeza de disco.
    - [Desconecte o disco de dados da VM quebrada](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Conecte o disco de dados a uma VM em funcionamento](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Use a [ferramenta Limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço.
3. Depois de redimensionar e limpar, desfragmente a unidade:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Dependendo do nível de fragmentação, isso pode levar horas.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir a VM, habilite a coleção de consoles serial e de despejo de memória

1. Abra uma sessão de prompt de comando elevada (Execute como administrador).
2. Execute os seguintes comandos:

    Ativar console serial:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Certifique-se de que o espaço livre no disco do SISTEMA OPERACIONAL seja pelo menos igual ao tamanho da memória VM (RAM).

    Se não houver espaço suficiente no disco do SISTEMA OPERACIONAL, altere o local onde o arquivo de despejo de memória será criado e encaminhe-o para um disco de dados conectado à VM e com espaço livre suficiente. Para alterar o `%SystemRoot%` local, substitua-a pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo:

    **Habilitar a configuração sugerida de despejo do sistema operacional:**

    Carregar disco do sistema operacional quebrado:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Habilitar no ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Habilitar no ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Descarregar disco do sistema operacional quebrado:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Use [a etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.
