---
title: Estado do sistema e proteção de recuperação de metais nus
description: Use o Azure Backup Server para fazer backup do estado do sistema e fornecer proteção BMR (Bare-Metal Recovery, recuperação de metais nus).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505887"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Faça backup do estado do sistema e restaure o metal nu usando o Azure Backup Server

Use o Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de recuperação bare-metal (BMR).

* **Backup do estado do sistema**: Faz backup de arquivos do sistema operacional. Este backup permite que você se recupere quando um computador é iniciado, mas os arquivos do sistema e o registro são perdidos. Um backup do estado do sistema inclui os seguintes elementos:
  * Membro do domínio: arquivos de inicialização, o banco de dados de registro da classe COM+, o registro
  * Controlador de domínio: Windows Server Active Directory (NTDS), arquivos de inicialização, o banco de dados de registro da classe COM+, o registro, o volume do sistema (SYSVOL)
  * Computador que executa os serviços de cluster: metadados do servidor de Cluster
  * Computador que executa os serviços de certificados: dados de certificado
* **Backup em metal nu :** Faz backup de arquivos do sistema operacional e todos os dados em volumes críticos, exceto para dados do usuário. Por definição, um backup de BMR inclui um backup de estado do sistema. Ele oferece proteção quando um computador não será iniciado e você precisa recuperar tudo.

A tabela a seguir resume o que você pode fazer backup e recuperar. Para obter informações sobre versões do aplicativo que o estado do sistema e o BMR podem proteger, consulte [o que o Azure Backup Server faz backup?](backup-mabs-protection-matrix.md)

|Backup|Problema|Recuperar de backup do Servidor de Backup do Azure|Recuperar de backup de estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de arquivo**<br /><br />Backup de dados regular<br /><br />Backup de estado do sistema/BMR|Dados de arquivos perdidos|S|N|N|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|S|N|S<br /><br />BMR, seguido de recuperação regular de dados de arquivos de backup|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Recuperação de desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|VM perdida|S|N|N|
|Hyper-v<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Sistema operacional perdido ou danificado|N|S|S|
|Hyper-v<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Host do Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-v<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Host do Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação regular do Servidor de Backup do Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Dados do aplicativo perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (logs de transação/banco de dados intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (logs de transação/banco de dados perdidos)|N|N|S<br /><br />Recuperação de BMR, seguida de recuperação regular do Servidor de Backup do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona o backup de estado do sistema

Quando um backup de estado do sistema é executado, o Servidor de Backup se comunica com o Backup do Windows Server para solicitar um backup de estado do sistema do servidor. Por padrão, o Servidor de Backup e o Backup do Windows Server usam a unidade que tenha mais espaço livre. As informações sobre esta unidade são salvas no arquivo *PSDataSourceConfig.xml.* 

Você pode personalizar a unidade que o Backup Server usa para o backup do estado do sistema: 

1. No servidor protegido, vá para *C:\Arquivos de programa\Microsoft Data Protection Manager\MABS\Datasources*. 
1. Abra o arquivo *PSDataSourceConfig.xml* para edição. 
1. Altere o valor \<FilesToProtect\> para a letra da unidade. 
1. Salve e feche o arquivo. 

Se um grupo de proteção estiver definido para proteger o estado do sistema do computador, execute uma verificação de consistência. Se um alerta for gerado, selecione Modificar grupo **de proteção** no alerta e, em seguida, complete as páginas no assistente. Em seguida, execute outra verificação de consistência.

Se o servidor de proteção estiver em um cluster, uma unidade de cluster pode ser selecionada como a unidade que tem mais espaço livre. Se essa propriedade da unidade for comutada para outro nó e um backup do estado do sistema for executado, a unidade não está disponível e o backup falha. Neste cenário, modifique *PSDataSourceConfig.xml* para apontar para uma unidade local.

Em seguida, o Backup do Windows Server cria uma pasta chamada *WindowsImageBackup* na raiz da pasta de restauração. À medida que o Backup do Windows Server cria o backup, todos os dados são colocados nessa pasta. Quando o backup termina, o arquivo é transferido para o computador do Servidor de Backup. Observe as seguintes informações:

* Esta pasta e seu conteúdo não são limpos quando o backup ou transferência termina. A melhor maneira de pensar nisso é que o espaço está reservado para a próxima vez que um backup terminar.
* A pasta é criada para cada backup. O carimbo de data/hora e reflete a hora de seu último backup de estado do sistema.

## <a name="how-bmr-backup-works"></a>Como funciona o backup do BMR

Para a BMR (incluindo um backup de estado do sistema), o trabalho de backup é salvo diretamente para um compartilhamento no computador do Servidor de Backup. Ele não é salvo em uma pasta no servidor protegido.

Servidor de Backup chama o Backup do Windows Server e compartilha o volume de réplica para esse backup BMR. Neste caso, ele não requer o Backup do Windows Server para usar a unidade que tem mais espaço livre. Em vez disso, ele usa o compartilhamento que foi criado para o trabalho.

Quando o backup termina, o arquivo é transferido para o computador do Servidor de Backup. Os logs são armazenados em *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* BMR não tem suporte para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operacional cliente.

* Você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos de proteção diferentes.

* Um computador do Servidor de Backup não pode se proteger de BMR.

* A proteção de curto prazo para fita (disco para fita, ou D2T) não é suportada para BMR. O armazenamento a longo prazo para fita (disco a disco para fita, ou D2D2T) é suportado.

* Para proteção de BMR, o Backup do Windows Server deve ser instalado no computador protegido.

* Para proteção bmr, ao contrário da proteção do estado do sistema, o Backup Server não tem requisitos de espaço no computador protegido. Backup do Windows Server transfere diretamente os backups para o computador do Servidor de Backup. O trabalho de transferência de backup não aparece na exibição de **trabalhos** do Servidor de Backup.

* O Servidor de Backup reserva 30 GB de espaço no volume de réplica para a BMR. Você pode alterar esse loteamento de espaço na página **alocação de disco** no assistente de grupo de proteção modificada. Ou você pode usar os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell. No volume de ponto de recuperação, a proteção BMR exige cerca de 6 GB para uma retenção de cinco dias.
  * Você não pode reduzir o tamanho do volume da réplica para menos de 15 GB.
  * Servidor de Backup não calcula o tamanho da fonte de dados BMR. Ele presume 30 GB para todos os servidores. Altere o valor com base no tamanho dos backups da BMR esperados no seu ambiente. Você pode calcular aproximadamente o tamanho de um backup BMR como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de inicialização + volume do sistema + volume que hospeda os dados de estado do sistema, como o Active Directory.

* Se você mudar da proteção do estado do sistema para a proteção do BMR, então a proteção do BMR requer menos espaço no volume do *ponto de recuperação*. No entanto, o espaço extra no volume não é recuperado. Você pode reduzir manualmente o tamanho do volume na página **'Modificar alocação** de disco' do assistente 'Modificar grupo de proteção'. Ou você pode usar os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se você mudar da proteção do estado do sistema para a proteção do BMR, então a proteção do BMR requer mais espaço no volume da *réplica*. O volume é estendido automaticamente. Se você quiser alterar as alocações de espaço padrão, use o cmdlet Modify-DiskAllocation PowerShell.

* Se você mudar da proteção do BMR para a proteção do estado do sistema, então você precisa de mais espaço no volume do ponto de recuperação. O Servidor de Backup pode tentar aumentar automaticamente o volume. Se o pool de armazenamento não tiver espaço suficiente, um erro ocorrerá.

    Se você mudar da proteção do BMR para a proteção do estado do sistema, então você precisa de espaço no computador protegido. Você precisa do espaço porque a proteção do estado do sistema primeiro grava a réplica para o computador local e, em seguida, transfere a réplica para o computador do Servidor de Backup.

## <a name="before-you-begin"></a>Antes de começar

1. **Implantar o Servidor de Backup do Azure**. Verifique se o Servidor de Backup é implantado corretamente. Para obter mais informações, consulte:
    * [Requisitos de sistema para o Servidor de Backup do Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do Servidor de Backup](backup-mabs-protection-matrix.md)

1. **Configurar o armazenamento**. Você pode armazenar dados de backup em disco, fita e na nuvem com o Azure. Para obter mais informações, consulte [Preparar dados de armazenamento](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Configurar o agente de proteção**. Instale o agente de proteção no computador que você deseja fazer backup. Para obter mais informações, consulte [Implantar o agente de proteção do DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Fazer backup do estado do sistema e bare-metal

Para fazer backup do estado do sistema e do metal nu:

1. Para abrir o Create New Protection Group Wizard, no console do administrador do servidor de backup, selecione **'Ações** >  **de proteção** > **Criar grupo'**

1. Na página **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores** e, depois, selecione **Avançar**.

1. Na página **Selecionar membros do grupo**, expanda o computador e, em seguida, selecione **BMR** ou **o estado do sistema**.

    Observe que você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos diferentes. Além disso, quando você seleciona a BMR, o estado do sistema é habilitado automaticamente. Para obter mais informações, consulte [Implantar Grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

1. Na página **Seleto método de proteção de dados,** escolha como lidar com backup de curto prazo e backup a longo prazo. 

    O backup de curto prazo é sempre para o disco primeiro, com a opção de fazer backup do disco para o Azure usando o Azure Backup (a curto ou longo prazo). Uma alternativa para backup de longo prazo para a nuvem é configurar o backup de longo prazo para um dispositivo de fita autônoma ou biblioteca de fitas que está conectada ao Servidor de Backup.

1. Na página **Selecionar metas de curto prazo,** escolha como fazer backup de armazenamento de curto prazo em disco:
    * Para **o intervalo de retenção,** escolha quanto tempo para manter os dados no disco.
    * Para **obter a freqüência de sincronização,** escolha com que frequência executar um backup incremental em disco. Se você não quiser definir um intervalo de backup, você pode selecionar **pouco antes de um ponto de recuperação**. O Backup Server executará um backup completo expresso pouco antes de cada ponto de recuperação ser agendado.

1. Se você quiser armazenar dados em fita para armazenamento a longo prazo, então na página **Especificar Metas de Longo Prazo,** escolha quanto tempo para manter os dados de fita (1 a 99 anos).
    1. Para **freqüência de backup,** escolha com que frequência executar backup em fita. A freqüência é baseada na faixa de retenção selecionada:
        * Quando a faixa de retenção é de 1 a 99 anos, você pode fazer backup diariamente, semanalmente, quinzenal, mensal, trimestral, semestral ou anual.
        * Quando a faixa de retenção é de 1 a 11 meses, você pode fazer backup diariamente, semanalmente, quinzenalmente ou mensalmente.
        * Quando o intervalo de retenção é de 1 a 4 semanas, você pode fazer backup diariamente ou semanalmente.

    1. Na página **Selecionar fita e detalhes da biblioteca,** selecione a fita e a biblioteca a serem usados. Escolha também se os dados devem ser compactados e criptografados.

1. Na página **'Alocação de disco de revisão',** revise o espaço em disco do pool de armazenamento disponível para o grupo de proteção.

    * **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup.
    * **Espaço em disco para ser provisionado no Servidor de Backup do Azure** é o espaço que o Servidor de Backup recomenda para o grupo de proteção. O Backup Server usa essas configurações para escolher o volume de backup ideal. Você pode editar as opções de volume de backup em **detalhes de alocação de disco**.
    * Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o Backup Server sugere que você adicione ao volume para garantir backups suaves.

1. Na página **Escolher método de criação de réplicas,** selecione como lidar com a replicação inicial de dados completos. 

    Se você optar por replicar pela rede, recomendamos que você escolha um horário de menos movimento. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

1. Na página **Escolher opções de verificação de consistência,** selecione como automatizar verificações de consistência. 

    Você pode escolher executar uma verificação somente quando dados de réplica se tornam inconsistentes ou em um agendamento. Se você não quiser configurar verificação automática de consistência, então você pode executar uma verificação manual a qualquer momento. Para executar uma verificação manual, na área de **Proteção** do Console do Administrador do Servidor de Backup do Azure, clique com o botão direito no grupo de proteção e, depois, selecione **Executar Verificação de Consistência**.

1. Se você optar por fazer backup na nuvem usando o Azure Backup, na página **Especificar dados de proteção on-line,** selecione as cargas de trabalho que deseja fazer backup no Azure.

1. Na página **Especificar agendamento de backup on-line,** selecione quantas vezes fazer backup gradualmente no Azure. 

    Você pode agendar backups para funcionar todos os dias, semana, mês e ano. Você também pode selecionar a hora e a data em que os backups devem ser executados. Backups podem ocorrer até duas vezes por dia. Cada vez que um backup é executado, um ponto de recuperação de dados é criado no Azure a partir da cópia dos dados de backup armazenados no disco Backup Server.

1. Na página **Especificar política de retenção on-line,** selecione como os pontos de recuperação criados a partir dos backups diários, semanais, mensais e mensais são mantidos no Azure.

1. Na página **Escolher Replicação Online**, selecione como ocorre a replicação inicial completa de dados. 

    Você pode replicar pela rede ou fazer backup offline (semeação off-line). Um backup offline usa o recurso Azure Import. Para obter mais informações, consulte [Fluxo de trabalho de backup offline no Backup do Azure](offline-backup-azure-data-box.md).

1. Na página **Resumo**, examine as configurações. Depois de selecionar **Criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados termina, na página **Status,** o status do grupo de proteção é **OK**. Os backups acontecem de acordo com as configurações do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou BMR

Você pode recuperar BMR ou estado do sistema para um local de rede. Se você fizer backup do BMR, use o Ambiente de Recuperação do Windows (WinRE) para iniciar o sistema e conectá-lo à rede. Use o Backup do Windows Server para recuperar de um local de rede. Se você fizer backup do estado do sistema, use o Backup do Windows Server para recuperar a localização da rede.

### <a name="restore-bmr"></a>Restaurar BMR

Para executar a recuperação no computador do Servidor de Backup:

1. No painel **Recuperação,** encontre o computador que deseja recuperar. Em seguida, **selecione Recuperação de Metal Bare**.

1. Pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

1. Na página **Selecionar tipo de recuperação,** **selecione Copiar para uma pasta de rede**.

1. Na página **Especificar destino,** selecione o destino dos dados copiados. 

    Lembre-se, o destino precisa ter espaço suficiente para os dados. Recomendamos que você crie uma nova pasta para o destino.

1. Na página **Especificar opções de recuperação,** selecione as configurações de segurança. Em seguida, selecione se deve usar instantâneos de hardware baseados em san (storage area network, san) para uma recuperação mais rápida. Esta opção só está disponível se:
    * Você tem uma SAN que fornece essa funcionalidade. 
    * Você pode criar e dividir um clone para torná-lo gravável.
    * O computador protegido e o computador backup server estão conectados à mesma rede.

1. Configure opções de notificação. 
1. Na página **Confirmação**, selecione **Recuperar**.

Para configurar a localização do compartilhamento:

1. No local de recuperação, vá para a pasta que contém o backup.

1. Compartilhe a pasta que está um nível acima do *WindowsImageBackup* para que a raiz da pasta compartilhada seja a pasta *WindowsImageBackup.* 

    Se você não compartilhar essa pasta, a restauração não encontrará o backup. Para se conectar usando o WinRE, você precisa de um compartilhamento que você possa acessar no WinRE com o endereço IP e credenciais corretos.

Para restaurar o sistema:

1. Inicie o computador no qual deseja restaurar a imagem usando o DVD do Windows para o sistema que você está restaurando.

1. Na primeira página, verifique as configurações para idioma e local. Na página **Instalar**, selecione **Reparar seu computador**.

1. Na página **Opções de Recuperação do Sistema**, selecione **Restaurar seu computador usando uma imagem do sistema que você criou anteriormente**.

1. Na página **Selecionar um backup de imagem do sistema**, selecione **Selecionar uma imagem do sistema** > **Avançado** > **Pesquisar por uma imagem de sistema na rede**. Se um aviso for exibido, selecione **Sim**. Vá para o caminho de compartilhamento, insira as credenciais e, em seguida, selecione o ponto de recuperação. O sistema verifica backups específicos disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que você deseja usar.

1. Na **Escolha como restaurar a** página de backup, selecione Discos de formato e **repartição**. Na página seguinte, verifique as configurações.

1. Para iniciar a restauração, selecione **Concluir**. Uma reinicialização é necessária.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Para executar a recuperação no Backup Server:

1. No painel de **Recuperação**, localize o computador que deseja recuperar e selecione **Recuperação Bare-Metal**.

1. Pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

1. Na página **Selecionar tipo de recuperação,** **selecione Copiar para uma pasta de rede**.

1. Na página **Especificar destino,** selecione onde copiar os dados. 

    Lembre-se, o destino selecionado precisa ter espaço suficiente para os dados. Recomendamos que você crie uma nova pasta para o destino.

1. Na página **Especificar opções de recuperação,** selecione as configurações de segurança. Em seguida, selecione se deve usar instantâneos de hardware baseados em SAN para uma recuperação mais rápida. Esta opção só está disponível se: 
    * Você tem uma SAN que fornece essa funcionalidade.
    * Você pode criar e dividir um clone para torná-lo gravável. 
    * O computador protegido e o servidor do Servidor de Backup estão conectados à mesma rede.

1. Configure opções de notificação. 
1. Na página **Confirmação**, selecione **Recuperar**.

Para executar o backup do Windows Server:

1. Selecione **ações** > **para recuperar** > **este servidor** > **em seguida**.

1. Selecione **Outro Servidor**, selecione a página **Especificar Tipo de Local** e, em seguida, selecione **Pasta compartilhada remota**. Digite o caminho para a pasta que contém o ponto de recuperação.

1. Na página **Selecionar Tipo de Recuperação**, selecione **Estado do Sistema**.

1. Na página **Selecionar Local para Recuperação do Estado do Sistema**, selecione **Local Original**.

1. Na página **Confirmação**, selecione **Recuperar**. 

1. Após a restauração, reinicie o servidor.

Você também pode executar a restauração do estado do sistema em um prompt de comando: 

1. Inicie o backup do Windows Server no computador que deseja recuperar. 

1. Para obter o identificador de versão, no prompt de comando, digite: 

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Use o identificador de versão para iniciar a restauração de estado do sistema. No prompt de comando, insira: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Confirme que você deseja iniciar a recuperação. Você pode ver o processo na janela do Prompt de Comando. Um log de restauração é criado. 

1. Após a restauração, reinicie o servidor.
