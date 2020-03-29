---
title: Solucionar problemas de Servidor de Backup do Azure
description: Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho do aplicativo.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: cc0cf7e91c1aacbc637d33ab1e5546cc54836b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673126"
---
# <a name="troubleshoot-azure-backup-server"></a>Solucionar problemas de Servidor de Backup do Azure

Use as informações nas tabelas a seguir para solucionar problemas de erros encontrados durante o uso do Servidor de Backup do Azure.

## <a name="basic-troubleshooting"></a>Solução básica de problemas

Recomendamos que você execute a validação abaixo, antes de começar a solucionar problemas do Microsoft Azure Backup Server (MABS):

- [Certifique-se de que o Agente mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que há conectividade de rede entre o agente MARS e o Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (no console de Serviço). Se necessário, reinicie e tente novamente a operação
- [Certifique-se de que há de 5 a 10% de espaço de volume livre disponível no local da pasta temporária](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se o registro estiver falhando, certifique-se de que o servidor no qual você está tentando instalar o Azure Backup Server ainda não está registrado em outro cofre
- Se a instalação por push falhar, verifique se o agente de DPM já existe. Se sim, desinstale o agente e tente instalar novamente
- [Verifique se algum outro processo ou software antivírus está interferindo no Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Verificar se o serviço do SQL Agent está em execução e definido como automático no servidor MAB<br>

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrando em um cofre | Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Faça o download do arquivo de credenciais mais recentes do cofre e tente novamente. <br>(OU)</li> <li> Se a ação anterior não funcionar, tente fazer o download das credenciais para um diretório local diferente ou criar um novo cofre. <br>(OU)</li> <li> Tente atualizar as configurações de data e hora conforme descrito [neste artigo](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided). <br>(OU)</li> <li> Verifique para ver se c:\windows\temp tem mais de 65000 arquivos. Mover arquivos obsoletos para outro local ou exclua os itens na pasta Temp. <br>(OU)</li> <li> Verificar o status dos certificados. <br> a. Abra **Gerenciar Certificados de Computador** (no painel de controle). <br> b. Expandir o **nó pessoal** e seus **certificados**de nó infantil .<br> c.  Remova o certificado **Windows Azure Tools**. <br> d. Repetir o registro no cliente de Backup do Azure. <br> (OU) </li> <li> Verificar se alguma política de grupo está em vigor. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica está inconsistente

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Verifique se a opção de verificação de consistência automática no Assistente de grupo de proteção está ativada. Para obter mais informações sobre as causas da inconsistência da réplica e sugestões relevantes, consulte o artigo [Réplica é inconsistente](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10)).<br> <ol><li> No caso de backup de BMR/estado do sistema, verifique se o Backup do Windows Server está instalado no servidor protegido.</li><li> Verifique se há problemas relacionados ao espaço no pool de armazenamento do DPM no Servidor de Backup do Azure/DPM, e alocar armazenamento conforme necessário.</li><li> Verificar o estado do Serviço de Cópias de Sombra de Volume no servidor protegido. Se ele estiver em um estado desabilitado, defina-o para iniciar manualmente. Inicie o serviço no servidor. Em seguida, volte para o console do Servidor de Backup do Azure/DPM e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação de ponto de recuperação online

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Falha na criação de ponto de recuperação online | **Mensagem de erro**: o Windows Azure Backup Agent não foi capaz de criar um instantâneo do volume selecionado. <br> **Solução de solução**: Tente aumentar o espaço em volume de réplica e ponto de recuperação.<br> <br> **Mensagem de erro**: O agente de backup do Windows Azure não pode se conectar ao serviço OBEngine <br> **Solução alternativa**: verifique se o OBEngine consta na lista de serviços em execução no computador. Se o serviço OBEngine não estiver em execução, use o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Mensagem de erro**: A senha de criptografia para este servidor não está definida. Configure uma frase secreta de criptografia. <br> **Solução de solução**: Tente configurar uma senha de criptografia. Se falhar, execute as seguintes etapas: <br> <ol><li>Verifique se o local de rascunho existe. Esse é o local que é mencionado no registro **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, com o nome **ScratchLocation** deve existir.</li><li> Se houver um local temporário, tente registrar novamente usando a frase secreta antiga. *Sempre que você configurar uma frase secreta de criptografia, salve-a em um local seguro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Os servidores DPM originais e externos devem ser registrados no mesmo cofre

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Restaurar | **Código de erro**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Mensagem de erro**: Os servidores DPM originais e externos devem ser registrados no mesmo cofre | **Causa**: Esse problema ocorre quando você está tentando restaurar arquivos para o servidor alternativo do servidor original usando a opção de recuperação Externa DPM e se o servidor que está sendo recuperado e o servidor original de onde os dados estão em backup não estão associados ao mesmo cofre do Serviço de Recuperação.<br/> <br/>**Solução de solução** Para resolver esse problema, certifique-se de que o servidor original e alternativo esteja registrado no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Falha em trabalhos de criação de ponto de recuperação online para VM do VMware

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Falha em trabalhos de criação de ponto de recuperação online para VM do VMware. O DPM encontrou um erro de VMware ao tentar obter informações de ChangeTracking. Código de erro - Falha de arquivo (ID 33621) |  <ol><li> Redefinir o CTK no VMware para as VMs afetadas.</li> <li>Verificar se o disco independente não está em vigor no VMware.</li> <li>Pare a proteção das VMs afetadas e reproteja com o botão **Atualizar.** </li><li>Executar uma CC para as VMs afetadas.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM no Servidor

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Enviar agentes por push para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM em \<ServerName>. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <ul><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas etapas](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> (OU) </li><li> Se você estiver anexando um computador de um domínio confiável, solucionar problemas usando as etapas descritas [neste blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(OU)</li><li> Tente desabilitar o antivírus como uma etapa de solução de problemas. Se isso resolver o problema, modifique as configurações de antivírus conforme sugerido [neste artigo](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A instalação não pôde atualizar os metadados do Registro

| Operação | Detalhes do erro | Solução alternativa |
|-----------|---------------|------------|
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar esta atualização, a entrada de registro de corte ReFS. | Ajuste a chave de registro **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Defina o valor Dword como 1. |
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da SnapOptimization do Volume. | Crie a chave de registro **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** com um valor de string vazio. |

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao agente

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Enviar agentes por push para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada que é mostrada no produto não funcionar, tome as seguintes etapas:** <br> Tente instalar o agente de proteção manualmente no servidor de produção conforme especificado [neste artigo](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure (ID: 100050) | O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure. | **Se a ação recomendada que é mostrada no produto não funcionar, tome as seguintes etapas:** <br>1. Execute o seguinte comando a partir de um prompt elevado: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Isso abrirá a janela do Internet Explorer. <br/> 2. Vá para **ferramentas** > **opções de internet** > **conexões** > lan**configurações**de LAN . <br/> 3. Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<br/> 4. Se a máquina tiver acesso limitado à Internet, certifique-se de que as configurações de firewall na máquina ou proxy permitam que essas [URLs](install-mars-agent.md#verify-internet-access) e [endereço IP](install-mars-agent.md#verify-internet-access).|
| Falha na instalação do Agente de backup do Azure | Falha na instalação dos Serviços de Recuperação do Microsoft Azure. Todas as alterações feitas ao sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instalar o Agente do Azure manualmente.

## <a name="configuring-protection-group"></a>Configurar grupo de proteção

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não pôde enumerar o componente do aplicativo no computador protegido (nome do computador protegido). | Selecione **Atualizar** na tela da interface do usuário configurar grupo de proteção no nível de componente/fonte de dados relevante. |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme descrito [neste artigo](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Configurar grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção. | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Exclua todos os volumes existentes nos discos. Em seguida, adicione-os ao pool de armazenamento.|
| Alteração da política |Não foi possível modificar a política de backup. Erro: a operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo ter passado. Se o problema persistir, contate o Suporte da Microsoft. | **Causa:**<br/>Esse erro ocorre em três condições: quando as configurações de segurança estiverem habilitadas, quando você tentar reduzir o período de retenção para abaixo dos valores mínimos especificados anteriormente, e quando você estiver usando uma versão sem suporte. (Versões sem suporte são aquelas abaixo da versão 2.0.9052 do Servidor de Backup do Microsoft Azure e atualização do Servidor de Backup do Azure 1). <br/>**Ação recomendada:**<br/> Para continuar com atualizações relacionadas à política, defina o período de retenção acima do período mínimo de retenção especificado. (O período mínimo de retenção é de sete dias para diariamente, quatro semanas para semanal, três semanas para mensal ou um ano para anual.) <br><br>Opcionalmente, outra abordagem preferencial será atualizar o agente de backup e o Servidor de Backup do Azure para utilizar todas as atualizações de segurança. |

## <a name="backup"></a>Backup

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Ocorreu um erro inesperado durante a execução do trabalho. O dispositivo não está pronto. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas:** <br> <ul><li>Defina o Espaço de Armazenamento de Cópia de Sombra para ilimitado nos itens no grupo de proteção e execute a verificação de consistência.<br></li> (OU) <li>Tente excluir o grupo de proteção existente e criar vários grupos novos. Cada novo grupo de proteção deve ter um item individual dentro dele.</li></ul> |
| Backup | Se você estiver fazendo backup somente do estado do sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do estado do sistema. | <ol><li>Verifique se o Backup do Windows Server está instalado no computador protegido.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isso é acessar o computador protegido, abrir o Backup do Windows Server, clicar nas seleções e, em seguida, selecionar a BMR. A interface do usuário lhe mostrará quanto espaço é necessário. Abrir o cronograma local de backup do **WSB** > **Local backup** > **Backup schedule** > Selecione o servidor completo**de configuração** > **de backup** (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha de backup para BMR | Se o tamanho de BMR for grande, mova alguns arquivos do aplicativo para a unidade do sistema operacional e tente novamente. |
| Backup | A opção de reproteger uma VMware VMm em um novo Microsoft Azure Backup Server não aparece como disponível para adicionar. | As propriedades do VMware são apontadas para uma instância antiga e obsoleta do Servidor de Backup do Azure. Para resolver o problema:<br><ol><li>No VCenter (equivalente do SC-VMM), vá para a guia **Resumo** e então **Atributos Personalizados**.</li>  <li>Exclua o antigo nome do Servidor de Backup do Azure do valor **DPMServer**.</li>  <li>Volte para o novo Servidor de Backup do Azure e modifique o PG.  Depois de selecionar o botão **Atualizar**, a VM é exibida com uma caixa de seleção conforme disponível para adicionar para proteção.</li></ol> |
| Backup | Erro ao acessar pastas/arquivos compartilhados | Tente modificar as configurações do antivírus conforme sugerido neste artigo [Execute o software antivírus no servidor DPM](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).|

## <a name="change-passphrase"></a>Alterar frase secreta

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Alterar frase secreta |O PIN de Segurança que foi inserido está incorreto. Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de segurança inválido ou expirado enquanto você estiver executando uma operação crítica (como alterar uma senha). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas, ou quando você tenta alterar a frase secreta quando você está usando uma versão sem suporte.<br/>**Ação recomendada:**<br/> Para alterar a senha, você deve primeiro atualizar o agente de backup para a versão mínima, que é 2.0.9052. Você também precisa atualizar o Servidor de Backup do Azure para a versão mínima de atualização 1 e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |

## <a name="configure-email-notifications"></a>Configurar notificações por email

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar as notificações de e-mail usando uma conta do Office 365 |ID do Erro: 2013| **Causa:**<br> Tentativa de usar a conta do Office 365 <br>**Ação recomendada:**<ol><li> A primeira coisa a garantir é que "Permitir o Relé Anônimo em um Conector receber" para o servidor DPM está configurado no Exchange. Para obter mais informações sobre como configurá-lo, consulte [Permitir retransmissão anônimo em um conector receber](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Se você não pode usar uma retransmissão SMTP interna e precisa configurar usando o servidor do Office 365, você pode configurar o IIS para ser uma retransmissão. Configure o servidor DPM para [retransmitir o SMTP para O365 usando IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Certifique-se de\@usar o formato de domain.com do usuário e *não* o usuário do domínio\.<br><br><li>Aponte o DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o e-mail de usuário de onde os e-mails devem vir.<li> O nome de usuário e a senha na página de instalação de SMTP do DPM devem ser para uma conta de domínio no domínio em que o DPM está. </li><br> quando você estiver alterando o endereço do servidor SMTP, faça a alteração para as novas configurações, feche a caixa de configurações e a abra novamente para se certificar que ela reflita o novo valor.  Simplesmente alterar e testar pode nem sempre fazer com que as novas configurações tenham efeito, então testar assim é a melhor prática.<br><br>A qualquer momento durante esse processo, você pode limpar essas configurações fechando o console do DPM e editando as seguintes chaves do registro: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Excluir SMTPPassword e as chaves SMTPUserName**. Você pode adicioná-las novamente na interface do usuário quando você iniciá-lo novamente.

## <a name="common-issues"></a>Problemas comuns

Esta seção cobre os erros comuns que você pode encontrar ao usar o Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou porque a réplica de backup em disco é inválida ou está ausente. | Para resolver esse problema, verifique as etapas abaixo e tente novamente a operação: <br/> 1. Crie um ponto de recuperação de disco<br/> 2. Executar verificação de consistência na fonte de dados <br/> 3. Pare a proteção da fonte de dados e, em seguida, reconfigure a proteção para essa fonte de dados

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou porque os metadados na réplica são inválidos. | Crie um ponto de recuperação de disco desta fonte de dados e tente novamente o backup on-line novamente

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou devido à réplica de origem de dados inconsistente. | Execute uma verificação de consistência nesta fonte de dados e tente novamente

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou porque a réplica de backup de disco não pôde ser clonada.| Certifique-se de que todos os arquivos de réplica de backup de disco anteriores (.vhdx) estão desmontados e que nenhum backup em disco está em andamento durante backups on-line
