---
title: Serviço de mobilidade de solução de problemas empurra instalação com recuperação do site do Azure
description: Solucionar problemas de instalação dos Serviços de Mobilidade ao habilitar a replicação para recuperação de desastres com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656319"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Instalação de impulso de serviço de mobilidade para solução de problemas

A instalação do serviço mobility é um passo fundamental para permitir a replicação. O sucesso desta etapa depende do atendimento aos pré-requisitos e do trabalho com configurações suportadas. As falhas mais comuns que você pode enfrentar durante a instalação do serviço de mobilidade devem-se a:

* [Erros de credencial/privilégio](#credentials-check-errorid-95107--95108)
* [Falhas de logon](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de arquivo e de compartilhamento de impressora](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas na Instrumentação de Gerenciamento de Janelas (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operacionais sem suporte](#unsupported-operating-systems)
* [Configurações de inicialização sem suporte](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas de instalação do Volume Shadow Copy Service (VSS)](#vss-installation-failures)
* [Nome do dispositivo na configuração do GRUB, em vez do UUID do dispositivo](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume do Gerenciador de VolumeLógico (LVM)](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando você habilita a replicação, o Azure Site Recovery tenta instalar o agente de serviço de mobilidade em sua máquina virtual (VM). Como parte desse processo, o servidor de configuração tenta se conectar com a máquina virtual e copiar o agente. Para habilitar a instalação bem-sucedida, siga a orientação passo-a-passo para solucionar problemas.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

Verifique se a conta de usuário escolhida durante a replicação enable é válida e precisa. O Azure Site Recovery requer a conta **raiz** ou a conta de usuário com **privilégios de administrador** para executar uma instalação push. Caso contrário, a instalação de push será bloqueada na máquina de origem.

Para windows **(erro 95107),** verifique se a conta de usuário tem acesso administrativo no computador de origem, com uma conta local ou conta de domínio. Se você não estiver usando uma conta de domínio, você precisa desativar o controle de acesso remoto ao usuário no computador local.

* Para adicionar manualmente uma chave de registro que desativa o controle de acesso remoto do usuário:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Adicionar um `DWORD`novo :`LocalAccountTokenFilterPolicy`
  * Defina o valor para`1`

* Para adicionar a chave de registro, a partir de um prompt de comando, execute o seguinte comando:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Para Linux **(erro 95108),** você deve escolher a conta **raiz** para a instalação bem-sucedida do agente de serviço mobility. Além disso, os serviços sftp (File transfer protocol, protocolo de transferência de arquivos ssh) devem estar em execução. Para habilitar a autenticação do subsistema SFTP e senha no arquivo _sshd_config:_

1. Faça login como **raiz**.
1. Vá para _/etc/ssh/sshd_config arquivo,_ encontre `PasswordAuthentication`a linha que começa com .
1. Descomente a linha e `yes`mude o valor para .
1. Encontre a linha `Subsystem`que começa com , e descomente a linha.
1. Reinicie o serviço `sshd` .

Se desejar modificar as credenciais da conta de usuário escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficientes (ErroID: 95517)

Quando o usuário escolhido para instalar o agente Mobility não tiver privilégios de administrador, o servidor de configuração/servidor de processo de saída de escala não será autorizado a copiar o software do agente de mobilidade para a máquina de origem. Este erro é resultado de uma falha negada de acesso. Verifique se a conta de usuário tem privilégios de administrador.

Se desejar modificar as credenciais da conta de usuário escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficientes (ErroID: 95518)

Quando o estabelecimento de relacionamento de confiança de domínio entre o domínio principal e a estação de trabalho falha ao tentar entrar na máquina de origem, a instalação do agente de mobilidade falha com o ID de erro 95518. Certifique-se de que a conta de usuário usada para instalar o agente de mobilidade tenha privilégios administrativos para fazer login através do domínio primário da máquina de origem.

Se desejar modificar as credenciais da conta de usuário escolhida, siga as [instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de login (ErroID: 95519, 95520, 95521, 95522)

Esta seção descreve mensagens de erro de credencial e login.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de usuário foram desativadas (ErrorID: 95519)

A conta de usuário escolhida durante a replicação enable foi desativada. Para habilitar a conta de usuário, consulte [este artigo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) ou execute o seguinte comando, substituindo o nome _de usuário_ do texto pelo nome de usuário real.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas por causa de várias tentativas de login com falha (ErrorID: 95520)

Uma série de esforços de repetição com falha para acessar um computador bloqueará a conta de usuário. A falha pode ser devido a:

* As credenciais fornecidas durante a configuração estão incorretas.
* A conta de usuário escolhida durante a replicação de habilitação está errada.

Modifique as credenciais escolhidas seguindo [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e tente novamente a operação.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Os servidores logon não estão disponíveis na máquina de origem (ErrorID: 95521)

Esse erro ocorre quando os servidores de logon não estão disponíveis na máquina de origem. Se os servidores de logon não estiverem disponíveis, as solicitações de login falharão e o agente de mobilidade não poderá ser instalado. Para obter um login bem-sucedido, certifique-se de que os servidores de logon estejam disponíveis na máquina de origem e inicie o serviço Netlogon. Para obter mais informações, consulte [Cenários de Logon do Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de logon não está sendo executado na máquina de origem (ErrorID: 95522)

O serviço de login não está sendo executado na sua máquina de origem e causou falha na solicitação de login. O agente de mobilidade não pode ser instalado. Para resolver o erro, use um dos `Netlogon` seguintes métodos para iniciar o serviço na máquina de origem:

* Para iniciar `Netlogon` o serviço a partir `net start Netlogon`de um prompt de comando, execute o comando .
* Do Gerenciador de `Netlogon` Tarefas, inicie o serviço.

## <a name="connectivity-failure-errorid-95117--97118"></a>Falha de conectividade (ErroID: 95117 & 97118)

O servidor de processo de configuração do servidor/escala-out tenta conectar-se à VM de origem para instalar o agente Mobility. Esse erro ocorre quando a máquina de origem não pode ser alcançável porque há problemas de conectividade de rede.

Para resolver o erro:

* Certifique-se de que você pode pingar sua máquina de origem a partir do servidor de configuração. Se você escolheu o servidor de processo de saída de escala durante a replicação de habilitação, certifique-se de que você pode pingar sua máquina de origem a partir do servidor de processo.

* A partir da linha de `Telnet` comando da máquina do servidor de origem, use para pingar o servidor de configuração ou servidor de processo de escala na porta HTTPS 135, conforme mostrado no comando a seguir. Este comando verifica se há algum problema de conectividade de rede ou problemas de bloqueio da porta de firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Além disso, para um VM Linux:
  * Verifique se os pacotes OpenSSH, OpenSSH Server e OpenSSL mais recentes estão instalados.
  * Verifique e garanta que o Secure Shell (SSH) esteja habilitado e em execução na porta 22.
  * Serviços SFTP devem estar em execução. Para habilitar a autenticação do subsistema e senha do SFTP no arquivo _sshd_config:_

    1. Faça login como **raiz**.
    1. Vá para _/etc/ssh/sshd_config_ arquivo, encontre `PasswordAuthentication`a linha que começa com .
    1. Descomente a linha e `yes`mude o valor para .
    1. Encontre a linha `Subsystem`que começa com , e descomente a linha
    1. Reinicie o serviço `sshd` .

* Uma tentativa de conexão poderia ter falhado se não houvesse respostas adequadas após um período de tempo, ou uma conexão estabelecida falhou porque um host conectado falhou em responder.
* Pode ser um problema relacionado à conectividade/rede/domínio. Também pode ser porque o nome DNS resolve problema de resolução ou problema de exaustão da porta TCP. Verifique se há algum problema conhecido no domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (ErroID: 95523)

Esse erro ocorre quando a rede que a máquina de origem reside não é encontrada, pode ter sido excluída ou não está mais disponível. A única maneira de resolver o erro é garantir que a rede exista.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação dos serviços de compartilhamento arquivos / impressoras (ErrorID: 95105 & 95106)

Após uma verificação de conectividade, verifique se o serviço de compartilhamento de arquivos e impressoras está habilitado em sua máquina virtual. Essas configurações são necessárias para copiar o agente de mobilidade para a máquina de origem.

Para **Windows 2008 R2 e versões anteriores**:

* Para habilitar o compartilhamento de arquivo e impressão através do Firewall do Windows,
  1. Sistema **de painel de** > controle aberto e**firewall do Windows de****segurança** > . No painel esquerdo, selecione **Regras avançadas** > **de entrada** na árvore do console.
  1. Localize as regras de compartilhamento de arquivos e compartilhamento de impressora (NB-Sessão-entrada) e arquivo e impressora (SMB-entrada).
  1. Para cada regra, clique com botão direito na regra e, em seguida, clique em **Habilitar regra**.

* Para habilitar o compartilhamento de arquivos com a Diretiva de Grupo:
  1. Vá para **Iniciar,** digite `gpmc.msc` e pesquise.
  1. No painel de navegação, abra as seguintes pastas:**Configuração** > do usuário **da política** > de computador local**Modelos administrativos** > de compartilhamento de**rede do****Windows Components** > .
  1. No painel de detalhes, clique duas vezes **Impedir os usuários de compartilhamento de arquivos em seus perfis**.

     Para desativar a configuração de Diretiva de grupo e ativar a capacidade do usuário de compartilhar arquivos, **selecione Disabled**.

  1. Selecione **OK** para salvar suas alterações.

  Para saber mais, consulte [Ativar ou desativar o compartilhamento de arquivos com a diretiva de grupo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Para habilitar o compartilhamento de arquivos e impressoras para versões posteriores do Windows ou Linux, siga as instruções em [Instalar o serviço de mobilidade para recuperação de desastres de VMs e servidores físicos vMware](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração do Windows Management Instrumentation (WMI) (Código de erro: 95103)

Após a verificação dos serviços de arquivo e impressora, habilite o serviço WMI para perfis privados, públicos e de domínio através do firewall. Essas configurações são necessárias para concluir a execução remota no computador de origem.

Para habilitar o WMI:

1. Vá para A Segurança **do Painel de** > **Controle** e selecione Firewall **do Windows**.
1. Selecione **Configurações de alteração** e selecione a guia **Exceções.**
1. Na janela **Exceções,** selecione a caixa de seleção para O WMI (Windows Management Instrumentation, instrumentação de gerenciamento do Windows) para permitir o tráfego WMI através do firewall.

Você também pode habilitar o tráfego WMI através do firewall a partir do prompt de comando com o seguinte comando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Outros artigos de solução de problemas do WMI foi possível encontrar os artigos a seguir.

* [Teste de WMI básico](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Solução de problemas de WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Solucionar problemas com scripts de WMI e serviços de WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemas operacionais sem suporte

Outra razão comum para a falha pode ser por causa de um sistema operacional sem suporte. Use um sistema operacional suportado e uma versão do kernel para uma instalação bem-sucedida do serviço Mobility. Evite o uso de patches privados.

Para visualizar a lista de sistemas operacionais e versões do kernel suportadas pelo Azure Site Recovery, consulte o [documento da matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de inicialização sem suporte (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Inicialização e partições do sistema / volumes não são o mesmo disco (ErrorID: 95309)

Antes da versão 9.20, as partições/volumes do sistema em diferentes discos eram uma configuração sem suporte. Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>O disco de inicialização não está disponível (ErrorID: 95310)

Uma máquina virtual sem um disco de inicialização não pode ser protegida. Um disco de inicialização garante uma recuperação suave de uma máquina virtual durante uma operação de failover. A ausência de um disco de inicialização resulta em uma falha na inicialização da máquina após o failover. Certifique-se de que a máquina virtual contém um disco de inicialização e tente novamente a operação. Além disso, vários discos de inicialização na mesma máquina não são suportados.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos de inicialização presentes na máquina de origem (ErrorID: 95311)

Uma máquina virtual com vários discos de inicialização não é uma [configuração suportada](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (ErrorID: 95313)

Antes da versão 9.20, uma partição raiz ou configuração de volume em vários discos era uma configuração sem suporte. Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Habilitar a proteção falha como nome do dispositivo mencionado na configuração GRUB em vez de UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Causa possível

Os arquivos de configuração do Grand Unified Bootloader (GRUB)_(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub)_ podem conter o valor para os parâmetros **raiz** e **retomar** como os nomes reais do dispositivo em vez de um identificador universalmente único (UUID). A Recuperação do Site determina a abordagem UUID, pois os nomes dos dispositivos podem mudar através da reinicialização da VM. Por exemplo, a VM pode não ficar online com o mesmo nome no failover e isso resulta em problemas.

Por exemplo:

- A linha a seguir é do arquivo GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A linha a seguir é do arquivo GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> As linhas GRUB contêm nomes reais de dispositivos para os parâmetros **raiz** e **currículo** em vez do UUID.

### <a name="how-to-fix"></a>Como corrigir

os nomes de dispositivo devem ser substituídos pelo UUID correspondente.

1. Encontre o UUID do dispositivo executando `blkid \<device name>`o comando .

   Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Agora substitua o nome do dispositivo pelo `root=UUID=\<UUID>`seu UUID no formato como . Por exemplo, se substituirmos os nomes dos dispositivos por UUID por parâmetro raiz e currículo mencionados nos arquivos _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub,_ então as linhas nos arquivos parecem a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Reinicie a proteção.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalar serviço de mobilidade concluído com aviso para reinicialização (ErrorID: 95265 & 95266)

O serviço de mobilidade de recuperação do site tem muitos componentes, um dos quais é chamado de driver de filtro. O driver do filtro é carregado na memória do sistema somente durante uma reinicialização do sistema. As correções do driver de filtro só podem ser realizadas quando um novo driver de filtro é carregado no momento de uma reinicialização do sistema.

> [!IMPORTANT]
> Este é um aviso e a replicação existente funcionará mesmo após a nova atualização do agente. Você pode optar por reiniciar quando quiser obter os benefícios do novo driver de filtro, mas se você não reiniciar, o driver de filtro antigo continua funcionando. Assim, depois de uma atualização sem reinicialização, exceto para o driver de filtro, **os benefícios de outras melhorias e correções no serviço de Mobilidade são realizados**. Embora recomendado, não é obrigatório reiniciar após cada atualização. Para obter informações sobre quando uma reinicialização é obrigatória, defina a seção [Deinicialização após atualização do serviço de mobilidade](service-updates-how-to.md#reboot-after-mobility-service-upgrade) em atualizações de serviço no Azure Site Recovery.

> [!TIP]
>Para obter as melhores práticas sobre o agendamento de atualizações durante a janela de manutenção, consulte o suporte para atualizações [mais recentes do sistema operacional/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) no Serviço de recuperação do site.

## <a name="lvm-support-from-920-version"></a>Suporte do LVM começando na versão 9.20

Antes da versão 9.20, o Logical Volume Manager (LVM) era suportado apenas para discos de dados. A `/boot` partição deve estar em uma partição de disco e não em um volume LVM.

Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)o [disco do Sistema Operacional em LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) é suportado.

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (ErroID: 95524)

Quando o agente de mobilidade é copiado para a máquina de origem, é necessário pelo menos 100 MB de espaço livre. Certifique-se de que a máquina de origem tenha a quantidade necessária de espaço livre e tente novamente a operação.

## <a name="vss-installation-failures"></a>Falhas na instalação do VSS

A instalação do Volume Shadow copy Service (VSS) faz parte da instalação do agente Mobility. Este serviço é usado no processo para gerar pontos de recuperação consistentes de aplicativos. Falhas durante a instalação do VSS podem ocorrer devido a vários motivos. Para identificar os erros exatos, consulte _C:\ProgramData\ASRConfiguraçãoLogs\ASRUnifiedAgentInstaller.log_. Alguns dos erros comuns e as etapas de resolução são destacados na seção a seguir.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS-2147023170 [0x800706BE] - código de saída 511

Esse problema é mais frequentemente visto quando o software antivírus está bloqueando as operações dos serviços de recuperação do Site do Azure.

Para resolver o problema:

1. Revise a lista de [exclusões de pastas do programa Antivirus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Siga as diretrizes publicadas pelo seu provedor de antivírus para desbloquear o registro de DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erro VSS 7 [0x7] - código de saída 511

Este erro é um erro de tempo de execução causado porque não há memória suficiente para instalar VSS. Aumente o espaço em disco para a conclusão bem sucedida desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS-2147023824 [0x80070430] - código de saída 517

Esse erro ocorre quando o serviço do Provedor VSS do Azure Site Recovery é [marcado para exclusão](/previous-versions/ms838153(v=msdn.10)). Tente instalar vss manualmente na máquina de origem executando o seguinte comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS-2147023841 [0x8007041F] - código de saída 512

Esse erro ocorre quando o banco de dados de serviço saqueado do Provedor VSS de recuperação do site do Azure está [bloqueado](/previous-versions/ms833798(v=msdn.10)). Tente instalar vss manualmente na máquina de origem executando o seguinte comando a partir de um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando houver uma falha, verifique se algum programa antivírus ou outros serviços estão presos em um estado **inicial.** Um processo em um estado **inicial** pode reter o bloqueio nos serviços de banco de dados. Isso levará a falhas na instalação do provedor VSS. Certifique-se de que nenhum serviço está em um estado **inicial** e, em seguida, tente novamente a operação acima.

### <a name="vss-exit-code-806"></a>Código de saída do VSS 806

Esse erro ocorre quando a conta de usuário usada para `CSScript` instalação não tem permissões para executar o comando. Forneça as permissões necessárias à conta do usuário para executar o script e repita a operação.

### <a name="other-vss-errors"></a>Outros erros do VSS

Tente instalar o serviço do provedor VSS manualmente na máquina de origem executando o seguinte comando a partir de um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Erro VSS - 0x8004E00F

Esse erro normalmente ocorre durante a instalação do `DCOM` agente `DCOM` de mobilidade devido a problemas e está em estado crítico.

Use o seguinte procedimento para determinar a causa do erro.

### <a name="examine-the-installation-logs"></a>Examine os registros de instalação

1. Abra o registro de instalação localizado em _C:\ProgramData\ASRConfiguraçãoLogs\ASRUnifiedAgentInstaller.log_.
2. A presença do seguinte erro indica este problema:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Como resolver o problema:

Entre em contato com a equipe da [plataforma Microsoft Windows](https://aka.ms/Windows_Support) para obter assistência para resolver o problema do DCOM.

Quando o problema do DCOM for resolvido, reinstale o Provedor VSS de recuperação do site do Azure usando manualmente o seguinte comando a partir de um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se a consistência do aplicativo não for crítica para os requisitos de recuperação de desastres, você pode ignorar a instalação do Provedor VSS.

Para contornar a instalação do Provedor VSS de recuperação do site do Azure e instalar manualmente a instalação pós-instalação do Provedor de Recuperação do Site do Azure:

1. Instalar o serviço de Mobilidade. A instalação falhará na etapa: **Configuração pós-instalação**.
1. Para contornar a instalação do VSS:
   1. Abra o diretório de instalação do Azure Site Recovery Mobility Service localizado em:

      _C:\Arquivos de programa (x86)\Microsoft Azure Site Recovery\agent_

   1. Modifique os scripts de instalação do Provedor VSS de recuperação do site do Azure _InMageVSSProvider_Install_ e _InMageVSSProvider_Uninstall.cmd_ para sempre ter sucesso adicionando as seguintes linhas:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Faça uma instalação manual do Agente de Mobilidade.
1. Quando a instalação for bem-sucedida e for para a próxima etapa, **Configure,** remova as linhas adicionadas.
1. Para instalar o provedor VSS, abra um prompt de comando como administrador e execute o seguinte comando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verifique se o Provedor VSS de Recuperação do Site do Azure está instalado como um serviço no Windows Services. Abra o MMC do serviço de componentes para confirmar se o provedor VSS está listado.
1. Se a instalação do Provedor VSS continuar a falhar, trabalhe com suporte técnico para resolver os erros de permissões na Interface de Programação de Aplicativos Criptográficos (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A instalação do provedor VSS falha porque o serviço de cluster está sendo ativado em máquina sem cluster

Esse problema faz com que a instalação do Azure Site Recovery Mobility Agent falhe durante a instalação do Provedor VSS de Recuperação do Site do Azure. A falha é porque há `COM+` um problema com o que impede a instalação do provedor VSS.

### <a name="to-identify-the-issue"></a>Para identificar o problema

No log localizado no servidor de configuração em _C:\ProgramData\ASRConfiguraçãoConfigurações\UploadedLogs\<data-hora>UA_InstallLogFile.log_ você encontrará a seguinte exceção:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Como resolver o problema:

1. Verifique se esta máquina é uma máquina sem cluster e que os componentes do cluster não estão sendo usados.
1. Se os componentes não forem usados, remova os componentes do cluster da máquina.

## <a name="drivers-are-missing-on-the-source-server"></a>Os drivers estão faltando no servidor de origem

Se a instalação do Agente de Mobilidade falhar, examine os logs em _C:\ProgramData\ASRConfigurações_ para determinar se alguns dos drivers necessários estão faltando em alguns conjuntos de controle.

Como resolver o problema:

1. Usando um editor `regedit.msc`de registro, como, abra o registro.
1. Abra `HKEY_LOCAL_MACHINE\SYSTEM` o nó.
1. No `SYSTEM` nó, localize os conjuntos de controle.
1. Abra cada conjunto de controles e verifique se os seguintes drivers do Windows estão presentes:

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. Reinstale os drivers desaparecidos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](vmware-azure-tutorial.md) sobre como configurar a recuperação de desastres para VMware VMware.
