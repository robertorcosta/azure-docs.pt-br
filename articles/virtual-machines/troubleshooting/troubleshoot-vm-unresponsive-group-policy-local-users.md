---
title: A máquina virtual não está respondendo ao aplicar a política ' Política de Grupo usuários locais & grupos '
description: Este artigo fornece etapas para resolver problemas em que a tela de carga está presa aplicando uma política durante a inicialização em uma VM (máquina virtual) do Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620851"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A máquina virtual não está respondendo ao aplicar a política ' Política de Grupo usuários locais & grupos '

Este artigo fornece etapas para resolver problemas em que a tela de carga está presa aplicando uma política, durante a inicialização, em uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Quando você usa o [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você verá que ela está presa no carregamento com a mensagem: *aplicando política de grupo política de grupos e usuários locais*.

![Texto alt: tela mostrando a aplicação Política de Grupo carregamento da política de grupos e usuários locais (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Texto alt: tela mostrando a aplicação Política de Grupo carregamento da política de grupos e usuários locais (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Causa

Os sintomas desse congelamento são causados por um defeito de código na biblioteca de vínculo dinâmico do serviço de perfil do Windows (*profsvc. dll*).

> [!NOTE]
> Esse defeito se aplica somente ao Windows Server 2012 e ao Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>A política em questão

A política que está sendo aplicada que não concluirá seus processos é:

* *Os modelos de configuração do computador \ sistema/usuário Profiles\Delete perfis de usuário com mais de um número especificado de dias durante a reinicialização do sistema*

Esta política só será travada se as seis condições a seguir forem verdadeiras:

* A *exclusão de perfis de usuário mais antigos que um número especificado de dias na política de reinicialização do sistema* está habilitada.
* Você tem perfis que atenderam aos requisitos de idade para exigir a limpeza.
* Você tem componentes que registraram para notificação de exclusão para perfis.
* Os componentes fazem chamadas (diretas ou indiretas) que precisam adquirir dados dos componentes do Gerenciador de controle de serviço (SCM) do Windows, como informações de início, parada ou consulta sobre um serviço.
* Você tem um serviço configurado para iniciar como *automático*.
* Esse serviço é definido para ser executado sob o contexto de uma conta de domínio (em vez de usar uma conta interna, como um sistema local).

### <a name="the-code-defect"></a>O defeito do código

O defeito do código é devido ao SCM (Gerenciador de controle de serviço) e aos serviços de perfil que tentam aplicar bloqueios uns aos outros simultaneamente. Há bloqueios para impedir que vários serviços façam alterações nos mesmos dados ao mesmo tempo, o que causaria corrupção. Normalmente, várias solicitações de bloqueio não causarão um problema. No entanto, como isso está ocorrendo durante a inicialização, nenhum serviço pode concluir seus processos, pois eles ficam presos entre si.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Bug do so 5880648-deadlocks do Gerenciador de controle de serviço com a política "excluir perfis de usuário ao reiniciar"

Há duas ações que se sobrepõem para que:

* A ação 1 adquire o bloqueio de perfil, mas ainda não adquiriu o bloqueio de SCM.

  **E**

* A ação 2 adquire o bloqueio do SCM, mas ainda não adquiriu o bloqueio do perfil.

Quando esse deadlock ocorrer, a tentativa de adquirir o segundo bloqueio necessário suspende a ação.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Ação 1-notificação de exclusão de perfil antigo (tem **bloqueio de perfil**, precisa de **bloqueio de SCM**)

1. Primeiro, a política definida para excluir perfis antigos adquire um bloqueio de serviço de perfil interno.

   * Esse bloqueio está lá para impedir que dois threads interajam com os perfis enquanto a *operação de exclusão* está em andamento.

2. A política localiza perfis que são antigos o suficiente para serem excluídos.
3. Como parte da exclusão do perfil, um componente que foi registrado para notificações das exclusões de um perfil tenta **Iniciar um serviço**.
4. Antes de iniciar o serviço, o SCM (Gerenciador de controle de serviço) precisa adquirir um **bloqueio de SCM interno** mantido por threads na **ação 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Ação 2-carregamento/criação de perfil para dados específicos do usuário (tem **bloqueio de SCM**, precisa de **bloqueio de perfil**)

1. Na inicialização, o SCM precisa solicitar todos os serviços de *início automático* por seu grupo, bem como quaisquer serviços dos quais esses serviços dependem.

2. O **SCM adquire um bloqueio de SCM interno** usado para controlar o acesso para iniciar, interromper ou configurar serviços à medida que ele ordena os serviços.

3. Depois que os serviços estão em ordem, o SCM percorre cada serviço e o inicia.

4. Se o serviço estiver sendo executado sob o contexto de uma conta de domínio, um perfil precisará ser carregado ou criado para a conta de domínio, para que possa armazenar dados específicos do usuário.

5. Essa solicitação é enviada para o **serviço de perfil**.

6. O serviço de perfil precisa acessar o **bloqueio interno** adquirido na **ação 1**.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e acessar uma VM de reparo
2. Habilitar o console serial e a coleção de despejo de memória
3. Recriar a VM
4. Coletar o arquivo de despejo de memória

   > [!NOTE]
   > Ao encontrar esse erro de inicialização, o SO convidado não está operacional. Você estará Solucionando problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar uma VM de reparo.
2. Usando Conexão de Área de Trabalho Remota Conecte-se à VM de reparo.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

Para habilitar a coleta de despejo de memória e o console serial, execute o script abaixo:

1. Abra uma sessão de prompt de comando com privilégios elevados (executar como administrador).
2. Execute os comandos a seguir:

   * Habilitar console serial:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verifique se o espaço livre no disco do sistema operacional é tão grande quanto o tamanho da memória (RAM) na VM.

   * Se não houver espaço suficiente no disco do sistema operacional, você deverá alterar o local em que o arquivo de despejo de memória será criado e referir-se a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua `%SystemRoot%` pela letra da unidade (como "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para habilitar o despejo do sistema operacional

**Carregar disco do so quebrado:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Habilitar em ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Habilitar em ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Recriar a VM

Use [a etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, você precisaria primeiro coletar o arquivo de despejo de memória para a falha e contatar o suporte com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Use as etapas [1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar uma nova VM de reparo.

2. Usando Conexão de Área de Trabalho Remota Conecte-se à VM de reparo.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra da unidade atribuída ao disco do SO anexado for F, você precisará ir até F:\Windows.

2. Localize o arquivo Memory. dmp e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.

3. Se você estiver tendo problemas para localizar o arquivo Memory. dmp, talvez queira usar [chamadas de NMI (interrupção não mascaráveis) no console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Você pode seguir o guia para [gerar um kernel ou um](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) arquivo de despejo de memória completo usando chamadas NMI.
