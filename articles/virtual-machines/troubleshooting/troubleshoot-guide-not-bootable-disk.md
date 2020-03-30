---
title: Erro de inicialização – "este não é um disco inicializado"
description: Este artigo fornece etapas para resolver problemas em que o disco não pode ser inicializado em uma máquina virtual do Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300973"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Erro de inicialização – Este não é um disco bootable

Este artigo fornece etapas para resolver problemas em que o disco não pode ser inicializado em uma VM (Azure Virtual Machine).

## <a name="symptoms"></a>Sintomas

Quando você usa [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você verá que a captura de tela exibe um prompt com a mensagem 'Este não é um disco inicializável. Por favor, insira um disquete bootable e pressione qualquer tecla para tentar novamente...'.

   A figura 1

   ![A Figura 1 mostra a mensagem *"Este não é um disco inicializado. Por favor, insira um disquete bootable e pressione qualquer tecla para tentar novamente..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Esta mensagem de erro significa que o processo de inicialização do sistema operacional não conseguiu localizar uma partição ativa do sistema. Esse erro também pode significar que há uma referência ausente no armazenamento BCD (Boot Configuration Data, dados de configuração de inicialização), impedindo-o de localizar a partição do Windows.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e acessar uma VM de reparo.
2. Defina o status de partição como ativo.
3. Corrigir a partição de disco.
4. **Recomendação**: Antes de reconstruir a VM, habilite a coleção de consoles seriados e dump de memória.
5. Reconstrua o VM Original.

   > [!NOTE]
   > Ao encontrar esse erro de inicialização, o Sistema Operacional convidado não está operacional. Você estará solucionando problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as etapas 1-3 dos comandos de reparo da [VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2. Usando a conexão remota da área de trabalho conecte-se à VM de reparo.

### <a name="set-partition-status-to-active"></a>Definir o status da partição como ativo

As VMs de geração 1 devem primeiro verificar se a partição do sistema operacional, que mantém a loja BCD, está marcada como *ativa*. Se você tiver uma VM de Geração 2, pule para [corrigir a partição de disco,](#fix-the-disk-partition)pois o sinalizador *Status* foi preterido na geração posterior.

1. Abra um prompt de comando elevado *(cmd.exe)*.
2. Digite *diskpart* para iniciar a ferramenta DISKPART.
3. Digite *o disco de lista* para listar os discos no sistema e identifique o Sistema Operacional Conectado VHD.
4. Uma vez que o Sistema Operacional VHD conectado esteja localizado, digite o *disco sel #* para selecionar o disco.  Consulte a Figura 2, onde o Disco 1 é o Sistema Operacional VHD conectado.

   Figura 2

   ![A Figura 2 mostra a janela *DISKPART* mostrando a saída do comando de disco de lista, o Disco 0 e o Disco 1 exibidos na tabela.  Também mostra a saída do comando sel disk 1, o Disco 1 é o disco selecionado](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Uma vez que o disco seja selecionado, *insira partição de lista* para listar as partições do disco selecionado.
6. Uma vez identificada a partição de inicialização, digite *a partição sel #* para selecionar a partição.  Normalmente, a partição de inicialização terá cerca de 350 MB de tamanho.  Veja a Figura 3, onde a Partição 1 é a partição de inicialização.

   Figura 3

   ![A Figura 3 mostra a janela *DISKPART* com a saída do comando *lista partição*. A partição 1 e a partição 2 são exibidas na tabela. Ele também mostra a saída do comando *sel partition 1*, quando a Partição 1 é o disco selecionado.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Digite 'partição de detalhes' para verificar o status da partição. Consulte a Figura 4, onde a partição está *ativa: Não*, ou Figura 5, onde a partição é 'Ativo: Sim'.

   Figura 4

   ![A Figura 4 mostra a janela *DISKPART* com a saída do comando *detail partition*, quando a Partição 1 está definida como *Ativa: Não*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![A Figura 5 mostra a janela *DISKPART* com a saída do comando *detail partition*, quando a Partição 1 está definida como *Ativa: Sim*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se a partição não estiver **ativa,** entre *ativo* para alterar o *sinalizador Ativo.*
9. Verifique se a alteração de status foi feita corretamente digitando *partição de detalhes*.

   Figura 6

   ![A Figura 6 mostra a janela de parte de disco com a saída do comando *partição de detalhes*, quando a Partição 1 está definida como *Ativa: Sim*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Digite a *saída* para fechar a ferramenta DISKPART e salvar suas alterações de configuração.

### <a name="fix-the-disk-partition"></a>Corrigir a partição de disco

1. Abra um prompt de comando elevado (cmd.exe).
2. Use o seguinte comando para executar *chkdsk* no disco e corrigir erros:

   `chkdsk <DRIVE LETTER>: /f`

   A adição da opção de comando '/f' corrigirá quaisquer erros no disco. Certifique-se <DRIVE LETTER> de substituir com a letra do SISTEMA OPERACIONAL anexo VHD.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir a VM, habilite a coleção de consoles serial e de despejo de memória

Para habilitar a coleção de dump de memória e o Serial Console, execute o seguinte script:

1. Abra uma sessão de prompt de comando elevada (Execute como administrador).
2. Execute os seguintes comandos:

   Habilitar console serial

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
