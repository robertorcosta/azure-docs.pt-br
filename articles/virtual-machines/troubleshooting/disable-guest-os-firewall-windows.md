---
title: Desativar o firewall do sistema operacional convidado na VM do Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 6883d046078b5dccd2f1e83e87b41ca83edc92e3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030591"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Desativar o firewall do sistema operacional convidado na VM do Azure

Este artigo fornece uma referência para situações em que você suspeita de que o firewall do sistema operacional convidado está filtrando o tráfego parcial ou completo para uma máquina virtual (VM). Isso pode ocorrer se alterações que foram feitas ao firewall fizeram com que as conexões RDP falhassem.

## <a name="solution"></a>Solução

O processo descrito neste artigo se destina a ser usado como uma solução alternativa para que você possa se concentrar em corrigir o problema real, que é como configurar as regras de firewall corretamente. It\rquote uma prática recomendada da Microsoft para ter o componente do Firewall do Windows habilitado. A maneira como você configura as regras do firewall \cf3 depende do nível de acesso à VM \rquote necessária.

### <a name="online-solutions"></a>Soluções on-line 

Se a VM estiver on-line e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer as mitigações a seguir usando a outra VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Mitigação 1: Extensão de script personalizado ou recurso de executar comando

Se você tiver um agente do Azure em funcionamento, você pode usar a [Extensão de Script Personalizado](../extensions/custom-script-windows.md) ou o recurso [Executar Comandos](../windows/run-command.md) (somente VMs do Resource Manager) para executar remotamente os scripts a seguir.

> [!Note]
> * Se o firewall for definido localmente, execute o script a seguir:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Se o firewall é definido por meio de uma política do Active Directory, você poderá executar o seguinte script para acesso temporário. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   No entanto, assim que a política é aplicada novamente, você vai será retirado da sessão remota. A correção permanente para esse problema é modificar a política que é aplicada neste computador.

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

1.  Conecte-se a uma VM que está localizada na mesma rede virtual da VM que você não pode acessar por meio de conexão de RDP.

2.  Abra uma janela do console do PowerShell.

3.  Execute os seguintes comandos:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Se o firewall for definido por meio de um objeto Política de Grupo, esse método poderá não funcionar porque esse comando altera apenas as entradas do Registro local. Se uma política estiver em vigor, ela substituirá essa alteração. 

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: Comandos do PSTools

1.  Na VM de solução de problemas, baixe [ PSTools ](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância do CMD e acesse a VM por meio de seu DIP.

3.  Execute os seguintes comandos:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Mitigação 4: Registro remoto 

Siga estas etapas para usar o [Registro Remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Na solução de problemas da VM, inicie o editor do registro e selecione **Arquivo** > **Conecte o Registro de Rede**.

2.  Abra a ramificação \System do *computador de destino*e especifique os seguintes valores:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Reinicie o serviço. Uma vez que você não pode fazer isso usando o registro remoto, você deve usar Remover o Console de Serviço.

4.  Abra uma instância de **Services. msc**.

5.  Clique em **Serviços (Local)** .

6.  Selecione **Conectar-se a outro computador**.

7.  Insira o **endereço IP privado (DIP)** da VM com problema.

8.  Reinicie a política de firewall local.

9.  Tente conectar-se à VM via RDP novamente do seu computador local.

### <a name="offline-solutions"></a>Soluções Offline 

Se você tiver uma situação em que você não pode alcançar a VM por meio de qualquer método, Extensão do Script Personalizado falhará e você terá o trabalho no modo OFFLINE trabalhando diretamente através do disco do sistema. Para fazer isso, siga estas etapas:

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma conexão de área de trabalho remota para a VM de recuperação.

3.  Verifique se o disco está sinalizado como Online no console de Gerenciamento de Disco. Anote a letra da unidade atribuída ao disco do sistema operacional anexado.

4.  Antes de fazer qualquer alteração, crie uma cópia da pasta\windows\system32\config no caso de ser necessária uma reversão das alterações.

5.  Na solução de problemas da VM, inicie o editor do registro (regedit.exe). 

6.  Para este procedimento de solução de problemas, nós estamos montando os hives como BROKENSYSTEM e BROKENSOFTWARE.

7.  Realce a chave HKEY_LOCAL_MACHINE e, em seguida, selecione Arquivo > Carregar Hive no menu.

8.  Localize o arquivo \windows\system32\config\SYSTEM no disco do sistema anexado.

9.  Abra uma instância do PowerShell elevada e execute os comandos a seguir:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Desanexar o disco do sistema e recriar a VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema foi resolvido.
