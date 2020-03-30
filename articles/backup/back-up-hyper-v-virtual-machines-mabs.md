---
title: Fazer backup de máquinas virtuais Hyper-V com MABS
description: Este artigo contém os procedimentos de backup e recuperação de máquinas virtuais usando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255058"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Faça backup de máquinas virtuais Hyper-V com o Azure Backup Server

Este artigo explica como fazer backup de máquinas virtuais Hyper-V usando o Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Cenários com suporte

O MABS pode fazer backup de máquinas virtuais em execução em servidores host Hyper-V nos seguintes cenários:

- **Máquinas virtuais com armazenamento local ou direto** – Faça backup das máquinas virtuais hospedadas em servidores autônomos de host Hyper-V que tenham armazenamento local ou diretamente anexado. Por exemplo: um disco rígido, um dispositivo SAN (storage area network, rede de área de armazenamento) ou um dispositivo NAS (Network Attached Storage, armazenamento conectado à rede). O agente de proteção MABS deve ser instalado em todos os hosts.

- **Máquinas virtuais em um cluster com armazenamento CSV** – Faça backup das máquinas virtuais hospedadas em um cluster do Hyper-V com o armazenamento CSV (Volume Compartilhado Clusterizado). O agente de proteção MABS é instalado em cada nó de cluster.

## <a name="host-versus-guest-backup"></a>Backup host versus convidado

O MABS pode fazer um backup de hyper-Vms ou de nível convidado. No nível host, o agente de proteção MABS é instalado no servidor ou cluster de host Hyper-V e protege todos os VMs e arquivos de dados em execução nesse host.   No nível de hóspedes, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente naquela máquina.

Os dois métodos têm vantagens e desvantagens:

- Os backups em nível de host são flexíveis porque funcionam independentemente do tipo de sistema operacional em execução nas máquinas convidadas e não exigem a instalação do agente de proteção MABS em cada VM. Se você implantar o backup de nível de host, poderá recuperar uma máquina virtual inteira ou arquivos e pastas (recuperação em nível de item).

- O backup em nível de hóspede é útil se você quiser proteger cargas de trabalho específicas em execução em uma máquina virtual. No nível host, você pode recuperar uma VM inteira ou arquivos específicos, mas não fornecerá recuperação no contexto de um aplicativo específico. Por exemplo, para recuperar itens específicos do SharePoint de uma VM de backup, você deve fazer backup em nível de hóspede supérilha dessa VM. Use backup em nível de hóspede se quiser proteger os dados armazenados em discos de passagem. O Passthrough permite que a máquina virtual acesse diretamente o dispositivo de armazenamento e não armazene dados de volume virtual em um arquivo VHD.

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

O MABS realiza o backup com o VSS da seguinte forma. As etapas nessa descrição são numeradas para proporcionar maior clareza.

1. O mecanismo de sincronização baseado em blocos MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual seja completa e consistente.

2. Depois que a cópia inicial é feita e verificada, o MABS usa o escritor Hyper-V VSS para capturar backups. O escritor VSS fornece um conjunto consistente de dados de blocos de disco sincronizados com o servidor MABS. Essa abordagem fornece o benefício de um "backup completo" com o servidor MABS, minimizando os dados de backup que devem ser transferidos pela rede.

3. O agente de proteção MABS em um servidor que está executando o Hyper-V usa as APIs Hyper-V existentes para determinar se uma máquina virtual protegida também suporta VSS.

   - Se uma máquina virtual atender aos requisitos para o backup online e possuir o componente dos serviços de integração do Hyper-V instalado, o gravador VSS do Hyper-V recursivamente encaminha a solicitação VSS para todos os processos de reconhecimento de VSS na máquina virtual. Esta operação ocorre sem que o agente de proteção MABS seja instalado na máquina virtual. Essa solicitação VSS recursiva permite que o gravador VSS do Hyper-V verifique se as operações de gravação no disco estão sincronizadas de modo que um instantâneo de VSS seja capturado sem a perda de dados.

     O componente dos serviços de integração do Hyper-V invoca o gravador VSS do Hyper-V nos VSS (Serviços de Cópias de Sombra de Volume) em máquinas virtuais para verificar se os dados do aplicativo estão em um estado consistente.

   - Se a máquina virtual não estiver em conformidade com os requisitos de backup on-line, o MABS usará automaticamente as APIs Hyper-V para pausar a máquina virtual antes de capturar arquivos de dados.

4. Após a cópia inicial da linha de base sincronizar-se com o servidor MABS, todas as alterações feitas nos recursos da máquina virtual são capturadas em um novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual em um horário específico. As capturas dele podem ocorrer pelo menos uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa a replicação em nível de bloco em conjunto com o escritor Hyper-V VSS para determinar quais blocos foram alterados no servidor que está executando o Hyper-V após a criação do último ponto de recuperação. Esses blocos de dados são então transferidos para o servidor MABS e são aplicados à réplica dos dados protegidos.

5. O servidor MABS usa VSS nos volumes que hospedam dados de recuperação para que várias cópias de sombra estejam disponíveis. Cada uma dessas cópias de sombra fornece uma recuperação separada. Os pontos de recuperação VSS são armazenados no servidor MABS. A cópia temporária que é feita no servidor que executa o Hyper-V, só é armazenada durante a duração da sincronização MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais Hyper-V têm o rastreamento de alterações incorporado conhecido como rct (resiliente change tracking). O MABS usa O RCT (o rastreamento de alterações nativas no Hyper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários como falhas de VM. O RCT fornece melhor resiliência do que o rastreamento de alterações fornecido pelos backups baseados em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante as verificações de consistência.

## <a name="backup-prerequisites"></a>Pré-requisitos de backup

Estes são os pré-requisitos para fazer backup de máquinas virtuais Hyper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|- Se você quiser executar a recuperação em nível de item para máquinas virtuais (recuperar arquivos, pastas, volumes), então você precisará instalar a função Hyper-V no servidor MABS.  Se você só quer recuperar a máquina virtual e não o nível do item, então a função não é necessária.<br />- Você pode proteger até 800 máquinas virtuais de 100 GB cada em um servidor MABS e permitir vários servidores MABS que suportam clusters maiores.<br />- O MABS exclui o arquivo de página de backups incrementais para melhorar o desempenho de backup da máquina virtual.<br />- O MABS pode fazer backup de um servidor ou cluster Hyper-V no mesmo domínio do servidor MABS, ou em um domínio filho ou confiável. Se você quiser fazer backup do Hyper-V em um grupo de trabalho ou em um domínio não confiável, você precisará configurar a autenticação. Para um único servidor Hyper-V, você pode usar NTLM ou autenticação de certificado. Para um cluster, você pode usar apenas autenticação de certificado.<br />-   Não há suporte para o uso do backup em nível de host para fazer backup de dados da máquina virtual em discos de passagem. Neste cenário, recomendamos que você use backup em nível de host para fazer backup de arquivos VHD e backup em nível de hóspedes para fazer backup dos outros dados que não são visíveis no host.<br />   - Você pode fazer backup de VMs armazenados em volumes duplicados.|
|Pré-requisitos da VM do Hyper-V|- A versão dos Componentes de Integração que está sendo executado na máquina virtual deve ser a mesma do host Hyper-V. <br />-   Para cada backup de máquinas virtuais, você deverá liberar espaço no volume que hospeda os arquivos do disco rígido virtual para permitir espaço suficiente ao Hyper-V para discos diferenciais (AVHDs) durante o backup. O espaço deve ser, pelo menos, igual ao tempo da janela de cálculo do **Tamanho inicial do disco\*Taxa de rotatividade\*Backup**. Se estiver executando vários backups em um cluster, você precisará ter capacidade de armazenamento suficiente para acomodar os AVHDs para cada uma das máquinas virtuais usando esse cálculo.<br />- Para fazer backup de máquinas virtuais localizadas em servidores de host Hyper-V executando o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que não esteja conectado a nada. (No backup on-line do Windows Server 2012 R2, o host Hyper-V monta um novo VHD na VM e depois o desmonta. Apenas o controlador SCSI pode suportar isso e, portanto, é necessário para o backup on-line da máquina virtual.  Sem essa configuração, o ID de evento 10103 será emitido quando você tentar fazer backup da máquina virtual.)|
|Pré-requisitos do Linux|- Você pode fazer backup de máquinas virtuais Linux usando o MABS. Somente os instantâneos consistentes de arquivos têm suporte.|
|Fazer backup de VMs com armazenamento CSV|-   Para o armazenamento CSV, instale o provedor de hardware dos VSS (Serviços de Cópias de Sombra de Volume) no servidor Hyper-V. Entre em contato com seu fornecedor de SAN (rede de área de armazenamento) para o provedor de hardware dos VSS.<br />- Se um único nó for desligado inesperadamente em um cluster CSV, o MABS realizará uma verificação de consistência em relação às máquinas virtuais que estavam sendo usadas nesse nó.<br />-   Se precisar reiniciar um servidor Hyper-V que tenha a Criptografia de Unidade de Disco BitLocker habilitada no cluster CSV, você deverá executar uma verificação de consistência nas máquinas virtuais do Hyper-V.|
|Fazer backup de VMs com armazenamento SMB|-   Ative a montagem automática no servidor executando o Hyper-V para habilitar a proteção da máquina virtual.<br />   -   Desabilite o Descarregamento de Chimney TCP.<br />-   Verifique se todas as contas do computador$ do Hyper-V têm permissões completas sobre os compartilhamentos específicos de arquivos SMB remotos.<br />- Certifique-se de que o caminho do arquivo para todos os componentes da máquina virtual durante a recuperação para a localização alternativa é inferior a 260 caracteres. Se não, a recuperação pode ter sucesso, mas o Hyper-V não será capaz de montar a máquina virtual.<br />- Os seguintes cenários não são suportados:<br />     Implantações onde alguns componentes da máquina virtual estão em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para servidor de arquivos de localização de armazenamento e recuperação de uma máquina virtual para um computador que usa compartilhamentos remotos de SMB.<br />- Você precisará ativar o serviço de agente VSS do servidor de arquivos em cada servidor SMB - Adicioná-lo em **Adicionar funções e recursos** > **Selecionar funções** > de servidor**Serviços de arquivo e serviços** > de armazenamento**Serviços** > **de arquivo Serviço de arquivo** > **Serviço de arquivo Servidor DE ARQUIVOS Serviço SS Agent Service**.|

## <a name="back-up-virtual-machines"></a>Fazer backup de máquinas virtuais

1. Configure seu [servidor MABS](backup-azure-microsoft-azure-backup.md) e [seu armazenamento](backup-mabs-add-storage.md). Ao configurar seu armazenamento, use estas diretrizes de capacidade de armazenamento.
   - Tamanho médio da máquina virtual – 100 GB
   - Número de máquinas virtuais por servidor MABS - 800
   - Tamanho total de 800 VMs – 80 TB
   - Espaço necessário para o armazenamento de backup – 80 TB

2. Configure o agente de proteção MABS nos ates de cluster Hyper-V ou Hyper-V. Se você estiver fazendo backup no nível do hóspede, você instalará o agente nas VMs que deseja fazer backup no nível do hóspede.

3. No console administrador do MABS, clique em **Proteger** > **Criar grupo de proteção** para abrir o **assistente Criar novo grupo de proteção.**

4. Na página **Selecionar Membros do Grupo**, selecione as VMs que você deseja proteger dos servidores host Hyper-V em que estão localizadas. É recomendável que você coloque todas as VMs que terão a mesma política de proteção no mesmo grupo de proteção. Habilite a colocação para proporcionar um uso eficiente do espaço. A colocação permite que você localize dados de grupos de proteção diferentes no mesmo armazenamento em disco ou fita para que várias fontes de dados tenham uma única réplica e volume de pontos de recuperação.

5. Na página **Selecionar Método de Proteção de Dados**, especifique um nome do grupo de proteção. Selecione **Desejo proteção de curto prazo usando disco** e selecione **Desejo proteção online** se você deseja fazer backup de dados no Azure usando o serviço de Backup do Azure.

6. Em **Especificar** > o intervalo de**retenção**de gols de curto prazo, especifique quanto tempo deseja reter dados de disco. Na **freqüência de sincronização,** especifique quantas vezes os backups incrementais dos dados devem ser executados. Como alternativa, em vez de selecionar um intervalo para backups incrementais, você pode habilitar **Logo antes de um ponto de recuperação**. Com esta configuração ativada, o MABS executará um backup completo expresso pouco antes de cada ponto de recuperação programado.

    > [!NOTE]
    >
    >Se você estiver protegendo cargas de trabalho do aplicativo, os pontos de recuperação serão criados de acordo com a frequência de sincronização, desde que o aplicativo dê suporte a backups incrementais. Se isso não funcionar, o MABS executa um backup completo expresso, em vez de um backup incremental, e cria pontos de recuperação de acordo com o cronograma de backup expresso.

7. Na página **Examinar alocação de disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

   **O tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup, e o espaço em disco a ser **provisionado no MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas configurações. No entanto, você pode editar as opções de volume de backup nos **detalhes de alocação de disco**. Para as cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o MABS sugere que você adicione ao volume, para continuar com backups suavemente no futuro.

8. Na página **Escolher método de criação de réplica**, especifique como a replicação inicial de dados no grupo de proteção será executada. Se você selecionar para **replicar automaticamente sobre a rede,** recomendamos que você escolha um horário fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere selecionar **Manualmente,** o que requer replicar os dados off-line usando mídia removível.

9. Na página **Opções de verificação de consistência**, selecione como deseja automatizar as verificações de consistência. Você pode habilitar uma verificação para ser executada somente quando os dados de réplica se tornarem inconsistentes, ou de acordo com uma agenda. Se não quiser configurar a verificação de consistência automática, você poderá executar uma verificação manual a qualquer momento clicando com o botão direito do mouse no grupo de proteção e selecionando **Executar Verificação de Consistência**.

    Depois de criar o grupo de proteção, a replicação inicial dos dados ocorrerá de acordo com o método selecionado. Após a replicação inicial, cada backup ocorrerá em conformidade com as configurações do grupo de proteção. Se você precisar recuperar dados de backup, observe o seguinte:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Faça o backup das máquinas virtuais configuradas para migração dinâmica

Quando as máquinas virtuais estão envolvidas na migração ao vivo, o MABS continua a proteger as máquinas virtuais enquanto o agente de proteção MABS estiver instalado no host Hyper-V. A forma como o MABS protege as máquinas virtuais depende do tipo de migração ao vivo envolvida.

**Migração ao vivo dentro** de um cluster - Quando uma máquina virtual é migrada dentro de um cluster, o MABS detecta a migração e faz backup da máquina virtual do novo nó de cluster sem qualquer necessidade de intervenção do usuário. Como o local de armazenamento não mudou, o MABS continua com backups completos expressos.

**Migração ao vivo fora do cluster** - Quando uma máquina virtual é migrada entre servidores autônomos, diferentes clusters ou entre um servidor autônomo e um cluster, o MABS detecta a migração e pode fazer backup da máquina virtual sem intervenção do usuário.

### <a name="requirements-for-maintaining-protection"></a>Requisitos para manter a proteção

Veja a seguir os requisitos para manter a proteção durante a migração dinâmica:

- Os hosts Hyper-V para as máquinas virtuais devem estar localizados em uma nuvem VMM do Centro de Sistema, em um servidor VMM, executando pelo menos o System Center 2012 com o SP1.

- O agente de proteção MABS deve ser instalado em todos os hosts Hyper-V.

- Os servidores MABS devem ser conectados ao servidor VMM. Todos os servidores de host Hyper-V na nuvem VMM também devem ser conectados aos servidores MABS. Isso permite que o MABS se comunique com o servidor VMM para que o MABS possa descobrir em qual servidor de host Hyper-V a máquina virtual está em execução no momento e criar um novo backup a partir desse servidor Hyper-V. Se uma conexão não puder ser estabelecida no servidor Hyper-V, o backup falhará com uma mensagem de que o agente de proteção MABS está inalcançável.

- Todos os servidores MABS, servidores VMM e servidores de host Hyper-V devem estar no mesmo domínio.

### <a name="details-about-live-migration"></a>Detalhes sobre a migração dinâmica

Observe o seguinte para o backup durante a migração dinâmica:

- Se um armazenamento de transferência de migração ao vivo, o MABS realiza uma verificação completa de consistência da máquina virtual e, em seguida, continua com backups completos expressos. Quando ocorre a migração ao vivo do armazenamento, o Hyper-V reorganiza o disco rígido virtual (VHD) ou VHDX, o que causa um pico único no tamanho dos dados de backup do MABS.

- No host da máquina virtual, ative a montagem automática para habilitar a proteção virtual e desabilite o Descarregamento de Chimney TCP.

- O MABS usa a porta 6070 como a porta padrão para hospedar o Serviço auxiliar DPM-VMM. Para alterar o Registro:

    1. Navegue até **hklm\software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Crie um valor DWORD de 32 bits: DpmVmmHelperServicePort e escreva o número da porta atualizado como parte da chave do registro.
    3. Abra ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config``` e altere o número da porta de 6070 para a nova porta. Por exemplo: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Reinicie o Serviço Auxiliar do DPM-VMM e reinicie o serviço DPM.

### <a name="set-up-protection-for-live-migration"></a>Set up protection for live migration

Configure a proteção para migração dinâmica:

1. Configure o servidor MABS e seu armazenamento e instale o agente de proteção MABS em cada servidor de host Hyper-V ou nó de cluster na nuvem VMM. Se você estiver usando o armazenamento SMB em um cluster, instale o agente de proteção MABS em todos os nós de cluster.

2. Instale o console VMM como um componente cliente no servidor MABS para que o MABS possa se comunicar com o servidor VMM. O console deve ser da mesma versão daquele que está em execução no servidor VMM.

3. Atribua a conta MABSMachineName$ como uma conta de administrador somente leitura no servidor de gerenciamento VMM.

4. Conecte todos os servidores host Hyper-V a `Set-DPMGlobalProperty` todos os servidores MABS com o cmdlet PowerShell. O cmdlet aceita vários nomes de servidor MABS. Use o formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Para obter mais informações, consulte [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Depois que todas as máquinas virtuais em execução nos hosts Hyper-V nas nuvens do VMM forem descobertas no VMM, configure um grupo de proteção e adicione as máquinas virtuais que você deseja proteger. Verificações automáticas de consistência devem ser habilitadas no nível do grupo de proteção para proteção em cenários de mobilidade de máquinas virtuais.

6. Depois que as configurações são configuradas, quando uma máquina virtual migra de um cluster para outro, todos os backups continuam como esperado. Você pode verificar se a migração ao vivo está habilitada conforme o esperado da seguinte maneira:

   1. Verifique se o serviço auxiliar do DPM-VMM está em execução. Se não for, comece.

   2. Abra o Microsoft SQL Server Management Studio e conecte-se à instância que hospeda o banco de dados MABS (DPMDB). No DPMDB, execute a seguinte consulta: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Esta consulta contém uma `KnownVMMServer`propriedade chamada . Esse valor deve ser igual àquele fornecido com o cmdlet `Set-DPMGlobalProperty`.

   3. Execute a consulta a seguir para validar o parâmetro *VMMIdentifier* no `PhysicalPathXML` para uma máquina virtual específica. Substitua `VMName` pelo nome da máquina virtual.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Abra o arquivo .xml que retornou da consulta e verifique se o campo *VMMIdentifier* possui um valor.

### <a name="run-manual-migration"></a>Executar a migração manual

Depois de concluir as etapas nas seções anteriores e o trabalho de Gerente de Resumo do MABS for concluído, a migração será ativada. Por padrão, esse trabalho inicia-se à meia-noite e é executado todas as manhãs. Se desejar executar uma migração manual para verificar se tudo está funcionando conforme o esperado, faça o seguinte:

1. Abra o SQL Server Management Studio e conecte-se à instância que hospeda o banco de dados MABS.

2. Execute a seguinte consulta: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Essa consulta retorna a **ScheduleID**. Anote essa ID, pois ela será usada na próxima etapa.

3. No SQL Server Management Studio, expanda **SQL Server Agent**e **Trabalhos**. Clique com o botão direito do mouse na **ScheduleID** anotada e selecione **Iniciar Trabalho na Etapa**.

O desempenho de backup é afetado quando o trabalho é executado. O tamanho e a escala de sua implantação determinarão o tempo que esse trabalho levará para ser concluído.

## <a name="back-up-replica-virtual-machines"></a>Fazer backup de máquinas virtuais de réplica

Se o MABS estiver sendo executado no Windows Server 2012 R2 ou superior, então você pode fazer backup de réplicas de máquinas virtuais. Isso é útil por vários motivos:

**Reduz o impacto dos backups sobre a carga de trabalho em execução** – Fazer backup em uma máquina virtual gera uma sobrecarga à medida que um instantâneo é criado. Ao descarregar o processo de backup para um local remoto secundário, a carga de trabalho em execução não é mais afetada pela operação de backup. Isso aplica-se somente a implantações em que a cópia de backup está armazenada em um site remoto. Por exemplo, você pode fazer backups diários e armazenar dados localmente para garantir tempos de restauração rápidos, mas faça backups mensais ou trimestrais de máquinas virtuais de réplica armazenados remotamente para retenção de longo prazo.

**Economiza largura de banda** – Em uma implantação remota típica de escritório/filial, você precisará que uma quantidade adequada de largura de banda seja provisionada pelos administradores para transferir dados de backup entre os locais. Se você criar uma estratégia de failover e replicação além de sua estratégia de backup de dados, poderá reduzir a quantidade de dados redundantes enviados pela rede. Ao fazer backup dos dados da máquina virtual de réplica em vez do principal, você salva a sobrecarga de enviar os dados de backup pela rede.

**Habilitar o backup de hoster** – Você pode usar um datacenter hospedado como um site de réplica, sem a necessidade de um datacenter secundário. Neste caso, o Hoster SLA requer backup consistente de réplicas de máquinas virtuais.

Uma máquina virtual de réplica é desativada até que um failover seja iniciado e o VSS não possa assegurar um backup consistente com o aplicativo para uma máquina virtual de réplica. Portanto, o backup de uma máquina virtual de réplica será somente com controle de falhas. Se não for possível garantir a consistência de falha, o backup falhará e isso poderá ocorrer em algumas condições:

- A máquina virtual de réplica não está íntegra e se apresenta em um estado crítico.

- A máquina virtual de réplica está sendo sincronizada novamente (no estado de Ressincronização em andamento ou de Ressincronização Necessária).

- A replicação inicial entre os locais primário e secundário está em andamento ou pendente para a máquina virtual.

- Os logs .hrl estão sendo aplicados à máquina virtual de réplica ou uma ação anterior para aplicar os logs .hrl no disco virtual falhou ou foi cancelada ou interrompida.

- A migração ou failover da máquina virtual de réplica está em andamento

## <a name="recover-backed-up-virtual-machines"></a>Recuperar as máquinas virtuais com backup

Quando for possível recuperar uma máquina virtual do backup, use o Assistente de Recuperação para selecionar a máquina virtual e o ponto de recuperação específico. Use o Assistente de Recuperação e recupere a máquina virtual:

1. No console Administrador do MABS, digite o nome da VM ou expanda a lista de itens protegidos e selecione a VM que deseja recuperar.

2. No painel **Pontos de recuperação para**, no calendário, clique em qualquer data para ver os pontos de recuperação disponíveis. No painel **Caminho**, selecione o ponto de recuperação que você deseja usar no Assistente de Recuperação.

3. No menu **Ações**, clique em **Recuperar** para abrir o Assistente de Recuperação.

    A VM e o ponto de recuperação selecionados aparecem na tela **Examinar Seleção de Recuperação**. Clique em **Avançar**.

4. Na tela **Selecionar Tipo de Recuperação**, selecione onde você deseja restaurar os dados e clique em **Avançar**.

    - **Recuperar na instância original**: ao recuperar na instância original, o VHD original é excluído. O MABS recupera o VHD e outros arquivos de configuração para o local original usando o escritor Hyper-V VSS. Ao término do processo de recuperação, as máquinas virtuais ainda estarão altamente disponíveis.
        O grupo de recursos deve estar presente para recuperação. Se não estiver disponível, recupere em um local alternativo e crie a máquina virtual altamente disponível.

    - **Recuperar como máquina virtual para qualquer host**: O MABS suporta recuperação de localização alternativa (ALR), que fornece uma recuperação perfeita de uma máquina virtual Hyper-V protegida para um host Hyper-V diferente, independente da arquitetura do processador. As máquinas virtuais do Hyper-V que forem recuperadas em um nó de cluster não estarão altamente disponíveis. Se você escolher essa opção, o Assistente de Recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.

    - **Copiar para uma pasta**de rede : O MABS suporta a recuperação em nível de item (ILR), que permite fazer a recuperação em nível de item de arquivos, pastas, volumes e discos rígidos virtuais (VHDs) de um backup em nível de host de máquinas virtuais Hyper-V para um compartilhamento de rede ou um volume em um servidor protegido pelo MABS. O agente de proteção MABS não precisa ser instalado dentro do convidado para realizar a recuperação em nível de item. Se você escolher essa opção, o Assistente de Recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.

5. Em **Especificar as Opções de Recuperação**, configure as opções de recuperação e clique em **Avançar**:

    - Se você estiver recuperando uma VM em baixa largura de banda, clique em **Modificar** para habilitar a **Limitação do uso da largura de banda da rede**. Depois de ativar a opção de limitação, você pode especificar a quantidade de largura de banda que você deseja disponibilizar e a hora em que ela estará disponível.
    - Selecione **Habilitar a recuperação baseada em SAN usando instantâneos de hardware** se tiver configurado a rede.
    - Selecione **Enviar um email ao concluir a recuperação** e forneça os endereços de email se desejar que notificações de email sejam enviadas quando o processo de recuperação for concluído.

6. Na tela de Resumo, verifique se todos os detalhes estão corretos. Se os detalhes não estiverem corretos ou se desejar fazer uma alteração, clique em **Voltar**. Se você estiver satisfeito com as configurações, clique em **Recuperar** para iniciar o processo de recuperação.

7. A tela **Status de Recuperação** fornece informações sobre o trabalho de recuperação.

## <a name="next-steps"></a>Próximas etapas

[Recuperar dados do Servidor de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
