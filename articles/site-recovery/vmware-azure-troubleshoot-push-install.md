---
title: Solucionar problemas de instalação por push do serviço de mobilidade com o Azure Site Recovery f
description: Solucionar erros de instalação de serviços de mobilidade ao habilitar a replicação para recuperação de desastre com Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953786"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Solucionar problemas de instalação por push do serviço de mobilidade 

A instalação do serviço de mobilidade é uma etapa importante durante Habilitar Replicação. O sucesso dessa etapa depende exclusivamente dos pré-requisitos de reunião e trabalhe com as configurações com suporte. As falhas mais comuns enfrentadas durante a instalação do Serviço de Mobilidade são devido a:

* [Erros de credencial/privilégio](#credentials-check-errorid-95107--95108)
* [Falhas de logon](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de arquivo e de compartilhamento de impressora](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas de WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operacionais sem suporte](#unsupported-operating-systems)
* [Configurações de inicialização sem suporte](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas de instalação do VSS](#vss-installation-failures)
* [Nome do dispositivo na configuração do GRUB, em vez do UUID do dispositivo](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume LVM](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando você habilita a replicação, o Azure Site Recovery tenta enviar por push o agente de serviço de mobilidade em sua máquina virtual. Como parte desse processo, servidor de configuração tenta se conectar com a máquina virtual e copia o agente. Para habilitar a instalação com êxito, siga as diretrizes de solução de problemas passo a passo abaixo.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

* Verificar se a conta de usuário escolhida durante a habilitar a replicação está **válida, precisa**.
* O Azure Site Recovery exige a conta **RAIZ** ou a conta de usuário com **privilégios de administrador** para executar a instalação por push. Caso contrário, a instalação por push será bloqueada no computador de origem.
  * Para o Windows, (**erro 95107**), verifique se a conta de usuário tem acesso administrativo, local ou de domínio, no computador de origem.
  * Se você não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local.
    * Para desabilitar o controle de Acesso de Usuário Remoto, na chave do Registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione um novo DWORD: LocalAccountTokenFilterPolicy. Defina o valor para 1. Para executar essa etapa, execute o seguinte comando do prompt de comando:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Para o Linux (**erro 95108**), é necessário escolher a conta raiz para a instalação bem-sucedida do agente de mobilidade. Além disso, os serviços SFTP devem estar em execução. Para habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config:
    1. Entre como raiz.
    2. Vá para o arquivo /etc/ssh/sshd_config, localize a linha que começa com PasswordAuthentication.
    3. Remova a marca de comentário da linha e altere o valor para yes.
    4. Localize a linha que começa com Subsystem e remova a marca de comentário da linha.
    5. Reinicie o serviço sshd.

Se você quiser modificar as credenciais da conta de usuário escolhido, siga as instruções fornecidas [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficientes (errorID: 95517)

Quando o usuário escolhido para instalar o agente de mobilidade não tiver privilégios de administrador, o servidor de Configuração/servidor de processo de expansão não poderá copiar o software do agente de mobilidade no computador de origem. Portanto, esse erro é o resultado de uma falha de acesso negado. Verifique se a conta de usuário tem privilégios de administrador.

Se você quiser modificar as credenciais da conta de usuário escolhido, siga as instruções fornecidas [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficientes (errorID: 95518)

Quando o estabelecimento da relação de confiança de domínio entre o domínio primário e a estação de trabalho falha ao tentar entrar no computador de origem, a instalação do agente de mobilidade falha com a ID de erro 95518. Portanto, verifique se a conta de usuário usada para instalar o agente de mobilidade tem privilégios administrativos para entrar por meio do domínio primário do computador de origem.

Se você quiser modificar as credenciais da conta de usuário escolhido, siga as instruções fornecidas [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de logon (errorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de usuário foram desabilitadas (errorID: 95519)

A conta de usuário escolhida durante o trabalho Habilitar Replicação foi desabilitada. Para habilitar a conta de usuário, veja o artigo [aqui](https://aka.ms/enable_login_user) ou execute o comando a seguir, substituindo o texto *nome de usuário* pelo nome de usuário real.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas devido a várias tentativas de logon com falha (errorID: 95520)

Uma série de esforços de repetição com falha para acessar um computador bloqueará a conta de usuário. A falha pode ser devido a:

* As credenciais fornecidas durante a instalação de Configuração estão incorretas OU
* A conta de usuário escolhida durante o trabalho Habilitar Replicação está incorreta

Portando, modifique as credenciais escolhidas seguindo as instruções fornecidas [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e tente a operação novamente após algum tempo.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Os servidores de logon não estão disponíveis no computador de origem (errorID: 95521)

Esse erro ocorre quando os servidores de logon não estão disponíveis no computador de origem. A indisponibilidade dos servidores de logon levará à falha da solicitação de logon e, portanto, o agente de mobilidade não poderá ser instalado. Para fazer logon com êxito, verifique se os servidores de Logon estão disponíveis no computador de origem e inicie o serviço de Logon. Para obter instruções detalhadas, consulte a mensagem de erro KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) : não há servidores de logon disponíveis no momento.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de logon não está em execução no computador de origem (errorID: 95522)

O serviço de logon não está em execução no computador de origem e causou a falha da solicitação de logon. Portanto, o agente de mobilidade não pode ser instalado. Para resolver esse erro, verifique se o serviço de Logon está em execução no computador de origem para fazer logon com êxito. Para iniciar o serviço de logon, execute o comando "net start Logon" no prompt de comando ou inicie o serviço "NetLogon" no gerenciador de tarefas.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Falha de conectividade (errorID: 95117 & 97118)**

O servidor de Configuração/servidor de processo de expansão tenta se conectar à VM de origem para instalar o agente de Mobilidade. Esse erro ocorre quando o computador de origem não está acessível devido a problemas de conectividade de rede. Para resolver esse erro,

* Certifique-se de que você é capaz de executar o ping do servidor de configuração de sua máquina de origem. Se você tiver escolhido o servidor de processo de expansão durante a habilitação de replicação, verifique se que você é capaz de executar o ping de sua máquina de origem do servidor de processo.
  * Na linha de comando da máquina do Servidor de Origem, use o Telnet para executar ping no servidor de processo de expansão/servidor de configuração com porta https (135) conforme mostrado abaixo para ver se há algum problema de conectividade de rede ou bloqueio de portas de firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
* Além disso, para **máquina virtual Linux**,
  * Verifique se os últimos pacotes openssh, openssh-server e openssl estão instalados.
  * Verifique e garanta que o Secure Shell (SSH) esteja habilitado e em execução na porta 22.
  * Serviços SFTP devem estar em execução. Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config,
    * Entre como raiz.
    * Vá para o arquivo /etc/ssh/sshd_config, localize a linha que começa com PasswordAuthentication.
    * Remova a marca de comentário da linha e altere o valor para yes
    * Localize a linha que começa com Subsystem e remova a marca de comentário da linha
    * Reinicie o serviço sshd.
* Uma tentativa de conexão falha se não houver nenhuma resposta adequada após um período de tempo ou a conexão estabelecida falhou porque o host conectado não respondeu.
* Pode ser o domínio/rede/conectividade relacionados ao problema. Também é possível devido ao o nome DNS resolvendo o problema ou problema de esgotamento de porta TCP. Verifique se há algum problema conhecido no domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (errorID: 95523)

Esse erro ocorre quando a rede na qual reside o computador de origem não é encontrada, pode ter sido excluída ou não está mais disponível. A única maneira de resolver o erro é garantir a existência da rede.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação dos serviços de compartilhamento arquivos / impressoras (ErrorID: 95105 & 95106)

Após a verificação de conectividade, verifique se o arquivo e o serviço de compartilhamento de impressora está habilitado em sua máquina virtual. Essas configurações são necessárias para copiar o agente de Mobilidade no computador de origem.

Para **windows 2008 R2 e versões anteriores**,

* Para habilitar o compartilhamento de arquivo e impressão através do Firewall do Windows,
  * Abrir Painel de controle -> Sistema e segurança -> Firewall do Windows -> no painel esquerdo, clique em configurações Avançadas -> clique em regras de entrada na árvore de console.
  * Localize as regras de compartilhamento de arquivos e compartilhamento de impressora (NB-Sessão-entrada) e arquivo e impressora (SMB-entrada). Para cada regra, clique com botão direito na regra e, em seguida, clique em **Habilitar regra**.
* Habilitar compartilhamento de arquivo com a Política de Grupo,
  * Vá para iniciar, digite o gpmc.msc e pesquisar.
  * No painel de navegação, abra as seguintes pastas: política de computador Local, configuração do usuário, modelos administrativos, componentes do Windows e compartilhamento de rede.
  * No painel de detalhes, clique duas vezes **Impedir os usuários de compartilhamento de arquivos em seus perfis**. Para desabilitar a configuração de Política de Grupo, e habilitar a capacidade do usuário para compartilhar arquivos, clique em Desabilitado. Clique em OK para salvar as alterações. Para saber mais, consulte [habilitar ou desabilitar o compartilhamento de arquivos com o política de grupo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Para **versões posteriores**, siga as instruções fornecidas em [instalar o serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos](vmware-azure-install-mobility-service.md) para habilitar o compartilhamento de arquivos e impressoras.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração de Instrumentação de Gerenciamento do Windows (WMI) (código de erro: 95103)

Após a verificação dos serviços de arquivo e impressora, habilite o serviço WMI para perfis particulares, públicos e de domínio por meio do firewall. Essas configurações são necessárias para concluir a execução remota no computador de origem. Para habilitar isso,

* Acesse o Painel de Controle, clique em Segurança e, em seguida, em Firewall do Windows.
* Clique em Configurações de Segurança e clique na guia Alterar configurações.
* Na janela de exceções, marque a caixa de seleção para a Instrumentação de Gerenciamento do Windows (VMI) para habilitar o tráfego WMI através do firewall. 

Você também pode habilitar o tráfego WMI através do firewall no prompt de comando. Use o comando `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` a seguir.
Outros artigos de solução de problemas do WMI foi possível encontrar os artigos a seguir.

* [Teste de WMI básico](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Solução de problemas de WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Solucionar problemas com scripts de WMI e serviços de WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Sistema operacional sem suporte

Outro motivo mais comum para falha pode ser devido ao sistema operacional sem suporte. Verifique se que você estiver usando a versão do Kernel do sistema operacional com suporte para uma instalação bem-sucedida do serviço de mobilidade. Evite o uso de patch privado.
Para ver a lista dos sistemas operacionais e versões do kernel com suporte do Azure Site Recovery, consulte nosso [documento da matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de inicialização sem suporte (errorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Partições/volumes de inicialização e sistema não são o mesmo disco (errorID: 95309)

Antes da versão 9.20, partições/volumes de inicialização e do sistema localizados em discos diferentes eram uma configuração sem suporte. Começando na [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para essa configuração. Use a última versão para esse suporte.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>O disco de inicialização não está disponível (errorID: 95310)

Uma máquina virtual sem um disco de inicialização não pode ser protegida. Isso serve para garantir uma recuperação tranquila da máquina virtual durante a operação de failover. A ausência do disco de inicialização resulta em uma falha de inicialização do computador após o failover. Garanta que a máquina virtual contenha o disco de inicialização e repita a operação. Além disso, observe que não há suporte para mais de um disco de inicialização no mesmo computador.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos de inicialização presentes na máquina de origem (errorID: 95311)

Uma máquina virtual com vários discos de inicialização não é uma [configuração compatível](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (errorID: 95313)

Antes da versão 9.20, a partição ou o volume raiz disposto em vários discos era uma configuração sem suporte. Começando na [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para essa configuração. Use a última versão para esse suporte.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Falha ao habilitar a proteção, pois o nome do dispositivo mencionado na configuração de GRUB em vez de UUID (errorID: 95320)

**Possível causa:** </br>
Os arquivos de configuração do GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" ou "/etc/default/grub") podem conter o valor dos parâmetros **root** e **resume** como os nomes de dispositivo reais em vez do UUID. O Site Recovery exige a abordagem com UUID, pois o nome dos dispositivos poderá ser alterado quando ocorrer uma reinicialização da VM, uma vez que a VM poderá não ter o mesmo nome após um failover, causando problemas. Por exemplo: </br>


- A seguinte linha é do arquivo GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- A seguinte linha é do arquivo GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Se você observar a cadeia de caracteres em negrito acima, o GRUB terá nomes de dispositivo reais para os parâmetros "root" e "resume" em vez do UUID.
 
**Como corrigir:**<br>
os nomes de dispositivo devem ser substituídos pelo UUID correspondente.<br>


1. Localize o UUID do dispositivo executando o comando "blkid \<nome do dispositivo >". Por exemplo:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Agora, substitua o nome do dispositivo pelo UUID no formato "root = UUID =\<UUID >". Por exemplo, se substituímos os nomes de dispositivo com UUID para o parâmetro root e resume mencionado acima nos arquivos "/boot/Grub2/grub.cfg", "/boot/Grub2/grub.cfg" ou "/etc/default/grub: as linhas nos arquivos são semelhantes. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Reiniciar o trabalho de proteção novamente

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalação do serviço de mobilidade concluída com aviso para reinicialização (errorID: 95265 & 95266)

O serviço de mobilidade do Site Recovery tem muitos componentes, um dos quais é chamado driver de filtro. O driver de filtro é carregado na memória do sistema apenas no momento da reinicialização do sistema. Isso significa que as correções do driver de filtro só podem percebidas quando um novo driver de filtro é carregado, o que pode acontecer apenas no momento da reinicialização do sistema.

**Observe** que esse é um aviso e a replicação existente funcionará mesmo após a atualização do novo agente. Você pode optar por reinicializar sempre que desejar obter os benefícios do novo driver de filtro, mas se você não reinicializar o driver de filtro antigo continuar funcionando. Então, após uma atualização sem reinicialização, além do driver de filtro, **os benefícios de outros aprimoramentos e correções no serviço de mobilidade são concretizados**. Portanto, embora seja recomendado, não é obrigatório reinicializar após cada atualização. Para obter informações sobre quando uma reinicialização é obrigatória, defina a seção [reinicialização da máquina de origem após a atualização do agente de mobilidade](https://aka.ms/v2a_asr_reboot) em atualizações de serviço no Azure site Recovery.

> [!TIP]
>Para obter as melhores práticas de agendamento de atualizações durante a janela de manutenção, consulte o [suporte para versões mais recentes do sistema operacional/kernel](https://aka.ms/v2a_asr_upgrade_practice) em atualizações de serviço no Azure site Recovery.

## <a name="lvm-support-from-920-version"></a>Suporte do LVM começando na versão 9.20

Antes da versão 9.20, havia suporte para o LVM apenas em discos de dados. /boot deve estar em uma partição de disco e não ser um volume LVM.

Começando na [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para o [disco do sistema operacional no LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage). Use a última versão para esse suporte.

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (errorID: 95524)

Quando o agente de Mobilidade é copiado para o computador de origem, é necessário ter, pelo menos, 100 MB de espaço livre. Portanto, garanta que o computador de origem tenha o espaço livre necessário e tente a operação novamente.

## <a name="vss-installation-failures"></a>Falhas na instalação do VSS

A instalação do VSS faz parte da instalação do agente Mobility. Esse serviço é usado no processo de geração de pontos de recuperação consistentes do aplicativo. Falhas durante a instalação do VSS podem ocorrer devido a vários motivos. Para identificar os erros exatos, consulte **c: \ ProgramData \ ASRSetupLogs \ ASRUnifiedAgentInstaller.log**. Alguns erros comuns e as etapas de resolução são realçadas na seção a seguir.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS-2147023170 [0x800706BE] - código de saída 511

Esse problema é visto principalmente quando o software antivírus está bloqueando as operações dos serviços de Azure Site Recovery. Para resolver esse problema:

1. Exclua todas as pastas mencionadas [aqui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Siga as diretrizes publicadas pelo seu provedor de antivírus para desbloquear o registro da DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erro VSS 7 [0x7] - código de saída 511

Este é um erro de runtime e é causado devido a memória insuficiente para instalar o VSS. Certifique-se de aumentar o espaço em disco para conclusão bem-sucedida desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS-2147023824 [0x80070430] - código de saída 517

Esse erro ocorre quando o serviço do Provedor VSS do Azure Site Recovery é [marcado para exclusão](https://msdn.microsoft.com/library/ms838153.aspx). Tente instalar o VSS manualmente na máquina de origem executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS-2147023841 [0x8007041F] - código de saída 512

Esse erro ocorre quando o banco de dados de serviço do Provedor de VSS do Azure Site Recovery é [bloqueado](https://msdn.microsoft.com/library/ms833798.aspx).Tente instalar o VSS manualmente na máquina de origem executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Em caso de falha, verifique se algum programa antivírus ou outros serviços estão presos no estado "Iniciando". Isso pode reter o bloqueio nos serviços de banco de dados. Isso resultará em falhas na instalação do provedor VSS. Certifique-se de que nenhum serviço esteja em um estado de "início" e repita a operação acima.

### <a name="vss-exit-code-806"></a>Código de saída do VSS 806

Este erro ocorre quando a conta de usuário usada para instalação não tem permissões para executar o comando CSScript. Forneça as permissões necessárias à conta do usuário para executar o script e repita a operação.

### <a name="other-vss-errors"></a>Outros erros do VSS

Tente instalar o serviço do provedor VSS manualmente na máquina de origem executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Erro do VSS-0x8004E00F

Esse erro normalmente é encontrado durante a instalação do agente de mobilidade devido a problemas no DCOM e o DCOM está em um estado crítico.

Use o procedimento a seguir para determinar a causa do erro.

**Examinar os logs de instalação**

1. Abra o log de instalação localizado em c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. A presença do erro a seguir indica esse problema:

    Cancelando o registro do aplicativo existente...  Criar o objeto de catálogo obter a coleção de aplicativos 

    ERRO:

    - Código de erro:-2147164145 [0x8004E00F]
    - Código de saída: 802

Como resolver o problema:

Contate a [equipe da plataforma Microsoft Windows](https://aka.ms/Windows_Support) para obter assistência com a resolução do problema DCOM.

Quando o problema DCOM for resolvido, reinstale o provedor de Azure Site Recovery VSS manualmente usando o seguinte comando:
 
**C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent > "C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd**
  
Se a consistência do aplicativo não for crítica para seus requisitos de recuperação de desastre, você poderá ignorar a instalação do provedor do VSS. 

Para ignorar a instalação Azure Site Recovery do provedor VSS e instalar manualmente Azure Site Recovery pós-instalação do provedor VSS:

1. Instale o serviço de mobilidade. 
   > [!Note]
   > 
   > A instalação falhará na etapa ' pós-instalação configuração '. 
2. Para ignorar a instalação do VSS:
   1. Abra o diretório de instalação do serviço de mobilidade Azure Site Recovery localizado em:
   
      C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent
   2. Modifique os scripts de instalação do provedor de VSS Azure Site Recovery **nMageVSSProvider_Install** e **InMageVSSProvider_Uninstall. cmd** para sempre ter sucesso adicionando as seguintes linhas:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Execute a instalação do agente de mobilidade manualmente. 
4. Quando a instalação for bem sucedido e passar para a próxima etapa, **Configure**, remova as linhas que você adicionou.
5. Para instalar o provedor VSS, abra um prompt de comando como administrador e execute o seguinte comando:
   
    **C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent >. \ InMageVSSProvider_Install. cmd**

9. Verifique se o provedor VSS do ASR está instalado como um serviço nos serviços do Windows e abra o MMC do serviço de componente para verificar se o provedor VSS do ASR está listado.
10. Se a instalação do provedor VSS continuar a falhar, trabalhe com o CX para resolver os erros de permissões no CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Falha na instalação do provedor VSS porque o serviço de cluster está habilitado no computador não clusterizado

Esse problema faz com que a instalação do agente de mobilidade do Azure Site Recovery falhe durante a etapa de instalação do provedor VSS do Azure site Recoveryer devido a um problema com o COM+ que impede a instalação do provedor VSS.
 
### <a name="to-identify-the-issue"></a>Para identificar o problema

No log localizado no servidor de configuração em C:\ProgramData\ASRSetupLogs\UploadedLogs\<data-hora > UA_InstallLogFile. log, você encontrará a seguinte exceção:

COM+ não pôde se comunicar com o Coordenador de Transações Distribuídas da Microsoft (exceção de HRESULT: 0x8004E00F)

Como resolver o problema:

1.  Verifique se este computador é um computador não clusterizado e se os componentes do cluster não estão sendo usados.
3.  Se os componentes não estiverem sendo usados, remova os componentes do cluster do computador.

## <a name="drivers-are-missing-on-the-source-server"></a>Drivers ausentes no servidor de origem

Se a instalação do agente de mobilidade falhar, examine os logs em C:\ProgramData\ASRSetupLogs para determinar se alguns dos drivers necessários estão ausentes em alguns conjuntos de controle.
 
Como resolver o problema:
  
1. Usando um editor do registro, como regedit. msc, abra o registro.
2. Abra o nó HKEY_LOCAL_MACHINE \SYSTEM.
3. No nó sistema, localize os conjuntos de controles.
4. Abra cada conjunto de controle e verifique se os seguintes drivers do Windows estão presentes:

   - ATAPI
   - VMBus
   - Storflt
   - Storvsc
   - intelide
 
Reinstale os drivers ausentes.

## <a name="next-steps"></a>Próximas etapas

[Aprenda a](vmware-azure-tutorial.md) configurar uma recuperação após desastres para VMs VMware.
