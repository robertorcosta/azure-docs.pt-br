---
title: A máquina virtual do Azure não está respondendo ao aplicar a política
description: Este artigo fornece etapas para resolver problemas em que a tela de carregamento está parada ao aplicar uma política durante a inicialização em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748725"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A VM deixa de responder ao aplicar a política “Agrupar usuários e grupos de local de política de grupo”

Este artigo fornece etapas para resolver problemas em que a tela de carregamento fica parada ao aplicar uma política durante a inicialização em uma VM do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar [Diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para ver uma captura de tela da VM, a tela fica parada carregando com a mensagem: “*Aplicando política de grupos e usuários locais da política de grupo*”.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Captura da tela de carregamento “Aplicando política de grupos e usuários locais da política de grupo” (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Captura da tela de carregamento “Aplicando política de grupos e usuários locais da política de grupo” (Windows Server 2012).":::

## <a name="cause"></a>Causa

Há bloqueios conflitantes quando a política tenta limpar perfis de usuário antigos.

> [!NOTE]
> Aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Veja a política com problema:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e acessar uma VM de reparo](#step-1-create-and-access-a-repair-vm)
2. [Desabilitar a política](#step-2-disable-the-policy)
3. [Habilitar o console serial e a coleção de despejo de memória](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Recompilar a VM](#step-4-rebuild-the-vm)

> [!NOTE]
> Se você encontrar esse erro de inicialização, o sistema operacional convidado não está funcionando. Você precisa solucionar problemas no modo offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Etapa 1: Criar e acessar uma VM de reparo

1. Use as [etapas de 1a 3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar uma VM de reparo.
2. Use a conexão de área de trabalho remota para conectar-se à VM de reparo.

### <a name="step-2-disable-the-policy"></a>Etapa 2: Desabilitar a política

1. Na VM de reparo, abra o Editor do Registro.
2. Localize a chave **HKEY_LOCAL_MACHINE** e selecione **Arquivo** > **Carregar Hive…** no menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="A captura de tela mostra o HKEY_LOCAL_MACHINE realçado e o menu que contém Carregar Hive.":::

    - “Carregar Hive” permite carregar chaves do registro de um sistema offline, neste caso, o disco danificado anexado à VM de reparo.
    - As configurações de todo o sistema são armazenadas em `HKEY_LOCAL_MACHINE` e podem ser abreviadas como "HKLM".
3. No disco anexado, acesse o arquivo `\windows\system32\config\SOFTWARE` e abra-o.

    1. Será solicitado que você forneça um nome. Digite BROKENSOFTWARE.<br/>
    2. Para verificar se BROKENSOFTWARE foi carregado, expanda **HKEY_LOCAL_MACHINE** e procure a chave BROKENSOFTWARE adicionada.
4. Navegue até BROKENSOFTWARE e verifique se a chave CleanupProfile existe no hive carregado.

    1. Se a chave existir, a política CleanupProfile está definida e o valor dela representará a política de retenção em dias. Prossiga para excluir a chave.<br/>
    2. Se a chave não existir, a política CleanupProfile não está definida. [Envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), incluindo o arquivo memory.dmp que está no diretório do Windows do disco do sistema operacional anexado.

5. Use este comando pra excluir a chave CleanupProfiles:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Para descarregar o hive BROKENSOFTWARE, use este comando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Etapa 3: Habilitar o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute este script:

1. Abra uma sessão de prompt de comandos com privilégios elevados (Executar como administrador).
2. Execute estes comandos:

    **Habilitar console serial**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Verifique se o espaço livre no disco do sistema operacional é pelo menos igual ao tamanho da memória da VM (RAM).

    Se não houver espaço suficiente no disco do sistema operacional, altere o local do despejo de memória para um disco de dados anexado com espaço livre suficiente. Para alterar o local, substitua “% SystemRoot%” pela letra da unidade (ex.: “F:”) do disco de dados nos comandos abaixo.

    **Configuração sugerida para habilitar o despejo do sistema operacional**:

    Carregar disco danificado do sistema operacional:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Habilitar em ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Habilitar em ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Descarregar disco danificado do sistema operacional:
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Etapa 4: Recompilar a VM

Use a [etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.

Se o problema for corrigido, a política foi desabilitada localmente. Para uma solução permanente, não use a política CleanupProfiles em VMs. Use um método diferente para executar limpezas de perfil.

Não use esta política:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Próximas etapas

Se você encontrar problemas ao aplicar o Windows Update, consulte [A VM não está respondendo com o erro "C01A001D" ao aplicar Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).