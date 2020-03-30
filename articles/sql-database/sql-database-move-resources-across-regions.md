---
title: Como mover recursos para outra região
description: Saiba como mover seu Banco de Dados SQL Do Azure, pool elástico Azure SQL ou instância gerenciada do Azure SQL para outra região.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821426"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Como mover recursos do Azure SQL para outra região

Este artigo ensina um fluxo de trabalho genérico para como mover seu banco de dados único do Banco de Dados SQL do Azure, pool elástico e instância gerenciada para uma nova região. 

## <a name="overview"></a>Visão geral

Existem vários cenários em que você gostaria de mover seus recursos Azure SQL existentes de uma região para outra. Por exemplo, você expande seu negócio para uma nova região e quer otimizá-lo para a nova base de clientes. Ou você precisa mudar as operações para uma região diferente por razões de conformidade. Ou o Azure lançou uma nova região que proporciona uma melhor proximidade e melhora a experiência do cliente.  

Este artigo fornece um fluxo de trabalho geral para a movimentação de recursos para uma região diferente. O fluxo de trabalho consiste nas seguintes etapas: 

- Verifique os pré-requisitos para a mudança 
- Prepare-se para mover os recursos no escopo
- Monitore o processo de preparação
- Teste o processo de movimento
- Inicie o movimento real 
- Remova os recursos da região de origem 


> [!NOTE]
> Este artigo se aplica às migrações dentro da nuvem pública do Azure, ou dentro da mesma nuvem soberana. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Mover banco de dados único

### <a name="verify-prerequisites"></a>Verificar pré-requisitos 

1. Crie um servidor lógico de destino para cada servidor de origem. 
1. Configure o firewall com as exceções certas usando [o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configure os servidores lógicos com os logins corretos. Se você não é o administrador de assinatura ou administrador do servidor SQL, trabalhe com o administrador para atribuir as permissões que você precisa. Para obter mais informações, consulte [Como gerenciar a segurança do banco de dados Azure SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md). 
1. Se seus bancos de dados estiverem criptografados com TDE e usarem sua própria chave de criptografia no cofre de chaves do Azure, certifique-se de que o material de criptografia correto seja provisionado nas regiões de destino. Para obter mais informações, consulte [a criptografia de dados transparente sql do Azure com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Se a auditoria em nível de banco de dados estiver ativada, desative-a e habilite a auditoria em nível de servidor. Após o failover, a auditoria do nível do banco de dados exigirá o tráfego transversal, que não será desejado ou possível após a mudança. 
1. Para auditorias em nível de servidor, certifique-se de que:
   - O contêiner de armazenamento, o Log Analytics ou o hub de eventos com os registros de auditoria existentes são movidos para a região de destino. 
   - A auditoria é configurada no servidor de destino. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing.md). 
1. Se a sua instância tiver uma política de retenção de longo prazo (LTR), os backups LTR existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para se mover entre a nuvem soberana e uma região pública. Essa migração exigirá a movimentação dos backups da LTR para o servidor de destino, que não é suportado no momento. 

### <a name="prepare-resources"></a>Preparar recursos

1. Crie um [grupo de failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) entre o servidor lógico da fonte para o servidor lógico do destino.  
1. Adicione os bancos de dados que deseja mover para o grupo de failover. 
    - A replicação de todos os bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [As melhores práticas para usar grupos de failover com bancos de dados únicos](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitore o processo de preparação

Você pode ligar periodicamente para [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída de inclui uma propriedade para o **Estado de replicação**: 
   - **ReplicationState = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser reprovado com segurança. 
   - **ReplicationState = 0** (SEEDING) indica que o banco de dados ainda não está semeado, e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Uma vez que `2` **o ReplicationState** esteja, conecte-se a cada `<fog-name>.secondary.database.windows.net` banco de dados ou subconjunto de bancos de dados usando o ponto final secundário e execute qualquer consulta contra os bancos de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento

1. Conecte-se ao servidor de `<fog-name>.secondary.database.windows.net`destino usando o ponto final secundário .
1. Use [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para alternar a instância secundária gerenciada para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai reverter. 
1. Verifique se o comando foi `nslook up <fog-name>.secondary.database.windows.net` concluído com sucesso usando para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-databases"></a>Remova os bancos de dados de origem

Uma vez que a mudança seja concluída, remova os recursos na região de origem para evitar cobranças desnecessárias. 

1. Exclua o grupo failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Exclua cada banco de dados de origem usando [remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) para cada um dos bancos de dados no servidor de origem. Isso encerrará automaticamente os links de geo-replicação. 
1. Exclua o servidor de origem usando [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre-chave, os contêineres de armazenamento de auditoria, o hub de eventos, a instância AAD e outros recursos dependentes para deixar de ser cobrado por eles. 

## <a name="move-elastic-pools"></a>Mova piscinas elásticas

### <a name="verify-prerequisites"></a>Verificar pré-requisitos 

1. Crie um servidor lógico de destino para cada servidor de origem. 
1. Configure o firewall com as exceções certas usando [o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configure os servidores lógicos com os logins corretos. Se você não é o administrador de assinatura ou administrador do servidor SQL, trabalhe com o administrador para atribuir as permissões que você precisa. Para obter mais informações, consulte [Como gerenciar a segurança do banco de dados Azure SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md). 
1. Se seus bancos de dados estiverem criptografados com TDE e usarem sua própria chave de criptografia no cofre de chaves do Azure, certifique-se de que o material de criptografia correto seja provisionado na região de destino.
1. Crie um pool elástico de destino para cada piscina elástica de origem, certificando-se de que a piscina seja criada no mesmo nível de serviço, com o mesmo nome e o mesmo tamanho. 
1. Se uma auditoria em nível de banco de dados estiver ativada, desative-a e habilite a auditoria em nível de servidor. Após o failover, a auditoria em nível de banco de dados exigirá tráfego transversal, o que não é desejado ou possível após a mudança. 
1. Para auditorias em nível de servidor, certifique-se de que:
    - O contêiner de armazenamento, o Log Analytics ou o hub de eventos com os registros de auditoria existentes são movidos para a região de destino.
    - A configuração de auditoria é configurada no servidor de destino. Para obter mais informações, consulte [auditoria de banco de dados SQL](sql-database-auditing.md).
1. Se a sua instância tiver uma política de retenção de longo prazo (LTR), os backups LTR existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para se mover entre a nuvem soberana e uma região pública. Essa migração exigirá a movimentação dos backups da LTR para o servidor de destino, que não é suportado no momento. 

### <a name="prepare-to-move"></a>Prepare-se para se mover
 
1.  Crie um grupo de [failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) separado entre cada pool elástico no servidor lógico de origem e seu pool elástico de contrapartida no servidor de destino. 
1.  Adicione todos os bancos de dados no pool ao grupo de failover. 
    - A replicação dos bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [as melhores práticas para grupos de failover com piscinas elásticas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Embora seja possível criar um grupo de failover que inclua vários pools elásticos, recomendamos fortemente que você crie um grupo de failover separado para cada pool. Se você tiver um grande número de bancos de dados em vários pools elásticos que você precisa mover, você pode executar as etapas de preparação em paralelo e, em seguida, iniciar a etapa de movimento em paralelo. Este processo irá escalar melhor e levará menos tempo em comparação com ter vários pools elásticos no mesmo grupo de failover. 

### <a name="monitor-the-preparation-process"></a>Monitore o processo de preparação

Você pode ligar periodicamente para [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída de inclui uma propriedade para o **Estado de replicação**: 
   - **ReplicationState = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser reprovado com segurança. 
   - **ReplicationState = 0** (SEEDING) indica que o banco de dados ainda não está semeado, e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste
 
Uma vez que `2` **o ReplicationState** esteja, conecte-se a cada `<fog-name>.secondary.database.windows.net` banco de dados ou subconjunto de bancos de dados usando o ponto final secundário e execute qualquer consulta contra os bancos de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento
 
1. Conecte-se ao servidor de `<fog-name>.secondary.database.windows.net`destino usando o ponto final secundário .
1. Use [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para alternar a instância secundária gerenciada para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai reverter. 
1. Verifique se o comando foi `nslook up <fog-name>.secondary.database.windows.net` concluído com sucesso usando para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-elastic-pools"></a>Remova as piscinas elásticas de origem
 
Uma vez que a mudança seja concluída, remova os recursos na região de origem para evitar cobranças desnecessárias. 

1. Exclua o grupo failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Exclua cada pool elástico de origem no servidor de origem usando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Exclua o servidor de origem usando [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre-chave, os contêineres de armazenamento de auditoria, o hub de eventos, a instância AAD e outros recursos dependentes para deixar de ser cobrado por eles. 

## <a name="move-managed-instance"></a>Mover instância gerenciada

### <a name="verify-prerequisites"></a>Verificar pré-requisitos
 
1. Para cada instância gerenciada por origem, crie uma instância gerenciada de destino do mesmo tamanho na região de destino.  
1. Configure a rede para uma instância gerenciada. Para obter mais informações, consulte [a configuração da rede](sql-database-howto-managed-instance.md#network-configuration).
1. Configure o banco de dados mestre de destino com os logins corretos. Se você não é o administrador de assinatura ou administrador do servidor SQL, trabalhe com o administrador para atribuir as permissões que você precisa. 
1. Se seus bancos de dados estiverem criptografados com O TDE e usarem sua própria chave de criptografia no cofre de chaves do Azure, certifique-se de que o AKV com chaves de criptografia idênticas exista nas regiões de origem e destino. Para obter mais informações, consulte [o TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Se a auditoria estiver ativada por exemplo, certifique-se de que:
    - O contêiner de armazenamento ou o centro de eventos com os registros existentes é movido para a região alvo. 
    - A auditoria é configurada na instância de destino. Para obter mais informações, consulte [auditoria com instância gerenciada](sql-database-managed-instance-auditing.md).
1. Se a sua instância tiver uma política de retenção de longo prazo (LTR), os backups LTR existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para se mover entre a nuvem soberana e uma região pública. Essa migração exigirá a movimentação dos backups da LTR para o servidor de destino, que não é suportado no momento. 

### <a name="prepare-resources"></a>Preparar recursos

Crie um grupo de failover entre cada instância de origem e a instância de destino correspondente.
    - A replicação de todos os bancos de dados em cada instância será iniciada automaticamente. Consulte [grupos de failover automático para](sql-database-auto-failover-group.md) obter mais informações.

 
### <a name="monitor-the-preparation-process"></a>Monitore o processo de preparação

Você pode ligar periodicamente para [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída de inclui uma propriedade para o **Estado de replicação**: 
   - **ReplicationState = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser reprovado com segurança. 
   - **ReplicationState = 0** (SEEDING) indica que o banco de dados ainda não está semeado, e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Uma vez que `2` **o ReplicationState** esteja, conecte-se a cada `<fog-name>.secondary.database.windows.net` banco de dados ou subconjunto de bancos de dados usando o ponto final secundário e execute qualquer consulta contra os bancos de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento 

1. Conecte-se ao servidor de `<fog-name>.secondary.database.windows.net`destino usando o ponto final secundário .
1. Use [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) para alternar a instância secundária gerenciada para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai reverter. 
1. Verifique se o comando foi `nslook up <fog-name>.secondary.database.windows.net` concluído com sucesso usando para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-managed-instances"></a>Remova as instâncias gerenciadas da fonte
Uma vez que a mudança seja concluída, remova os recursos na região de origem para evitar cobranças desnecessárias. 

1. Exclua o grupo failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Isso soltará a configuração do grupo failover e encerrará os links de georeplicação entre as duas instâncias. 
1. Exclua a instância gerenciada de origem usando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Remova quaisquer recursos adicionais no grupo de recursos, como o cluster virtual, a rede virtual e o grupo de segurança. 

## <a name="next-steps"></a>Próximas etapas 

[Gerencie](sql-database-manage-after-migration.md) seu banco de dados SQL do Azure assim que ele for migrado. 

