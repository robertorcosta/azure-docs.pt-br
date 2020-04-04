---
title: Preparar um VHD do Windows para upload no Azure
description: Aprenda a preparar um Windows VHD ou VHDX para carregá-lo no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 8118ecde698b54213547e717d25613c0c3e0d3fd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631551"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure

Antes de carregar uma máquina virtual do Windows (VM) do local para o Azure, você deve preparar o disco rígido virtual (VHD ou VHDX). O Azure suporta vms de geração 1 e 2 que estão em formato de arquivo VHD e que possuem um disco de tamanho fixo. O tamanho máximo permitido para o VHD é de 2 TB.

Em uma geração 1 VM, você pode converter um sistema de arquivos VHDX para VHD. Você também pode converter um disco em expansão dinâmica para um disco de tamanho fixo. No entanto, não é possível alterar a geração de uma VM. Para obter mais informações, [consulte Devo criar uma geração 1 ou 2 VM em Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) e suporte a [Azure para VMs de geração 2 (visualização)](generation-2.md).

Para obter informações sobre a política de suporte para VMs do Azure, consulte [o suporte ao software do servidor Microsoft para VMs Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> As instruções deste artigo aplicam-se a:
>1. A versão de 64 bits do Windows Server 2008 R2 e posteriormente sistemas operacionais Windows Server. Para obter informações sobre a execução de um sistema operacional de 32 bits no Azure, consulte [Suporte para sistemas operacionais de 32 bits em VMs Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Se qualquer ferramenta de recuperação de desastres for usada para migrar a carga de trabalho, como a Azure Site Recovery ou a Azure Migrate, esse processo ainda é necessário ser feito e seguido no Sistema Operacional convidado para preparar a imagem antes da migração.

## <a name="system-file-checker-sfc-command"></a>Comando SFC (System File Checker)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Executar o utilitário Verificador de arquivos do sistema Windows (executar sfc /scannow) no SISTEMA OPERACIONAL antes da etapa de generalização da criação da imagem do sistema operacional do cliente

O comando System File Checker (SFC) é usado para verificar e substituir arquivos do sistema Windows.

Para executar o comando SFC:

1. Abra um prompt de CMD elevado como administrador.
1. Digite `sfc /scannow` e selecione **Enter**.

    ![ Verificador de Arquivos do Sistema](media/prepare-for-upload-vhd-image/system-file-checker.png)


Depois que a varredura sfc for concluída, tente instalar o Windows Updates e reinicie o computador.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Converta o disco virtual em um tamanho fixo e em VHD

Se você precisar converter seu disco virtual para o formato necessário para o Azure, use um dos métodos nesta seção:

1. Faça backup do VM antes de executar o processo de conversão de disco virtual.

1. Certifique-se de que o Windows VHD funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

1. Quanto ao tamanho do VHD:

   1. Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1 MB antes da conversão. Frações de um megabyte causarão erros ao criar imagens do VHD carregado.

   2. O tamanho máximo permitido para o OS VHD é de 2TB.


Depois de converter o disco, crie uma VM que use o disco. Inicie e faça login na VM para terminar de prepará-lo para upload.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Use o Hyper-V Manager para converter o disco 
1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista do computador, selecione **Action** > **Edit Disk**.
2. Na página **Localizar disco rígido virtual,** selecione seu disco virtual.
3. Na página **Escolher ação,** **selecione Converter a** > **seguir**.
4. Se você precisar converter do VHDX, selecione **VHD** > **Next**.
5. Se você precisar converter a partir de um disco em expansão dinâmica, selecione **Tamanho fixo** > **Seguinte**.
6. Localize e selecione um caminho no qual salvar o novo arquivo VHD.
7. Selecione **Concluir**.

> [!NOTE]
> Use uma sessão powershell elevada para executar os comandos neste artigo.

### <a name="use-powershell-to-convert-the-disk"></a>Use o PowerShell para converter o disco 
Você pode converter um disco virtual usando o comando [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) no Windows PowerShell. Escolha **Executar como administrador** ao iniciar o PowerShell. 

O comando exemplo a seguir converte o disco de VHDX para VHD. O comando também converte o disco de um disco em expansão dinâmica para um disco de tamanho fixo.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Neste comando, substitua `-Path` o valor pelo caminho para o disco rígido virtual que você deseja converter. Substitua o `-DestinationPath` valor por pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem VM do Windows no [formato de arquivo VMDK,](https://en.wikipedia.org/wiki/VMDK)use o [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) para convertê-la em formato VHD. Para obter mais informações, consulte [Como converter um VMware VMDK para Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

> [!NOTE]
> A plataforma Azure monta um arquivo ISO para o DVD-ROM quando uma VM do Windows é criada a partir de uma imagem generalizada.
> Por essa razão, o DVD-ROM deve ser habilitado no sistema operacional na imagem generalizada. Se estiver desativado, o Windows VM ficará preso no OOBE.

Na VM que você planeja carregar para o Azure, execute os seguintes comandos a partir de uma [janela de solicitação](https://technet.microsoft.com/library/cc947813.aspx)de comando elevada :

1. Remova qualquer rota persistente estática na tabela de roteamento:
   
   * Para exibir a tabela de rotas, execute `route print` no prompt de comando.
   * Verifique `Persistence Routes` as seções. Se houver uma rota persistente, `route delete` use o comando para removê-la.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Se a VM precisar trabalhar com um proxy específico, adicione uma exceção proxy ao endereço IP do Azure[(168.63.129.16)](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)para que a VM possa se conectar ao Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Defina a diretiva [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)de SAN de disco como:
   
    ```PowerShell
    diskpart 
    ```
    Na janela de prompt de comando aberta, digite os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Defina o tempo universal coordenado (UTC) para o Windows. Também defina o tipo de`w32time`inicialização `Automatic`do serviço de tempo do Windows ( ) para :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Defina o perfil de potência como alto desempenho:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Certifique-se de `TEMP` que `TMP` as variáveis ambientais estão definidas como valores padrão:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Verificar os serviços Windows
Verifique se cada um dos seguintes serviços do Windows está definido como os valores padrão do Windows. Esses serviços são o mínimo que deve ser configurado para garantir a conectividade vm. Para redefinir as configurações de inicialização, execute os seguintes comandos:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Atualize as configurações do registro de desktop remoto
Certifique-se de que as seguintes configurações estão configuradas corretamente para acesso remoto:

>[!NOTE] 
>Você pode receber uma mensagem de erro quando for executado `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`. Ignore essa mensagem. Significa apenas que o domínio não está empurrando essa configuração através de um objeto de diretiva de grupo.

1. O protocolo RDP está habilitado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. A porta RDP está configurada corretamente. A porta padrão é 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Ao implantar uma VM, as regras padrão são criadas em relação à porta 3389. Se você quiser alterar o número da porta, faça isso depois que a VM for implantada no Azure.

3. O ouvinte escuta em todos os adaptadores de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Configure o modo nla (de autenticação em nível de rede) para as conexões RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Defina o valor de keep alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Reconectar-se:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Limite o número de conexões simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Remova quaisquer certificados auto-assinados vinculados ao ouvinte RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Este código garante que você pode se conectar no início quando você implantar a VM. Se você precisar revisar isso mais tarde, você pode fazê-lo depois que a VM for implantada no Azure.

9. Se a VM fizer parte de um domínio, verifique as seguintes políticas para garantir que as configurações anteriores não sejam revertidas. 
    
    | Goal                                     | Política                                                                                                                                                       | Valor                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP está habilitado                           | Configuração do Computador\Diretivas\Configurações do Windows\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Conexões         | Permitir que os usuários se conectem remotamente usando a Área de Trabalho Remota                                  |
    | Diretiva de grupo do NLA                         | Configurações\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Segurança                                                    | Exigir autenticação do usuário para acesso remoto usando NLA |
    | Configurações de manter-se vivo                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Configurar o intervalo de conexão keep-alive                                                 |
    | Configurações de reconexão                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Reconecte-se automaticamente                                                                   |
    | Número limitado de configurações de conexão | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Limitar o número de conexões                                                              |

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
1. Ativar o Firewall do Windows nos três perfis (domínio, padrão e público):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Execute o seguinte comando no PowerShell para permitir o WinRM através dos três perfis de firewall (domínio, privado e público) e habilite o serviço remoto PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Ative as seguintes regras de firewall para permitir o tráfego RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Habilite a regra para compartilhamento de arquivos e impressoras para que a VM possa responder a um comando ping dentro da rede virtual:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Crie uma regra para a rede de plataformas Azure:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Se a VM fizer parte de um domínio, verifique as seguintes políticas do Azure AD para garantir que as configurações anteriores não sejam revertidas. 

    | Goal                                 | Política                                                                                                                                                  | Valor                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Habilitar os perfis do Firewall do Windows | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Proteger todas as conexões de rede         |
    | Habilitar o RDP                           | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções de área de trabalho remota de entrada |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções de área de trabalho remota de entrada |
    | Habilitar o ICMP-V4                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções do ICMP                   |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções do ICMP                   |

## <a name="verify-the-vm"></a>Verificar a VM 

Certifique-se de que a VM está saudável, segura e acessível ao RDP: 

1. Para ter certeza de que o disco está saudável e consistente, verifique o disco na próxima reinicialização da VM:

    ```PowerShell
    Chkdsk /f
    ```
    Certifique-se de que o relatório mostra um disco limpo e saudável.

2. Defina as configurações de BCD (Dados de Configuração da Inicialização). 

    > [!NOTE]
    > Use uma janela PowerShell elevada para executar esses comandos.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. O registro de despejo pode ser útil na solução de problemas do Windows. Habilite a coleta de registros de despejo:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Verifique se o repositório de Instrumentação de Gerenciamento do Windows (WMI) é consistente:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório estiver corrompido, consulte [WMI: Corrupção de repositório ou não](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Certifique-se de que nenhum outro aplicativo está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Para ver quais portas são usadas `netstat -anob`na VM, execute:

    ```PowerShell
    netstat -anob
    ```

6. Para carregar um Windows VHD que é um controlador de domínio:

   * Siga [estas etapas extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

   * Certifique-se de conhecer a senha do Modo de Restauração de Serviços de Diretório (DSRM) caso você tenha que iniciar a VM no DSRM em algum momento. Para obter mais informações, consulte [Definir uma senha DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Certifique-se de conhecer a conta e a senha do administrador incorporado. Você pode querer redefinir a senha de administrador local atual e certificar-se de que você pode usar esta conta para fazer login no Windows através da conexão RDP. Essa permissão de acesso é controlada pelo objeto de diretiva de diretiva do grupo "Permitir logon through Remote Desktop Services". Veja este objeto no Editor de Políticas de Grupo Local aqui:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Verifique as seguintes políticas do Azure AD para ter certeza de que você não está bloqueando o acesso ao RDP através de RDP ou da rede:

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar acesso a este computador pela rede

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar logon pelos Serviços de Área de Trabalho Remota


9. Verifique a seguinte política do Azure AD para ter certeza de que você não está removendo nenhuma das contas de acesso necessárias:

   - Configuração do computador\Configurações do Windows\Configurações de segurança\Políticas locais\Atribuição de direitos do usuário\Acesse este computador a partir da rede

   A política deve listar os seguintes grupos:

   - Administradores

   - Operadores de Backup

   - Todos

   - Usuários

10. Reinicie a VM para ter certeza de que o Windows ainda está saudável e pode ser alcançado através da conexão RDP. Neste ponto, você pode querer criar uma VM em seu Hyper-V local para garantir que a VM comece completamente. Em seguida, teste para ter certeza de que você pode chegar ao VM através de RDP.

11. Remova quaisquer filtros extras de TDI (Transport Driver Interface, interface de motorista de transporte) extra. Por exemplo, remova o software que analisa pacotes TCP ou firewalls extras. Se você precisar revisar isso mais tarde, você pode fazê-lo depois que a VM for implantada no Azure.

12. Desinstale qualquer outro software ou driver de terceiros relacionado a componentes físicos ou qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
Idealmente, você deve manter a máquina atualizada no *nível de remendo*. Se isso não for possível, certifique-se de que as seguintes atualizações estejam instaladas. Para obter as últimas atualizações, consulte as páginas de histórico de atualizações do Windows: [Windows 10 e Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4009470) e [Windows 7 SP1 e Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| Componente               | Binário         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Armazenamento                 | disk.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17638/6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17188/6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17623/6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 – KB3125574                | 6.2.9200.16384 – KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17061/6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614         | 10.0.14393.953 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17047/6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726         | 10.0.14393.1066 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 – KB3125574                | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726         | 10.0.14393.1198 – KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614         | 10.0.14393.576 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614         | 10.0.14393.206 – KB4022715                              | -                          | -                                               | -                                               |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 – KB4022715                             | 10.0.15063.250 – KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726         | 10.0.14393.479 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 – KB4022722                | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 – KB4022722                | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726         | 10.0.14393.251 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 – KB4022719                | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Núcleo                    | ntoskrnl.exe   | 6.1.7601.23807 – KB4022719                | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Serviços da Área de Trabalho Remota | rdpcorets.dll  | 6.2.9200.21506 – KB4022719                | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 – KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 – KB4022719                | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726         | 10.0.14393.594 – KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Para evitar uma reinicialização acidental durante o provisionamento de VM, recomendamos garantir que todas as instalações do Windows Update estejam concluídas e que nenhuma atualização esteja pendente. Uma maneira de fazer isso é instalar todas as atualizações possíveis do Windows e reiniciar uma vez antes de executar o comando Sysprep.

### <a name="determine-when-to-use-sysprep"></a>Determine quando usar o Sysprep<a id="step23"></a>    

System Preparation Tool (Sysprep) é um processo que você pode executar para redefinir uma instalação do Windows. O Sysprep oferece uma experiência "fora da caixa" removendo todos os dados pessoais e redefinindo vários componentes. 

Você normalmente executa o Sysprep para criar um modelo a partir do qual você pode implantar várias outras VMs que têm uma configuração específica. O modelo é chamado de *imagem generalizada*.

Se você quiser criar apenas uma VM de um disco, você não precisa usar o Sysprep. Em vez disso, você pode criar a VM a partir de uma *imagem especializada*. Para obter informações sobre como criar uma VM a partir de um disco especializado, consulte:

- [Criar uma VM com base em um disco especializado](create-vm-specialized.md)
- [Criar uma VM com base em um disco VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se você quiser criar uma imagem generalizada, você precisa executar o Sysprep. Para obter mais informações, [consulte Como usar o Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx). 

Nem todas as partes ou aplicativos instalados em um computador baseado no Windows suportam imagens generalizadas. Então, antes de executar este procedimento, certifique-se de que o Sysprep suporta o papel do computador. Para obter mais informações, consulte [o suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalize um VHD

>[!NOTE]
> Depois de `sysprep.exe` executar as etapas seguintes, desligue a VM. Não ligue de novo até criar uma imagem dele no Azure.

1. Entre na VM Windows.
1. Execute o **Prompt de Comando** como administrador. 
1. Mude o diretório `%windir%\system32\sysprep`para . Em seguida, execute `sysprep.exe`.
1. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.

    ![Ferramenta de Preparação do Sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. Em **Opções de Desligamento**, selecione **Desligar**.
1. Selecione **OK**.
1. Quando sysprep terminar, desligue a VM. Não use **Restart** para desligar a VM.

Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM a partir de um disco generalizado, consulte [Carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](sa-upload-generalized.md).


>[!NOTE]
> Um arquivo *personalizado unattend.xml* não é suportado. Embora façamos `additionalUnattendContent` suporte à propriedade, que fornece apenas suporte limitado para adicionar opções [de configuração microsoft-windows-shell](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) no arquivo *unattend.xml* que o agente de provisionamento Do Azure usa. Você pode usar, por exemplo, [adicionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) para adicionar FirstLogonCommands e LogonCommands. Para obter mais informações, consulte [o exemplo adicionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Complete as configurações recomendadas
As seguintes configurações não afetam o upload do VHD. No entanto, é altamente recomendável que você as configure.

* Instale o [Azure Virtual Machine Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, você pode habilitar as extensões de VM. As extensões VM implementam a maior parte da funcionalidade crítica que você pode querer usar com suas VMs. Você precisará das extensões, por exemplo, para redefinir senhas ou configurar RDP. Para obter mais informações, consulte [a visão geral do Azure Virtual Machine Agent](../extensions/agent-windows.md).
* Depois de criar o VM no Azure, recomendamos que você coloque o arquivo de página no volume da *unidade temporal* para melhorar o desempenho. Você pode configurar a colocação do arquivo da seguinte forma:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Se um disco de dados estiver conectado à VM, a letra do volume da unidade temporal será tipicamente *D*. Essa designação pode ser diferente, dependendo das configurações e do número de unidades disponíveis.
  * Recomendamos desativar bloqueadores de script que possam ser fornecidos por software antivírus. Eles podem interferir e bloquear os scripts do Windows Provisioning Agent executados quando você implantar uma nova VM a partir de sua imagem.
  
## <a name="next-steps"></a>Próximas etapas
* [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](upload-generalized-managed.md)
* [Solucionar problemas de ativação do Azure Windows VM](troubleshoot-activation-problems.md)

