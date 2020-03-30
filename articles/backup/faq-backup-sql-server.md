---
title: FAQ - Backup de bancos de dados SQL Server em VMs do Azure
description: Encontre respostas para perguntas comuns sobre o backup de bancos de dados SQL Server em VMs do Azure com o Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247703"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>FAQ sobre bancos de dados SQL Server que estão sendo executados em um backup azure VM

Este artigo responde a perguntas comuns sobre o backup de bancos de dados SQL Server que são executados em máquinas virtuais (VMs) do Azure e usam o serviço de backup do [Azure.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Posso usar o backup do Azure para O VM IaaS, bem como o SQL Server na mesma máquina?

Sim, você pode ter backup vm e backup SQL na mesma VM. Neste caso, acionamos internamente o backup completo somente de cópia na VM para não truncar os logs.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução tenta novamente ou cura automaticamente os backups?

Em algumas circunstâncias, o serviço de backup do Azure aciona backups corretivos. A cura automática pode acontecer para qualquer uma das seis condições mencionadas abaixo:

- Se o log ou backup diferencial falhar devido ao erro de validação do LSN, o próximo log ou backup diferencial será convertido em um backup completo.
- Se nenhum backup completo tiver acontecido antes de um log ou backup diferencial, esse log ou backup diferencial será convertido em um backup completo.
- Se o ponto de ponto do backup completo mais recente for superior a 15 dias, o próximo log ou backup diferencial será convertido em um backup completo.
- Todos os trabalhos de backup que são cancelados devido a uma atualização de extensão são reativados após a atualização ser concluída e a extensão é iniciada.
- Se você optar por substituir o banco de dados durante a Restauração, o próximo backup de log/diferencial será acionado e um backup completo será acionado.
- Nos casos em que um backup completo é necessário para redefinir as cadeias de log devido à alteração no modelo de recuperação do banco de dados, um completo é acionado automaticamente no próximo cronograma.

A cura automática como um recurso é habilitada para todos os usuários por padrão; No entanto, no caso de você optar por optar por não optá-lo, em seguida, execute o abaixo:

- Na instância do SQL Server, na pasta *C:\Program Files\Azure Workload Backup\bin,* crie ou edite o arquivo **ExtensionSettingsOverrides.json.**
- No **ExtensionSettingsOverrides.json**, set *{"EnableAutoHealer": false}*.
- Salve suas alterações e feche o arquivo.
- Na instância do SQL Server, abra o **Manage de tarefas** e, em seguida, reinicie o serviço **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Posso controlar quantos backups simultâneos são executados no servidor SQL?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server. Para alterar a configuração:

1. Na instância sql server, na pasta *C:\Program Files\Azure Workload Backup\bin,* crie o arquivo *ExtensionSettingsOverrides.json.*
2. No arquivo *ExtensionSettingsOverrides.json,* altere a configuração **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
O valor padrão do DefaultBackupTasksThreshold é **20**.

3. Salve suas alterações e feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Embora este método ajude se o aplicativo de backup estiver consumindo uma grande quantidade de recursos, o SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) é uma maneira mais genérica de especificar limites na quantidade de CPU, IO físico e memória que as solicitações de aplicativos recebidas podem usar.

> [!NOTE]
> No UX você ainda pode ir em frente e agendar quantos backups a qualquer momento, no entanto eles serão processados em uma janela deslizante de dizer, 5, conforme o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?

De acordo com as limitações do SQL, você pode executar o backup somente do Copy Full na réplica secundária; no entanto, o backup completo não é permitido.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger grupos de disponibilidade no local?

Não. O Azure Backup protege os bancos de dados SQL Server em execução no Azure. Se um grupo de disponibilidade (AG) for espalhado entre o Azure e as máquinas locais, o AG só poderá ser protegido se a réplica principal estiver sendo distribuída no Azure. Além disso, o Azure Backup protege apenas os nós executados na mesma região do Azure que o cofre dos Serviços de Recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger grupos de disponibilidade entre regiões?

O cofre azure Backup Recovery Services pode detectar e proteger todos os nós que estão na mesma região que o cofre. Se o grupo de disponibilidade do SQL Server Always On abrange várias regiões do Azure, configure o backup da região que possui o nó principal. O Backup do Microsoft Azure poderá detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a sua preferência de backup. Quando sua preferência de backup não é atendida, os backups falham e você recebe o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas de portais é documentado [aqui.](backup-azure-monitoring-built-in-monitor.md) No entanto, caso você esteja interessado em ter alertas mesmo para trabalhos bem-sucedidos, você pode usar [o Monitoramento usando o Monitor Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de backup programados no menu Backup Jobs?

O menu **Backup Job** só mostrará trabalhos de backup demanda. Para o trabalho programado, use [o Monitoramento usando o Monitor Do Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

Sim, você pode alcançar esse recurso com [proteção automática.](backup-sql-server-database-azure-vms.md#enable-auto-protection)  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância autoprotegida, o que acontecerá com os backups?

Se um banco de dados for descartado de uma instância de proteção automática, os backups do banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a aparecer como insalubre em **Itens de Backup** e ainda está protegido.

A maneira correta de parar de proteger esse banco de dados é fazer **stop backup** com dados **de exclusão** neste banco de dados.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se eu parar a operação de backup de um banco de dados autoprotegido, qual será o seu comportamento?

Se você **parar o backup com dados retidos,** nenhum backup futuro ocorrerá e os pontos de recuperação existentes permanecerão intactos. O banco de dados ainda será considerado protegido e será mostrado os **itens de backup**.

Se você **parar o backup com dados de exclusão,** não haverá backups futuros e os pontos de recuperação existentes também serão excluídos. O banco de dados será considerado não protegido e será mostrado a instância no Configure Backup. No entanto, ao contrário de outros bancos de dados protegidos que podem ser selecionados manualmente ou que podem ser protegidos automaticamente, esse banco de dados parece acinzentado e não pode ser selecionado. A única maneira de reproteger esse banco de dados é desativar a proteção automática na instância. Agora você pode selecionar esse banco de dados e configurar proteção nele ou reativar a proteção automática na instância novamente.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu mudar o nome do banco de dados depois de protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo banco de dados. Assim, o serviço tratará essa situação como se o banco de dados não fosse encontrado e com falha nos backups.

Você pode selecionar o banco de dados, que agora é renomeado e configurar proteção nele. Caso a proteção automática esteja ativada na instância, o banco de dados renomeado será automaticamente detectado e protegido.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não posso ver um banco de dados adicionado para uma instância autoprotegida?

Um banco de dados que você [adiciona a uma instância de autoprotegido](backup-sql-server-database-azure-vms.md#enable-auto-protection) pode não aparecer imediatamente em itens protegidos. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, você pode descobrir e proteger novos bancos de dados imediatamente se você executar manualmente uma descoberta selecionando **DBs de redescoberta,** como mostrado na imagem a seguir:

  ![Descubra manualmente um banco de dados recém-adicionado](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Próximas etapas

Aprenda a fazer backup de [um banco de dados SQL Server](backup-azure-sql-database.md) que está sendo executado em uma VM Azure.
