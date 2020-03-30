---
title: Criptografia de dados transparente gerenciada pelo cliente (TDE)
description: Traga seu suporte de chave própria (BYOK) para criptografia de dados transparente (TDE) com o Azure Key Vault para banco de dados SQL e Synapse Azure. Visão geral de TDE com BYOK, benefícios, como funciona, considerações e recomendações.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528721"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Criptografia de dados transparente do Azure SQL com chave gerenciada pelo cliente

O Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) com chave gerenciada pelo cliente permite o cenário Bring Your Own Key (BYOK) para proteção de dados em repouso e permite que as organizações implementem a separação de deveres no gerenciamento de chaves e dados. Com a criptografia de dados transparente gerenciada pelo cliente, o cliente é responsável e em um controle completo de um gerenciamento de ciclo de vida chave (criação de chaves, upload, rotação, exclusão), permissões de uso chave e auditoria das operações nas chaves.

Neste cenário, a chave usada para criptografia da Key de Criptografia de Banco de Dados (DEK), chamada protetor tde, é uma chave assimétrica gerenciada pelo cliente armazenada em um [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)de propriedade do cliente e gerenciado pelo cliente, um sistema de gerenciamento de chaves externas baseado na nuvem. O Key Vault é um armazenamento seguro altamente disponível e escalável para chaves criptográficas RSA, opcionalmente apoiado por módulos de segurança de hardware validados pelo FIPS 140-2 Nível 2 (HSMs). Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia/descriptografia usando a chave para as entidades autorizadas. A chave pode ser gerada pelo cofre da chave, importada ou [transferida para o cofre-chave a partir de um dispositivo HSM on-prem](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Para o Banco de Dados SQL do Azure e o Azure Synapse, o protetor TDE é definido no nível lógico do servidor e é herdado por todos os bancos de dados criptografados associados a esse servidor. Para a instância gerenciada do Azure SQL, o protetor TDE é definido no nível de ocorrência e é herdado por todos os bancos de dados criptografados nessa instância. O *servidor* de termo refere-se tanto ao servidor lógico do Banco de Dados SQL quanto à instância gerenciada ao longo deste documento, a menos que seja indicado de forma diferente.

> [!IMPORTANT]
> Para aqueles que usam o TDE gerenciado por serviços que gostariam de começar a usar o TDE gerenciado pelo cliente, os dados permanecem criptografados durante o processo de troca, e não há tempo de inatividade nem recriptografia dos arquivos do banco de dados. Mudar de uma chave gerenciada por serviço para uma chave gerenciada pelo cliente requer apenas uma recriptografia do DEK, que é uma operação rápida e on-line.

## <a name="benefits-of-the-customer-managed-tde"></a>Benefícios do TDE gerenciado pelo cliente

O TDE gerenciado pelo cliente oferece os seguintes benefícios ao cliente:

- Controle total e granular sobre o uso e gestão do protetor TDE;

- Transparência do uso do protetor TDE;

- Capacidade de implementar a separação de deveres na gestão de chaves e dados dentro da organização;

- O administrador do Key Vault pode revogar as permissões de acesso de chaves para tornar o banco de dados criptografado inacessível;

- Gerenciamento central de chaves em AKV;

- Maior confiança de seus clientes finais, uma vez que a AKV foi projetada de forma que a Microsoft não possa ver nem extrair chaves de criptografia;

## <a name="how-customer-managed-tde-works"></a>Como funciona o TDE gerenciado pelo cliente

![Configuração e funcionamento do TDE gerenciado pelo cliente](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Para que o servidor possa usar o protetor TDE armazenado no AKV para criptografia do DEK, o administrador do cofre chave precisa dar os seguintes direitos de acesso ao servidor usando sua identidade AAD exclusiva:

- **obter** - para recuperar a parte pública e propriedades da chave no Cofre chave

- **wrapKey** - para ser capaz de proteger (criptografar) DEK

- **desembrulheKey** - para ser capaz de desproteger (descriptografar) DEK

O administrador do cofre chave também pode [habilitar o registro de eventos de auditoria do cofre de chaves,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)para que eles possam ser auditados posteriormente.

Quando o servidor é configurado para usar um protetor TDE da AKV, o servidor envia o DEK de cada banco de dados habilitado para TDE para o cofre de chaves para criptografia. O cofre-chave retorna o DEK criptografado, que é armazenado no banco de dados do usuário.

Quando necessário, o servidor envia DEK protegido para o cofre de chaves para descriptografia.

Os auditores podem usar o Azure Monitor para revisar os registros do Key Vault AuditEvent, se o registro estiver ativado.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisitos para configurar o TDE gerenciado pelo cliente

### <a name="requirements-for-configuring-akv"></a>Requisitos para configurar AKV

- O cofre-chave e a instância gerenciada do Banco de Dados/Controle de SQL devem pertencer ao mesmo inquilino do Azure Active Directory. Não há suporte para interações de servidor e cofre de chaves entre locatários. Para mover recursos posteriormente, o TDE com AKV terá que ser reconfigurado. Saiba mais sobre [a movimentação de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- O recurso [de exclusão suave](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ativado no cofre da chave, para proteger contra a perda de dados a exclusão acidental da chave (ou cofre de chaves) acontece. Os recursos suprimidos são retidos por 90 dias, a menos que sejam recuperados ou purgados pelo cliente nesse meio tempo. As ações de *recuperação* e *limpeza* têm suas próprias permissões associadas em uma política de acesso do cofre de chaves. O recurso de exclusão suave está desligado por padrão e pode ser ativado via [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) ou [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Ele não pode ser habilitado através do portal Azure.  

- Conceda ao servidor do Banco de Dados SQL ou acesso de instância gerenciada ao cofre de chaves (get, wrapKey, deswrapKey) usando sua identidade azure Active Directory. Ao usar o portal Azure, a identidade Azure AD é criada automaticamente. Ao usar o PowerShell ou o CLI, a identidade Azure AD deve ser explicitamente criada e a conclusão deve ser verificada. Consulte [Configurar a TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurar a TDE com BYOK para a Instância Gerenciada](https://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao usar o PowerShell.

- Ao usar firewall com AKV, você deve habilitar a opção *Permitir que serviços confiáveis da Microsoft contornem o firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Requisitos para configurar o protetor TDE

- O protetor TDE pode ser apenas assimétrico, a tecla RSA 2048 ou RSA HSM 2048.

- A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.

- A chave deve estar no estado *habilitado.*

- Se você estiver importando a chave existente no cofre de chaves, certifique-se de fornecê-la nos formatos de arquivo suportados (.pfx, .byok ou .backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Recomendações ao configurar o TDE gerenciado pelo cliente

### <a name="recommendations-when-configuring-akv"></a>Recomendações ao configurar AKV

- Associar-se no máximo 500 bancos de dados General Purpose ou 200 Business Critical no total com um cofre chave em uma única assinatura para garantir alta disponibilidade quando o servidor acessar o protetor TDE no cofre principal. Esses números são baseados na experiência e documentados nos limites de serviço do [cofre chave.](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) A intenção aqui é evitar problemas após o failover do servidor, pois ele desencadeará tantas operações-chave contra o cofre quanto há bancos de dados nesse servidor.

- Defina um bloqueio de recursos no cofre de chaves para controlar quem pode excluir esse recurso crítico e evitar a exclusão acidental ou não autorizada. Saiba mais sobre [bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Habilite a auditoria e o relatório em todas as chaves de criptografia: o cofre de chaves fornece registros fáceis de injetar em outras ferramentas de gerenciamento de informações de segurança e eventos. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) é um exemplo de um serviço que já está integrado.

- Vincule cada servidor com dois cofres-chave que residem em regiões diferentes e mantenham o mesmo material-chave, para garantir alta disponibilidade de bancos de dados criptografados. Marque apenas a chave do cofre da chave na mesma região que um protetor TDE. O sistema mudará automaticamente para o cofre de chaves na região remota se houver uma paralisação que afete o cofre da chave na mesma região.

### <a name="recommendations-when-configuring-tde-protector"></a>Recomendações ao configurar o protetor TDE
- Mantenha uma cópia do protetor TDE em um local seguro ou deposite-a no serviço de custódia.

- Se a chave for gerada no cofre de chaves, crie um backup de chave antes de usar a chave no AKV pela primeira vez. O backup pode ser restaurado apenas para um Cofre de Chaves Azure. Saiba mais sobre o [comando Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Crie um novo backup sempre que quaisquer alterações forem feitas na chave (por exemplo, atributos-chave, tags, ACLs).

- **Mantenha as versões anteriores** da chave no cofre de chaves ao fazer rotação de chaves para que os backups mais antigos do banco de dados possam ser restaurados. Quando o protetor TDE é alterado para um banco de dados, backups antigos do banco de dados **não são atualizados** para usar o protetor TDE mais recente. No momento da restauração, cada backup precisa do protetor TDE com o que foi criptografado no momento da criação. As rotações de chave podem ser executadas seguindo as instruções em [Girar o Protetor de Transparent Data Encryption usando PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Mantenha todas as chaves usadas anteriormente no AKV mesmo depois de mudar para chaves gerenciadas por serviço. Ele garante que os backups do banco de dados possam ser restaurados com os protetores TDE armazenados em AKV.  Os protetores TDE criados com o Azure Key Vault devem ser mantidos até que todos os backups armazenados restantes tenham sido criados com chaves gerenciadas por serviço. Faça cópias de backup recuperáveis dessas chaves usando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga as etapas da [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protetor TDE inacessível

Quando a criptografia de dados transparente é configurada para usar uma chave gerenciada pelo cliente, o acesso contínuo ao protetor TDE é necessário para que o banco de dados permaneça on-line. Se o servidor perder o acesso ao protetor TDE gerenciado pelo cliente no AKV, em até 10 minutos um banco de dados começará a negar todas as conexões com a mensagem de erro correspondente e mudará seu estado para *Inacessível*. A única ação permitida em um banco de dados no estado inacessível é excluí-lo.

> [!NOTE]
> Se o banco de dados estiver inacessível devido a uma paralisação de rede intermitente, não há nenhuma ação necessária e os bancos de dados voltarão a funcionar automaticamente.

Depois que o acesso à chave é restaurado, retomar o banco de dados on-line requer tempo e etapas adicionais, que podem variar de acordo com o tempo decorrido sem acesso à chave e ao tamanho dos dados no banco de dados:

- Se o acesso à chave for restaurado dentro de 8 horas, o banco de dados se curará automaticamente dentro de uma hora.

- Se o acesso à chave for restaurado após mais de 8 horas, a cura automática não é possível e trazer o banco de dados de volta requer etapas adicionais no portal e pode levar uma quantidade significativa de tempo, dependendo do tamanho do banco de dados. Uma vez que o banco de dados esteja novamente on-line, as configurações de nível de servidor configuradas anteriormente, como configuração [de grupo de failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) histórico de restauração de ponto no tempo e tags **serão perdidas**. Portanto, recomenda-se a implementação de um sistema de notificação que permite identificar e resolver os principais problemas de acesso subjacentes dentro de 8 horas.

### <a name="accidental-tde-protector-access-revocation"></a>Revogação acidental do acesso ao protetor TDE

Pode acontecer de alguém com direitos de acesso suficientes ao cofre de chaves acidentalmente desativar o acesso do servidor à chave por:

- revogando *as*permissões get , *wrapKey,* *desembrulheKey* do servidor

- excluindo a chave

- excluindo o cofre de chaves

- mudando as regras de firewall do cofre de chaves

- excluindo a identidade gerenciada do servidor no Azure Active Directory

Saiba mais sobre [as causas comuns para o banco de dados se tornar inacessível](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoramento do TDE gerenciado pelo cliente

Para monitorar o estado do banco de dados e permitir alertar para a perda de acesso ao protetor TDE, configure os seguintes recursos do Azure:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Um banco de dados inacessível que perdeu o acesso ao protetor TDE será exibido como "Indisponível" após a primeira conexão ao banco de dados ter sido negada.
- [Registro de atividades](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) Quando o acesso ao protetor TDE no cofre de chaves gerenciado pelo cliente falha, as entradas são adicionadas ao registro de atividades.  A criação de alertas para esses eventos permitirá que você reintegre o acesso o mais rápido possível.
- [Grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) podem ser definidos para enviar notificações e alertas com base em suas preferências, por exemplo, E-mail/SMS/Push/Voice, Logic App, Webhook, ITSM ou Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup e restauração de banco de dados com TDE gerenciado pelo cliente

Uma vez que um banco de dados é criptografado com TDE usando uma chave do Key Vault, quaisquer backups recém-gerados também são criptografados com o mesmo protetor TDE. Quando o protetor TDE é alterado, backups antigos do banco de dados **não são atualizados** para usar o protetor TDE mais recente.

Para restaurar um backup criptografado com um protetor TDE do Key Vault, certifique-se de que o material-chave esteja disponível para o servidor de destino. Portanto, recomendamos que você mantenha todas as versões antigas do protetor TDE no cofre de chaves, para que os backups do banco de dados possam ser restaurados.

> [!IMPORTANT]
> A qualquer momento não pode haver mais do que um protetor TDE definido para um servidor. É a chave marcada com "Faça da chave o protetor TDE padrão" na lâmina do portal Azure. No entanto, várias chaves adicionais podem ser vinculadas a um servidor sem marcá-las como um protetor TDE. Essas chaves não são usadas para proteger o DEK, mas podem ser usadas durante a restauração de um backup, se o arquivo de backup for criptografado com a chave com a impressão digital correspondente.

Se a chave necessária para restaurar um backup não estiver mais disponível no servidor de destino, a `<Servername>` seguinte mensagem de erro será devolvida \<na tentativa de \<restauração: "O servidor de destino não tem acesso a todas as URIs AKV criadas entre> #1 de> de data-hora e> de #2 de carimbo de hora. Por favor, tente novamente a operação depois de restaurar todas as URIs AKV."

Para atenuá-lo, execute o [cmdlet Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para servidor lógico sql de banco de dados de destino ou [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) para que a instância gerenciada de destino retorne a lista de chaves disponíveis e identifique as que faltam. Para garantir que todos os backups possam ser restaurados, certifique-se de que o servidor de destino para a restauração tenha acesso a todas as chaves necessárias. Essas chaves não precisam ser marcadas como protetora TDE.

Para saber mais sobre a recuperação de backup do Banco de Dados SQL, consulte [Recuperar um banco de dados SQL do Azure](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de backup do Pool SQL, consulte [Recuperar um Pool SQL](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Para obter o backup/restauração nativo do SQL Server com instância gerenciada, consulte [Quickstart: Restaure um banco de dados em uma instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Consideração adicional para arquivos de log: Os arquivos de log de backup permanecem criptografados com o protetor TDE original, mesmo que ele tenha sido girado e o banco de dados esteja usando um novo protetor TDE.  No momento da restauração, ambas as chaves serão necessárias para restaurar o banco de dados.  Se o arquivo de log estiver usando um protetor TDE armazenado no Azure Key Vault, essa chave será necessária no momento da restauração, mesmo que o banco de dados tenha sido alterado para usar o TDE gerenciado por serviço sem tempo.

## <a name="high-availability-with-customer-managed-tde"></a>Alta disponibilidade com TDE gerenciado pelo cliente

Mesmo nos casos em que não há geo-redundância configurada para servidor, é altamente recomendável configurar o servidor para usar dois cofres de chaves diferentes em duas regiões diferentes com o mesmo material chave. Ele pode ser realizado criando um protetor TDE usando o cofre de chave principal co-localizado na mesma região que o servidor e clonando a chave em um cofre de chaves em uma região Diferente do Azure, de modo que o servidor tenha acesso a um segundo cofre de chaves caso o cofre principal seja experimentar uma paralisação enquanto o banco de dados está em funcionamento.

Use o cmdlet Backup-AzKeyVaultKey para recuperar a chave em formato criptografado do cofre de chaves principal e, em seguida, use o cmdlet Restore-AzKeyVaultKey e especifique um cofre de chaves na segunda região para clonar a chave. Alternativamente, use o portal Azure para fazer backup e restaurar a chave. A chave no cofre de chaves secundária em outra região não deve ser marcada como protetor TDE, e nem sequer é permitida.

 Se houver uma paralisação que afete o cofre de chaves primárias, e só então, o sistema mudará automaticamente para a outra chave vinculada com a mesma impressão digital no cofre de chaves secundária, se existir. Observe que esse switch não acontecerá se o protetor TDE estiver inacessível por causa dos direitos de acesso revogados ou porque a chave ou o cofre da chave for excluído, pois pode indicar que o cliente intencionalmente queria restringir o acesso ao servidor da chave.

![HA de servidor único](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR e TDE gerenciado pelo cliente

Em ambos os cenários [ativos de georeplicação](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) e [grupos de failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) cada servidor envolvido requer um cofre de chaves separado, que deve ser co-localizado com o servidor na mesma região do Azure. O cliente é responsável por manter o material-chave nos cofres das chaves consistentemente, de modo que o geosecundário esteja em sincronia e possa assumir usando a mesma chave de seu cofre de chaves local se o primário se tornar inacessível devido a uma paralisação na região e um failover for acionado . Até quatro secundários podem ser configurados, e o encadeamento (secundários de secundários) não é suportado.

Para evitar problemas durante o estabelecimento ou durante a replicação geográfica devido ao material-chave incompleto, é importante seguir essas regras ao configurar o TDE gerenciado pelo cliente:

- Todos os cofres principais envolvidos devem ter as mesmas propriedades e os mesmos direitos de acesso para os respectivos servidores.

- Todos os cofres-chave envolvidos devem conter material chave idêntico. Aplica-se não apenas ao protetor TDE atual, mas aos todos os protetores TDE anteriores que podem ser usados nos arquivos de backup.

- Tanto a configuração inicial quanto a rotação do protetor TDE devem ser feitas primeiro no secundário e, em seguida, na primária.

![Grupos de failover e Geo-DR](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Para testar um failover, siga as etapas em [visão geral de georeplicação ativa](sql-database-geo-replication-overview.md). Deve ser feito regularmente para confirmar que as permissões de acesso para SQL para ambos os cofres principais foram mantidas.

## <a name="next-steps"></a>Próximas etapas

Você também pode querer verificar os seguintes scripts de amostra powershell para as operações comuns com TDE gerenciado pelo cliente:

- [Gire o protetor de criptografia de dados transparente para banco de dados SQL usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Remova um protetor TDE (Transparent Data Encryption, criptografia de dados transparente) para o banco de dados SQL usando o PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Gerencie criptografia de dados transparente em uma instância gerenciada com sua própria chave usando o PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
