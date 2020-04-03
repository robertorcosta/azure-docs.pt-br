---
title: A Máquina Virtual não responde ao aplicar a política 'Política de grupo Usuários locais & Grupos'
description: Este artigo fornece etapas para resolver problemas onde a tela de carga está presa aplicando uma diretiva durante a inicialização em uma Máquina Virtual Azure (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620851"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A Máquina Virtual não responde ao aplicar a política 'Política de grupo Usuários locais & Grupos'

Este artigo fornece etapas para resolver problemas onde a tela de carga está presa aplicando uma diretiva, durante o boot, em uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando você usa [diagnósticode inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você verá que a tela está presa carregando com a mensagem: *Aplicação da política de grupo Usuários locais e grupos .*

![Texto Alt: Tela mostrando a aplicação da política de grupo Locais usuários e grupos de política de carregamento de políticas (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Texto Alt: Tela mostrando a aplicação da política de grupo Locais usuários e grupos de política de carregamento (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Causa

Os sintomas deste congelamento são causados por um defeito de código na Biblioteca de Link Dinâmico do Serviço de Perfil do Windows (*profsvc.dll*).

> [!NOTE]
> Esse defeito se aplica apenas no Windows Server 2012 e no Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>A política em questão

A política que está sendo aplicada que não terminará seus processos é:

* *Configuração do computador\Políticas\Modelos administrativos\Perfis do sistema/perfis de usuário\Exclua perfis de usuário mais antigos do que um número especificado de dias na reinicialização do sistema*

Esta política só será travada se as seguintes seis condições forem verdadeiras:

* O *excluir perfis de usuário mais antigos do que um número especificado de dias na diretiva de reinicialização do sistema* está ativado.
* Você tem perfis que atenderam aos requisitos de idade para exigir limpeza.
* Você tem componentes que se registraram para excluir notificação para perfis.
* Os componentes fazem chamadas (diretas ou indiretas) que precisam adquirir dados dos componentes do Service Control Manager (SCM) do Windows, como Iniciar, Parar ou Consultar informações sobre um serviço.
* Você tem um serviço configurado para iniciar como *automático*.
* Este serviço é definido para ser executado sob o contexto de uma conta de domínio (em vez de usar uma conta incorporada, como um sistema local).

### <a name="the-code-defect"></a>O defeito de código

O defeito de código deve-se ao Service Control Manager (SCM) e aos serviços profile que tentam aplicar bloqueios um no outro simultaneamente. Existem bloqueios para impedir que vários serviços sejam alterados nos mesmos dados ao mesmo tempo, o que causaria corrupção. Normalmente, vários pedidos de bloqueio não causariam problemas. No entanto, uma vez que isso está acontecendo durante o boot, nenhum serviço pode completar seus processos, pois eles estão presos esperando um pelo outro.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Bug do SISTEMA OPERACIONAL 5880648 - Impasses do Service Control Manager com a política "Excluir perfis de usuário na reinicialização"

Existem duas ações que se sobrepõem de modo que:

* A Ação 1 adquire o bloqueio de perfil, mas ainda não adquiriu o bloqueio SCM.

  **AND**

* A Ação 2 adquire o bloqueio SCM, mas ainda não adquiriu o bloqueio do perfil.

Uma vez que esse impasse ocorra, a tentativa de adquirir o segundo bloqueio necessário trava a ação.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Ação 1 - Notificação de exclusão de perfil antigo (tem **bloqueio de perfil,** precisa **de bloqueio de SCM**)

1. Primeiro, a diretiva definida para excluir perfis antigos adquire um bloqueio de serviço de perfil interno.

   * Este bloqueio está lá para evitar que dois segmentos interajam com os perfis enquanto a *operação de exclusão* é progresso.

2. A política encontra perfis que têm idade suficiente para serem excluídos.
3. Como parte da exclusão do perfil, um componente que se cadastrou para notificações das exclusões de um perfil tenta **iniciar um serviço**.
4. Antes de iniciar o serviço, o Service Control Manager (SCM) precisa adquirir um **bloqueio scm interno** mantido por threads na **Ação 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Ação 2 - Carga/criação de perfil para dados específicos do usuário (tem **bloqueio scm**, precisa **bloqueio de perfil)**

1. No boot, o SCM precisa encomendar todos os serviços *de início automático* por seu grupo, bem como todos os serviços dos quais esses serviços dependem.

2. **O SCM adquire um bloqueio scm interno** usado para controlar o acesso à partida, parada ou configuração de serviços à medida que ordena os serviços.

3. Uma vez que os serviços estejam em ordem, o SCM faz loops em cada serviço e o inicia.

4. Se o serviço estiver sendo executado sob o contexto de uma conta de domínio, um perfil precisa ser carregado ou criado para a conta de domínio, para que ele possa armazenar dados específicos do usuário.

5. Esta solicitação é enviada para o **Serviço de Perfil**.

6. O serviço de perfil precisa ter acesso ao **bloqueio interno** adquirido na **Ação 1**.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e acessar uma VM de reparo
2. Habilitar a coleção de despejo de memória e console serial
3. Reconstruir o VM
4. Coletar o arquivo de despejo de memória

   > [!NOTE]
   > Ao encontrar esse erro de inicialização, o Sistema Operacional convidado não está operacional. Você estará solucionando problemas no modo Offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar uma VM de reparo.
2. Usando a conexão remota da área de trabalho conecte-se à VM de reparo.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Habilitar a coleção de despejo de memória e console serial

Para habilitar a coleção de dump de memória e o Serial Console, execute o script abaixo:

1. Abra uma sessão de prompt de comando elevada (Execute como administrador).
2. Execute os seguintes comandos:

   * Ativar console serial:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verifique se o espaço livre no disco do SO é tanto quanto o tamanho da memória (RAM) na VM.

   * Se não houver espaço suficiente no disco do SISTEMA OPERACIONAL, você deve alterar o local onde o arquivo de despejo de memória será criado e encaminhá-lo para qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar a `%SystemRoot%` localização, substitua-a pela letra de unidade (como "F:") do disco de dados nos comandos abaixo.

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

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Use [a etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, você precisaria primeiro reunir o arquivo de despejo de memória para o crash e o suporte de contato com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Conecte o disco do SISTEMA OPERACIONAL a uma nova VM de reparo

1. Use as etapas [1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar uma nova VM de reparo.

2. Usando a conexão remota da área de trabalho conecte-se à VM de reparo.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o arquivo de despejo e envie um bilhete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional conectado. Se a letra da unidade atribuída ao disco do SO anexado for F, você precisará ir até F:\Windows.

2. Localize o arquivo memory.dmp e envie [um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.

3. Se você estiver tendo problemas para localizar o arquivo memory.dmp, você pode querer usar [chamadas nmi (interrupção não mascaradas) no console serial.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Você pode seguir o guia para [gerar um kernel ou concluir o](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) arquivo de falha usando chamadas NMI.
