---
title: Solucionar problemas do windows virtual desktop host - Azure
description: Como resolver problemas quando você está configurando máquinas virtuais de host de sessão de desktop virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127592"
---
# <a name="session-host-virtual-machine-configuration"></a>Configuração da máquina virtual do host da sessão

Use este artigo para solucionar problemas que você está tendo ao configurar as Máquinas virtuais de hospedagem de desktop virtual do Windows (VMs).

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="vms-are-not-joined-to-the-domain"></a>As VMs não são unidas ao domínio

Siga estas instruções se você estiver tendo problemas para juntar VMs ao domínio.

- Junte-se à VM manualmente usando o processo em [Junte uma máquina virtual do Windows Server a um domínio gerenciado](../active-directory-domain-services/join-windows-vm.md) ou usando o modelo de [adesão de domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente pingar o nome de domínio da linha de comando na VM.
- Revise a lista de mensagens de erro de adesão de domínio em ['Problemas', adote mensagens de erro](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)'

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Causa:** Houve um erro de digitação feito quando as credenciais foram inseridas nas correções de interface do Azure Resource Manager.

**Correção:** Tome uma das seguintes ações para resolver.

- Adicione manualmente as VMs a um domínio.
- Reimplante o modelo assim que as credenciais forem confirmadas. Consulte [Criar um pool de host com o PowerShell](create-host-pools-powershell.md).
- Junte As VMs a um domínio usando um modelo com [a junta uma VM do Windows existente ao domínio AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Tempo de espera para entrada do usuário

**Causa:** A conta usada para completar a adesão ao domínio pode ter autenticação multifatorial (MFA).

**Correção:** Tome uma das seguintes ações para resolver.

- Remova temporariamente o MFA para a conta.
- Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta usada durante o provisionamento não tem permissões para concluir a operação

**Causa:** A conta que está sendo usada não tem permissões para aderir às VMs no domínio devido à conformidade e regulamentos.

**Correção:** Tome uma das seguintes ações para resolver.

- Use uma conta que seja membro do grupo Administrador.
- Conceda as permissões necessárias à conta que está sendo usada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: o nome do domínio não resolve

**Causa 1:** As VMs estão em uma rede virtual que não está associada à rede virtual (VNET) onde o domínio está localizado.

**Correção 1:** Crie o peering VNET entre o VNET onde as VMs foram provisionadas e o VNET onde o controlador de domínio (DC) está sendo executado. Consulte [Criar uma rede virtual peering - Gerenciador de recursos, diferentes assinaturas](../virtual-network/create-peering-different-subscriptions.md).

**Causa 2:** Ao usar o Azure Active Directory Domain Services (Azure AD DS), a rede virtual não tem as configurações do servidor DNS atualizadas para apontar para os controladores de domínio gerenciados.

**Correção 2:** Para atualizar as configurações de DNS para a rede virtual que contém o Azure AD DS, consulte [Atualizar as configurações de DNS para a rede virtual Azure](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Causa 3:** As configurações do servidor DNS da interface de rede não apontam para o servidor DNS apropriado na rede virtual.

**Correção 3:** Tome uma das seguintes ações para resolver, seguindo as etapas em [Alterar servidores DNS].
- Altere as configurações do servidor DNS da interface de rede para **Personalizado** com as etapas dos [servidores Change DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) e especifique os endereços IP privados dos servidores DNS na rede virtual.
- Altere as configurações do servidor DNS da interface de rede para **Herdar da rede virtual** com as etapas dos [servidores Change DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers)e altere as configurações do servidor DNS da rede virtual com as etapas dos [servidores Change DNS](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>O Windows Virtual Desktop Agent e o Windows Virtual Desktop Boot Loader não estão instalados

A maneira recomendada de provisionar VMs é usando o Azure Resource Manager Create e provisionar o modelo **de pool de host do Windows Virtual Desktop.** O modelo instala automaticamente o Windows Virtual Desktop Agent e o Windows Virtual Desktop Agent Boot Loader.

Siga estas instruções para confirmar se os componentes estão instalados e verificar se há mensagens de erro.

1. Confirme se os dois componentes estão instalados verificando em **Programas** > **e Recursos**do Painel**de** > Controle . Se **o Windows Virtual Desktop Agent** e o Windows Virtual Desktop Agent Boot **Loader** não estiverem visíveis, eles não serão instalados na VM.
2. Abra **o Explorador de Arquivos** e navegue até **C:\Windows\Temp\ScriptLog.log**. Se o arquivo estiver ausente, ele indica que o PowerShell DSC que instalou os dois componentes não foi capaz de ser executado no contexto de segurança fornecido.
3. Se o arquivo **C:\Windows\Temp\ScriptLog.log** estiver presente, abra-o e verifique se há mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Erro: O Windows Virtual Desktop Agent e o Windows Virtual Desktop Agent Boot Loader estão faltando. C:\Windows\Temp\ScriptLog.log também está faltando

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo do Azure Resource Manager estavam incorretas ou as permissões eram insuficientes.

**Correção 1:** Adicione manualmente os componentes ausentes às VMs usando [Criar um pool de host com powershell](create-host-pools-powershell.md).

**Causa 2:** O PowerShell DSC foi capaz de iniciar e executar, mas não conseguiu concluir, pois não pode entrar no Windows Virtual Desktop e obter informações necessárias.

**Correção 2:** Confirme os itens na lista a seguir.

- Certifique-se de que a conta não tenha MFA.
- Confirme se o nome do inquilino é preciso e o inquilino existe no Windows Virtual Desktop.
- Confirme se a conta tem pelo menos permissões de contribuinte RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Falha de erro: falha de autenticação, erro em C:\Windows\Temp\ScriptLog.log

**Causa:** O PowerShell DSC foi capaz de executar, mas não conseguiu se conectar ao Windows Virtual Desktop.

**Correção:** Confirme os itens na lista a seguir.

- Registre manualmente as VMs com o serviço de desktop virtual do Windows.
- Confirmar a conta usada para se conectar ao Windows Virtual Desktop tem permissões no inquilino para criar pools de host.
- Confirmar conta não tem MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>O Windows Virtual Desktop Agent não está se registrando com o serviço de desktop virtual do Windows

Quando o Windows Virtual Desktop Agent é instalado pela primeira vez em VMs host de sessão (manualmente ou através do modelo Do Gerenciador de Recursos do Azure e do PowerShell DSC), ele fornece um token de registro. A seção a seguir abrange problemas de solução de problemas aplicáveis ao Agente de Área de Trabalho Virtual do Windows e ao token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O status arquivado no Get-RdsSessionO cmdlet mostra o status como Indisponível

![Get-RdsSessionO cmdlet mostra o status como Indisponível.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se atualizar para uma nova versão.

**Correção:** Siga estas instruções para atualizar manualmente o agente.

1. Baixe uma nova versão do agente no VM do host de sessão.
2. Inicie o Gerenciador de Tarefas e, na guia de serviço, interrompa o serviço RDAgentBootLoader.
3. Execute o instalador para a nova versão do Windows Virtual Desktop Agent.
4. Quando solicitado para o token de registro, remova a entrada INVALID_TOKEN e pressione em seguida (um novo token não é necessário).
5. Complete a instalação do Assistente.
6. Abra o Gerenciador de Tarefas e inicie o serviço RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro: Entrada de registro do Agente de Desktop Virtual do Windows O Registro mostra um valor de 0

**Causa:** O token de registro expirou ou foi gerado com o valor de expiração de 999999.

**Correção:** Siga estas instruções para corrigir o erro do registro do agente.

1. Se já houver um token de registro, remova-o com Remove-RDSRegistrationInfo.
2. Gere um novo token com rds-NewRegistrationInfo.
3. Confirme se o parâmetro -ExpriationHours está definido como 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: o agente de área de trabalho virtual do Windows não está relatando batimentos cardíacos ao executar get-rdsSessionHost

**Causa 1:** O serviço RDAgentBootLoader foi interrompido.

**Correção 1:** Inicie o Gerenciador de Tarefas e, se a Guia de Serviço relatar um status parado para o serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** O porto 443 pode estar fechado.

**Correção 2:** Siga estas instruções para abrir a porta 443.

1. Confirme que a porta 443 está aberta baixando a ferramenta PSPing das [ferramentas Sysinternal](/sysinternals/downloads/psping/).
2. Instale o PSPing na VM do host de sessão onde o agente está sendo executado.
3. Abra o prompt de comando como administrador e emita o comando abaixo:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme se o PSPing recebeu informações do RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problemas de solução de problemas com a pilha lado a lado do Windows Virtual Desktop

A pilha lado a lado do Windows Virtual Desktop é instalada automaticamente com o Windows Server 2019. Use o Microsoft Installer (MSI) para instalar a pilha lado a lado no Microsoft Windows Server 2016 ou no Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha lado a lado do Windows Virtual Desktop é ativada com **habilitadoresxstackrs.ps1**.

Existem três maneiras principais de a pilha lado a lado ser instalada ou ativada em VMs do pool de host de sessão:

- Com o Azure Resource Manager Criar e provisionar o novo modelo **de pool de host seleto do Windows Virtual Desktop**
- Ao ser incluído e habilitado na imagem principal
- Instalado ou ativado manualmente em cada VM (ou com extensões/PowerShell)

Se você estiver tendo problemas com a pilha lado a lado do Windows Virtual Desktop, digite o comando **qwinsta** do prompt de comando para confirmar se a pilha lado a lado está instalada ou ativada.

A saída de **qwinsta** listará **rdp-sxs** na saída se a pilha lado a lado estiver instalada e ativada.

![Pilha lado a lado instalada ou habilitada com qwinsta listada como rdp-sxs na saída.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas de registro listadas abaixo e confirme se seus valores correspondem. Se as chaves de registro estiverem faltando ou os valores forem incompatíveis, siga as instruções em [Criar um pool de host com o PowerShell](create-host-pools-powershell.md) sobre como reinstalar a pilha lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE código de erro.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha lado a lado não está instalada na VM do host de sessão.

**Correção:** Siga estas instruções para instalar a pilha lado a lado na VM do host de sessão.

1. Use rdp (Remote Desktop Protocol, protocolo de desktop remoto) para entrar diretamente na VM do host de sessão como administrador local.
2. Baixe e importe [O módulo PowerShell da área de trabalho virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usar na sessão PowerShell se você ainda não tiver, execute este cmdlet para fazer login na sua conta:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Instale a pilha lado a lado usando [Criar um pool de host com o PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha lado a lado do Windows Virtual Desktop que funciona mal

Existem circunstâncias conhecidas que podem causar o mau funcionamento da pilha lado a lado:

- Não seguindo a ordem correta das etapas para ativar a pilha lado a lado
- Atualização automática para o Disco Versátil Aprimorado do Windows 10 (EVD)
- Faltando a função RDSH (Remote Desktop Session Host, host de sessão de desktop remota)
- Executando enablesxsstackrc.ps1 várias vezes
- Executando habilitaxsstackrc.ps1 em uma conta que não tem privilégios de administração local

As instruções nesta seção podem ajudá-lo a desinstalar a pilha lado a lado do Windows Virtual Desktop. Depois de desinstalar a pilha lado a lado, vá para "Registrar a VM com o pool de hosts do Windows Virtual Desktop" em [Criar um pool de host com o PowerShell](create-host-pools-powershell.md) para reinstalar a pilha lado a lado.

O VM usado para executar a remediação deve estar na mesma sub-rede e domínio que a VM com a pilha lado a lado com defeito.

Siga estas instruções para executar a remediação a partir da mesma sub-rede e domínio:

1. Conecte-se com o RDP (Remote Desktop Protocol, protocolo remoto padrão de desktop) à VM de onde a correção será aplicada.
2. Baixe PsExec https://docs.microsoft.com/sysinternals/downloads/psexecde .
3. Descompacte o arquivo baixado.
4. Inicie o prompt de comando como administrador local.
5. Navegue até a pasta onde o PsExec foi descompactado.
6. A partir do prompt de comando, use o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname é o nome da máquina da VM com a pilha lado a lado defeituosa.

7. Aceite o Contrato de Licença psexec clicando em Concordar.

    ![Captura de tela de contrato de licença de software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Esta caixa de diálogo aparecerá apenas na primeira vez que o PsExec for executado.

8. Depois que a sessão de prompt de comando for aberta na VM com a pilha lado a lado defeituosa, execute qwinsta e confirme se uma entrada chamada rdp-sxs está disponível. Se não, uma pilha lado a lado não está presente no VM para que o problema não esteja ligado à pilha lado a lado.

    ![Solicitação de comando do administrador](media/AdministratorCommandPrompt.png)

9. Execute o seguinte comando, que listará os componentes da Microsoft instalados na VM com a pilha lado a lado com defeito.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com nomes de produtos da etapa acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Remote Desktop".

12. Depois que todos os componentes do Windows Virtual Desktop tiverem sido desinstalados, siga as instruções do seu sistema operacional:

13. Se o sistema operacional for o Windows Server, reinicie a VM que tinha a pilha lado a lado com defeito (seja com o portal Azure ou com a ferramenta PsExec).

Se o seu sistema operacional for o Microsoft Windows 10, continue com as instruções abaixo:

14. Desde o VM executando psExec, abra o File Explorer e copie disablessstackrc.ps1 para a unidade do sistema da VM com a pilha lado a lado defeituosa.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome da máquina da VM com a pilha lado a lado defeituosa.

15. O processo recomendado: a partir da ferramenta PsExec, inicie o PowerShell e navegue até a pasta a partir da etapa anterior e execute disablesxsstackrc.ps1. Alternativamente, você pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets terminarem de funcionar, reinicie a VM com a pilha lado a lado defeituosa.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>O modo de licenciamento remoto da área de trabalho não está configurado

Se você fizer login no Windows 10 Enterprise várias sessões usando uma conta administrativa, você poderá receber uma notificação que diz: "O modo de licenciamento da área de trabalho remota não está configurado, os Serviços de Desktop Remoto pararão de funcionar em x dias. No servidor Connection Broker, use o Server Manager para especificar o modo de licenciamento da Área de Trabalho Remota."

Se o prazo expirar, aparecerá uma mensagem de erro que diz: "A sessão remota foi desconectada porque não há licenças de acesso ao cliente do Remote Desktop disponíveis para este computador."

Se você ver qualquer uma dessas mensagens, isso significa que a imagem não tem as últimas atualizações do Windows instaladas ou que você está configurando o modo de licenciamento da Área de Trabalho Remota através da política de grupo. Siga as etapas nas próximas seções para verificar a configuração da diretiva de grupo, identifique a versão da multisessão do Windows 10 Enterprise e instale a atualização correspondente.  

>[!NOTE]
>O Windows Virtual Desktop só requer uma licença de acesso ao cliente RDS (CAL) quando seu pool de hosts contém hosts de sessão do Windows Server. Para saber como configurar um CAL RDS, consulte [Licenciar sua implantação RDS com licenças de acesso ao cliente](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Desativar a configuração de diretiva de modo de licenciamento da área de trabalho remota

Verifique a configuração da diretiva de grupo abrindo o Editor de Políticas de Grupo na VM e navegando para **modelos administrativos** > **que o Windows Components** > Remote Desktop Services > **Remote Desktop****Session Host** > **Licensing** > **Set the Remote Desktop licensing mode**. Se a configuração de diretiva de grupo estiver **habilitada,** altere-a para **Desativada**. Se já está desativado, então deixe-o como está.

>[!NOTE]
>Se você definir a política de grupo através do seu domínio, desative essa configuração nas políticas que visam essas VMs multi-sessão do Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identifique qual versão do Windows 10 Enterprise multi-sessão você está usando

Para verificar qual versão do Windows 10 Enterprise multi-sessão você tem:

1. Faça login com sua conta de administração.
2. Digite "Sobre" na barra de pesquisa ao lado do menu Iniciar.
3. Selecione **Sobre o seu PC**.
4. Verifique o número ao lado de "Versão". O número deve ser "1809" ou "1903", como mostrado na imagem a seguir.

    ![Uma captura de tela da janela de especificações do Windows. O número da versão é destacado em azul.](media/windows-specifications.png)

Agora que você sabe o número da sua versão, pule para a seção relevante.

### <a name="version-1809"></a>Versão 1809

Se o número da sua versão diz "1809", instale [a atualização KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versão 1903

Reimplante o sistema operacional host com a versão mais recente do Windows 10, versão 1903 da Galeria Azure.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para solucionar problemas ao criar um inquilino e um pool de host em um ambiente de Desktop Virtual do Windows, consulte [Criação de inquilino e pool de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de clientes do Windows Virtual Desktop, consulte [conexões de serviço do Windows Virtual Desktop](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes de desktop remoto, consulte [Solucionar problemas do cliente de desktop remoto](troubleshoot-client.md)
- Para solucionar problemas ao usar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o [ambiente Windows Virtual Desktop](environment-setup.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
