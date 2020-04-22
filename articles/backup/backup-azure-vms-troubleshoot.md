---
title: Solucionando problemas de backup com VMs do Azure
description: Neste artigo, saiba como solucionar problemas encontrados com backup e restauração de máquinas virtuais Do Zure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 019c27b1f7e8560c86252aaf2ed1fb79df2439fa
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677351"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Solução de problemas falhas de backup em máquinas virtuais do Azure

Você pode solucionar problemas encontrados ao usar o Azure Backup com as informações listadas abaixo:

## <a name="backup"></a>Backup

Esta seção cobre a falha de operação de backup da máquina Virtual Do Azure.

### <a name="basic-troubleshooting"></a>Solução básica de problemas

* Certifique-se de que o Agente VM (WA Agent) é a [versão mais recente](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Certifique-se de que a versão do Sistema Operacional VM do Windows ou Linux seja suportada, consulte a [matriz de suporte de backup do VM IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Verifique se outro serviço de backup não está sendo executado.
  * Para garantir que não há problemas de extensão de snapshot, [desinstale extensões para forçar a recarga e, em seguida, tente novamente o backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout).
* Verifique se a VM tem conectividade com a internet.
  * Certifique-se de que outro serviço de backup não está sendo executado.
* De, `Services.msc`certifique-se de que o serviço **de agente convidado do Windows Azure** está sendo **executado**. Se o serviço do Agente Convidado do **Windows Azure** estiver ausente, instale-o nas [VMs do Azure em um cofre de Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* O **registro de eventos** pode mostrar falhas de backup que são de outros produtos de backup, por exemplo, backup do Windows Server e não são devidos ao backup do Azure. Use as seguintes etapas para determinar se o problema está com o Backup do Azure:
  * Se houver um erro com uma entrada **Backup** na origem do evento ou mensagem, verifique se os backups de backup do Azure IaaS VM foram bem sucedidos e se um Ponto de Restauração foi criado com o tipo de instantâneo desejado.
  * Se o Azure Backup estiver funcionando, então o problema provavelmente será com outra solução de backup.
  * Aqui está um exemplo de um erro de visualização de eventos 517 onde o backup do Azure estava funcionando bem, mas o "Backup do Servidor do Windows" estava falhando:<br>
    ![Falha no backup do Windows Server](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Se o backup do Azure estiver falhando, procure o Código de erro correspondente na seção Erros comuns de backup da VM neste artigo.

## <a name="common-issues"></a>Problemas comuns

A seguir, problemas comuns com falhas de backup em máquinas virtuais do Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopiandoVHDsFromBackUpUpVaultTakingLongTime - Cópia de dados de backup do cofre cronometrados

Código de erro: CopiandoVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: Cópia de dados de backup do cofre cronometrado

Isso pode acontecer devido a erros transitórios de armazenamento ou iOPS de conta de armazenamento insuficiente para serviço de backup para transferir dados para o cofre dentro do período de tempo. Configure o backup da VM usando essas [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e tente novamente a operação de backup.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInUserState - VM não está em um estado que permite backups

Código de erro: UserErrorVmNotInUserState <br/>
Mensagem de erro: VM não está em um estado que permite backups.<br/>

A operação de backup falhou porque a VM está em estado de falha. Para ter um backup bem-sucedido, o estado da VM deve ser em execução, parado ou interrompido (desalocado).

* Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup.
* Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Falha ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos

Código de erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Falha ao congelar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de arquivos.

* Desmontar os dispositivos para os quais o estado do sistema de arquivos não foi limpo, usando o comando **umount.**
* Execute uma verificação de consistência do sistema de arquivos nesses dispositivos usando o comando **fsck.**
* Monte os dispositivos novamente e tente novamente a operação de backup.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensãoSnapshotFailedCOM / ExtensãoInstalaçãoFalhaCOM / ExtensãoInstalaçãoFalhaMDTC - Falha na instalação/operação de extensão devido a um erro COM+

Código de erro: ExtensionSnapshotFailedCOM <br/>
Mensagem de erro: falha na operação de snapshot devido ao erro COM+

Código de erro: ExtensionInstallationFailedCOM  <br/>
Mensagem de erro: Falha na instalação/operação de extensão devido a um erro COM+

Código de erro: ExtensãoInstalaçãoFalhaMDTC <br/>
Mensagem de erro: Falha na instalação de extensão com o erro "O COM+ não conseguiu falar com o Coordenador de Transações Distribuídas da Microsoft <br/>

A operação backup falhou devido a um problema com o aplicativo **do sistema Com+** do serviço Windows.  Para resolver esse problema, siga estas etapas:

* Tente iniciar/reiniciar o aplicativo do **sistema Com+** (a partir de um prompt de comando elevado **- net start COMSysApp**).
* Certifique-se de que o serviço **de coordenador de transações distribuídas** esteja sendo executado como conta **de Serviço de Rede.** Caso assim, altere-o para ser executado como conta **do Serviço de Rede** e reinicie o aplicativo do sistema **COM+.**
* Se não conseguir reiniciar o serviço, reinstale o serviço **coordenador de transações distribuídas** seguindo as etapas abaixo:
  * Interrompa o serviço MSDTC
  * Abra um prompt de comando (cmd)
  * Executar o comando "msdtc-desinstalar"
  * Executar o comando "msdtc -install"
  * Inicie o serviço MSDTC
* Inicie o serviço Windows **COM+ System Application**. Depois que o **Aplicativo do Sistema COM+** for iniciado, dispare um trabalho de backup no portal do Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Operação de snapshot falhou porque os escritores VSS estavam em um estado ruim

Código de erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: A operação de instantâneo falhou porque os escritores VSS estavam em um estado ruim.

Reinicie os gravadores VSS que estão em um estado inválido. Em um prompt de comandos com privilégios elevados, execute ```vssadmin list writers```. A saída contém todos os gravadores VSS e seus estados. Para cada gravador VSS com um estado que não seja **[1] Estável**, para reiniciar o gravador VSS, execute os seguintes comandos em um prompt de comando elevado:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Falha na análise da configuração para a extensão de backup

Código de erro: ExtensionConParsingFailure<br/>
Mensagem de erro: Falha na análise da configuração da extensão de backup.

Esse erro acontece por causa das permissões alteradas no diretório **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Execute o seguinte comando e verifique se as permissões no diretório **MachineKeys** são padrão:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

As permissões padrão são:

* Todos: (R,W)
* BUILTIN\Administradores: (F)

Se você vir as permissões no diretório **MachineKeys** que são diferentes dos padrões, siga estas etapas para corrigir as permissões, exclua o certificado e dispare o backup:

1. Corrija as permissões no diretório **MachineKeys**. Usando as Propriedades de Segurança do Explorer e as Configurações Avançadas de Segurança no diretório, redefina as permissões de volta para os valores padrão. Remova todos os objetos de usuário (exceto padrão) do diretório e assegure-se de que a permissão **Todos** tenha acesso especial para:

   * Listar pastas / ler dados
   * Atributos de leitura
   * Atributos de leitura estendidos
   * Criar arquivos/gravar dados
   * Criar pastas/anexar dados
   * Atributos de gravação
   * Atributos de gravação estendidos
   * Permissões de leitura
2. Exclua todos os certificados onde **o Emitido para** é o modelo de implantação clássico ou o gerador de **certificados CRP do Windows Azure:**

   * [Abra os certificados em um console de computador local](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Em**Certificados** **Pessoais,** > exclua todos os certificados onde **O Emitido é** o modelo clássico de implantação ou o Gerador de **Certificados CRP do Windows Azure**.
3. Dispare um trabalho de backup da VM.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - O estado de extensão não é favorável à operação de backup

Código de erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: O estado de extensão não é favorável à operação de backup

A operação backup falhou devido ao estado inconsistente de extensão de backup. Para resolver esse problema, siga estas etapas:

* Verifique se o Agente Convidado está instalado e respondendo
* A partir do portal Azure, vá para **Virtual Machine** > **All Settings** > **Extensions**
* Selecione a extensão de backup VmSnapshot ou VmSnapshotLinux e clique em **Desinstalar**
* Depois de excluir a extensão de backup, tente novamente a operação de backup
* A operação de backup subsequente instalará a nova extensão no estado desejado

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensãoFailedSnapshotLimitReachedError - Falha na operação de snapshot, pois o limite de snapshot é excedido para alguns dos discos anexados

Código de erro: ExtensãoFalhadeimagemLimiteLimiteErroError <br/>
Mensagem de erro: falha na operação de snapshot, pois o limite de snapshot é excedido para alguns dos discos anexados

A operação de instantâneo falhou porque o limite de instantâneo excedeu para alguns dos discos conectados. Complete as etapas de solução de problemas abaixo e, em seguida, tente novamente a operação.

* Exclua os blob-snapshots de disco que não são necessários. Tenha cuidado para não excluir blob de disco, apenas blobs de snapshot devem ser excluídos.
* Se o soft-delete estiver ativado em Contas de armazenamento em disco VM, configure a retenção de exclusão suave de modo que os instantâneos existentes sejam menores do que o máximo permitido em qualquer momento.
* Se a recuperação do site do Azure estiver ativada na VM de backup, execute as etapas abaixo:

  * Certifique-se de que o valor de **isanysnapshotfailed** é definido como falso em /etc/azure/vmbackup.conf
  * Agende a recuperação do site do Azure em um momento diferente, de modo que não conflite a operação de backup.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensãoFailedTimeoutVMNetworkUnresponsive - Falha na operação de snapshot devido a recursos inadequados de VM

Código de erro: ExtensionFailedTimeVMNetworkUnresponsive<br/>
Mensagem de erro: Falha na operação de snapshot devido a recursos inadequados da VM.

A operação de backup na VM falhou devido ao atraso nas chamadas de rede durante a execução da operação de snapshot. Para resolver esse problema, siga a Etapa 1. Se o problema persistir, tente as etapas 2 e 3.

**Passo 1**: Criar instantâneo através do Host

Em um prompt de comando com privilégios elevados (administrador), execute o comando abaixo:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Isso garantirá que os instantâneos são executados por meio do host em vez do convidado. Tente a operação de backup novamente.

**Passo 2**: Tente alterar o cronograma de backup para um momento em que a VM está sob menos carga (menos CPU/IOps etc.)

**Passo 3**: Tente [aumentar o tamanho da VM](https://azure.microsoft.com/blog/resize-virtual-machines/) e tentar novamente a operação

## <a name="common-vm-backup-errors"></a>Erros comuns de backup da VM

| Detalhes do erro | Solução alternativa |
| ------ | --- |
| **Código de erro**: 320001, ResourceNotFound <br/> **Mensagem de erro**: Não foi possível realizar a operação, pois a VM não existe mais. <br/> <br/> **Código de erro:** 400094, BCMV2VMNotFound <br/> **Mensagem de erro**: A máquina virtual não existe <br/> <br/>  Uma máquina virtual do Azure não foi encontrada.  |Esse erro ocorre quando a VM primária é excluída, mas a política de backup ainda parece para uma VM fazer backup. Para corrigir esse erro, use as etapas a seguir: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço em nuvem,**<br>**Ou**</li><li> Pare a proteção da máquina virtual excluindo ou não os dados de backup. Para obter mais informações, consulte [Interromper a proteção de máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Código de erro:** UserErrorBCMPremiumStorageQuotaError<br/> **Mensagem de erro**: Não foi possível copiar o instantâneo da máquina virtual, devido ao espaço livre insuficiente na conta de armazenamento | No caso de VMs premium na pilha de backup de VM V1, copiamos o instantâneo para a conta de armazenamento. Essa etapa garante que o tráfego de gerenciamento de backup, que funciona no instantâneo, não limite o número de IOPS disponível para o aplicativo usando discos premium. <br><br>É recomendável que você aloque apenas 50 por cento, 17,5 TB, do espaço de conta de armazenamento total. Então, o serviço de Backup do Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados desse local copiado na conta de armazenamento para o cofre. |
| **Código de erro**: 380008, AzureVmOffline <br/> **Mensagem de erro**: Falha ao instalar a extensão do Microsoft Recovery Services, pois a máquina virtual não está em execução | O agente de VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro. <br> <ol> <li>Verifique se o agente da VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM esteja definido corretamente.</ol> Leia mais sobre como instalar o agente da VM e como validar a instalação do agente da VM. |
| **Código de erro**: ExtensionSnapshotBitlockerError <br/> **Mensagem de erro**: A operação de snapshot falhou com o erro de operação do Volume Shadow Copy Service (VSS) **Esta unidade está bloqueada pela Criptografia da unidade BitLocker. Você deve desbloquear esta unidade a partir do Painel de Controle.** |Desative o BitLocker para todas as unidades na VM e verifique se o problema VSS é resolvido. |
| **Código de erro**: VmNotInDesejávelEstado <br/> **Mensagem de erro**: A VM não está em um estado que permite backups. |<ul><li>Se a VM estiver em um estado transitório entre **Execução** e **Desligada**, aguarde a alteração do estado para mudar. Em seguida, dispare o trabalho de backup. <li> Se a VM for uma VM do Linux e usar o módulo de kernel do Linux com Segurança Aprimorada, exclua o caminho do Agente para Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.  |
| O agente de VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito necessário e o agente de VM. Em seguida, reinicie a operação. |Leia mais sobre [Instalação do Agente da VM e como validar a Instalação do Agente da VM](#vm-agent). |
| **Código de erro:** ExtensãoSnapshotFailedNoSecureNetwork <br/> **Mensagem de erro**: A operação de instantâneo falhou devido à falha na criação de um canal de comunicação de rede seguro. | <ol><li> Abra o Editor do Registro executando **regedit.exe** no modo elevado. <li> Identificar todas as versões do .NET Framework presente no seu sistema. Eles estão presentes na hierarquia de chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada .NET Framework presente na chave do registro, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Código de erro**: ExtensionVCRedistInstallationFailure <br/> **Mensagem de erro**: A operação de instantâneo falhou devido à falha na instalação do Visual C++ Redistributable para o Visual Studio 2012. | Navegue até C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersão e instale vcredist2013_x64.<br/>Certifique-se de que o valor-chave do registro que permite a instalação do serviço esteja definido como o valor correto. Ou seja, defina o valor **Iniciar** em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** para **3** e não **4**. <br><br>Se você ainda estiver enfrentando problemas com a instalação, reinicie o serviço de instalação executando **MSIEXEC /UNREGISTER** seguido de **MSIEXEC /REGISTER** em um prompt de comandos com privilégios elevados.  |
| **Código de erro**: UserErrorRequestDsallowedByPolicy <BR> **Mensagem de erro**: Uma diretiva inválida está configurada na VM que está impedindo a operação snapshot. | Se você tiver uma Diretiva Azure que [rege as tags dentro](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)do seu ambiente, considere alterar a política de um efeito [Negar](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) para um [efeito Modificar,](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)ou crie o grupo de recursos manualmente de acordo com o [esquema de nomeação exigido pelo Azure Backup](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Trabalhos

| Detalhes do erro | Solução alternativa |
| --- | --- |
| Não há suporte para cancelamento para este tipo de trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |
| O trabalho não está em um estado cancelável: <br>Aguarde até que o trabalho seja concluído. <br>**Ou**<br> O trabalho selecionado não está em um estado cancelável: <br>Aguarde a conclusão do trabalho. |É provável que o trabalho esteja quase terminando. Aguarde até o trabalho ser concluído.|
| Backup não pode cancelar o trabalho porque ele não está em andamento: <br>O cancelamento tem suporte somente para trabalhos em andamento. Tente cancelar um trabalho em andamento. |Esse erro ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| Falha de backup para cancelar o trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar

| Detalhes do erro | Solução alternativa |
| --- | --- |
| A restauração falhou com erro interno de nuvem. |<ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Se **Endereço** estiver configurado, as configurações de DNS são configuradas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com **ReservedIP** e as VMs existentes no serviço de nuvem estão no estado parado. Você pode verificar que um serviço de nuvem tem um IP reservado usando os seguintes cmdlets do PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem: <ul><li>Máquinas virtuais sob configuração do balanceador de carga, interno e externo.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com várias NICs. </ul><li>Selecione um novo serviço de nuvem na interface do usuário ou veja [Considerações de restauração](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já está em uso: <br>Especifique um nome DNS diferente e tente novamente. |O nome DNS aqui se refere ao nome do serviço de nuvem, geralmente terminando em **.cloudapp.net**. Esse nome precisa ser exclusivo. Se você obter esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br>  Este erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell obtém êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| A configuração da rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está usando um IP reservado que não corresponde à configuração da máquina virtual que está sendo restaurada: <br>Especifique um serviço de nuvem diferente que não está usando um IP reservado. Ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| O serviço de nuvem atingiu seu limite no número de pontos de extremidade de entrada: <br>Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| O cofre dos Serviços de Recuperação e a conta de armazenamento de destino estão em duas regiões diferentes: <br>Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure como seu cofre do Recovery Services. |Nenhum |
| Não há suporte para a conta de armazenamento especificada para a operação de restauração: <br>Há suporte para apenas para contas Basic ou Standard com configurações de replicação geográfica redundante localmente. Selecione uma conta de armazenamento com suporte. |Nenhum |
| O tipo de conta de armazenamento especificada para a operação de restauração não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauração está online. |Esse erro pode ocorrer devido a um erro transitório no Armazenamento do Microsoft Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| A cota do grupo de recursos foi atingida: <br>Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede que existe. |Nenhum |
| O serviço de Backup não tem autorização para acessar recursos em sua assinatura. |Para resolver esse erro, primeiro restaure discos, usando as etapas em [Restaurar discos com backup](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, use as etapas do PowerShell em [Criar uma VM de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Backup ou restauração demoram

Se o backup levar mais de 12 horas ou restaurar leva mais de 6 horas, revise [as práticas recomendadas](backup-azure-vms-introduction.md#best-practices)e [as considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente de VM

### <a name="set-up-the-vm-agent"></a>Configuração do agente de VM

Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente.

#### <a name="windows-vms"></a>VMs Windows

* Baixe e instale o [agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador para concluir a instalação.
* Para máquinas virtuais criadas usando o modelo de implantação clássico, [atualizar a propriedade de VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) para indicar que o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Azure Resource Manager.

#### <a name="linux-vms"></a>VMs Linux

* Instale a versão mais recente do agente do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte o [repositório do Agente Linux](https://github.com/Azure/WALinuxAgent).
* Para VMs criados usando o modelo clássico de implantação, [atualize a propriedade vm](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) e verifique se o agente está instalado. Essa etapa não é necessária para máquinas virtuais do Resource Manager.

### <a name="update-the-vm-agent"></a>Atualizar o agente de VM

#### <a name="windows-vms"></a>VMs Windows

* Para atualizar o Agente da VM, reinstale os [Binários do agente da VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não ocorram operações de backup durante a atualização do Agente da VM.

#### <a name="linux-vms"></a>VMs Linux

* Para atualizar o Agente VM Linux, siga as instruções no artigo [Atualizando o Agente Linux VM](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Sempre use o repositório de distribuição para atualizar o agente.

    Não baixe o código do agente do GitHub. Se o agente mais recente não estiver disponível para a sua distribuição, entre em contato com o suporte de distribuição para obter instruções para adquirir o agente mais recente. Também é possível verificar as informações mais recentes do [Agente do Linux do Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do agente de VM

Verifique a versão do agente da VM em VMs do Windows:

1. Entre na máquina virtual do Azure e navegue até a pasta **C:\WindowsAzure\Packages**. Você deve encontrar o arquivo **WaAppAgent.exe**.
2. Clique com o botão direito do mouse no arquivo e acesse **Propriedades**. Em seguida, selecione a guia **Detalhes.** O **campo Versão do Produto** deve ser 2.6.1198.718 ou superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar Problemas de Instantâneo de VM

O backup de VM depende da emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos na execução da tarefa do instantâneo, pode resultar na falha do trabalho de backup. As condições a seguir podem causar a falha da tarefa do instantâneo:

* **VMs com backup do SQL Server configurado pode causar atraso na tarefa de snapshot**. Por padrão, o backup da VM cria um backup completo do VSS em VMs do Windows. As VMs que executam o SQL Server, com backup do SQL Server configurado, podem sofrer atrasos de instantâneos. Se os atrasos de instantâneos causarem falhas de backup, defina a seguinte chave do registro:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **O status da VM é informado incorretamente porque a VM está desligada em RDP**. Se você usou a área de trabalho remota para desligar a máquina virtual, verifique se o status da VM no portal está correto. Se o status não estiver correto, use a opção **Desligar** no painel da VM do portal para desligar a VM.
* **Se mais de quatro VMs compartilharem o mesmo serviço em nuvem, espalhe as VMs em várias políticas de backup**. Escalone os horários do backup para que não tenha mais de quatro backups de VM iniciados ao mesmo tempo. Tente separar as horas de início nas políticas por pelo menos uma hora.
* **A VM é executada no alto da CPU ou memória**. Se a máquina virtual é executada em alta utilização de memória ou uso da CPU, mais de 90 por cento, a tarefa de instantâneo será enfieirada e postergada. Eventualmente, ele se adiaà. Se esse problema acontecer, tente um backup sob demanda.

## <a name="networking"></a>Rede

O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione. Se você precisar de um endereço IP privado estático, configure-o usando a plataforma do portal do Microsoft Azure ou o PowerShell. Verifique se a opção DHCP na VM está habilitada.
Obtenha mais informações sobre como configurar um endereço IP estático por meio do PowerShell:

* [Como adicionar um IP interno estático a uma VM existente](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
