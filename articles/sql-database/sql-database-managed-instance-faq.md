---
title: Perguntas frequentes de instância gerenciada
description: Perguntas frequentes (FAQ) sobre a instância gerenciada do Banco de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 3ffa4bc905a08c1757865db7bab828193ff3c7ea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770135"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) sobre a instância gerenciada do Banco de Dados SQL

Este artigo contém muitas das perguntas mais comuns sobre [instância gerenciada do Banco de Dados SQL](sql-database-managed-instance.md).

## <a name="supported-features"></a>Recursos compatíveis

**Onde posso encontrar uma lista de recursos com suporte na instância gerenciada?**

Para obter uma lista dos recursos com suporte na instância gerenciada, consulte [Banco de Dados SQL do Azure versus SQL Server](sql-database-features.md).

Para obter diferenças na sintaxe e no comportamento entre a instância gerenciada do Banco de Dados SQL do Azure e o SQL Server local, consulte [Diferenças do T-SQL do SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Especificações técnicas e limites de recursos
 
**Onde posso encontrar características técnicas e limites de recursos para a instância gerenciada?**

Para obter as características de geração de hardware disponíveis, consulte as [diferenças técnicas em gerações de hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Para as camadas de serviço disponíveis e suas características, consulte as [diferenças técnicas entre as camadas de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemas conhecidos e bugs

**Onde posso encontrar problemas conhecidos e bugs?**

Para verificar problemas conhecidos e bugs, consulte [Problemas conhecidos](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Novos recursos

**Onde posso encontrar os recursos mais recentes e os recursos na visualização pública?**

Para obter novos recursos e versões prévias de recursos, consulte as [notas de versão](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo leva para criar ou atualizar a instância, ou para restaurar um banco de dados?**

O tempo esperado para criar uma nova instância gerenciada ou alterar a camada de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas [Operações de gerenciamento](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenção de nomenclatura

**Uma instância gerenciada pode ter o mesmo nome que o SQL Server local?**

Não há suporte para a alteração do nome da instância gerenciada.

A zona DNS padrão da instância gerenciada *.database.windows.net* pôde ser alterada. 

Para usar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use CliConfig para definir um alias. A ferramenta é apenas um wrapper de configurações do registro, para que ela também possa usar a política de grupo ou o script.
- Use *CNAME* com a opção *TrustServerCertificate = true*.

## <a name="move-db-from-mi"></a>Mover o BD da Instância Gerenciada 

**Como posso mover o banco de dados da instância gerenciada de volta para o SQL Server ou o Banco de Dados SQL do Azure?**

Você pode [exportar o banco de dados para o BACPAC](sql-database-export.md) e, em seguida, [importar o arquivo BACPAC]( sql-database-import.md). Essa é uma abordagem recomendada caso o banco de dados seja menor que 100 GB.

A replicação transacional poderá ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

Os backups `COPY_ONLY` nativos obtidos da instância gerenciada não podem ser restaurados para o SQL Server porque a instância gerenciada tem uma versão de banco de dados superior em comparação com SQL Server.

## <a name="migrate-instance-db"></a>Migrar BD de instância

**Como posso migrar meu banco de dados de instância para um único Banco de Dados SQL do Azure?**

Uma opção é [exportar o banco de dados para um BACPAC](sql-database-export.md) e, em seguida, [importar o arquivo BACPAC](sql-database-import.md). 

Essa é a abordagem recomendada caso o banco de dados seja menor que 100 GB. A replicação transacional poderá ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Alternar geração de hardware 

**Posso mudar minha geração de hardware de instância gerenciada entre Gen 4 e Gen 5 online?**

A alternância online automatizada entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região em que a instância gerenciada é provisionada. Nesse caso, você pode verificar a [página de visão geral do modelo vCore](sql-database-service-tiers-vcore.md) explicando como alternar entre as gerações de hardware.

Essa é uma operação de execução longa, pois uma nova instância gerenciada será provisionada em segundo plano e os bancos de dados serão transferidos automaticamente entre a instância antiga e a nova com um failover rápido no final do processo. 

**E se as duas gerações de hardware não tiverem suporte na mesma região?**

Se as duas gerações de hardware não tiverem suporte na mesma região, a alteração da geração de hardware é possível, mas deve ser feita manualmente. Isso exige que você provisione uma nova instância na região onde a geração de hardware desejada esteja disponível, fazendo backup e restauração manualmente dos dados entre a instância antiga e a nova.

**E se não houver endereços IP suficientes para executar a operação de atualização?**

Caso não haja endereços IP suficientes na sub-rede em que sua instância gerenciada é provisionada, você precisará criar uma nova sub-rede e uma nova instância gerenciada dentro dela. Também sugerimos que a nova sub-rede seja criada com mais endereços IP alocados para que as operações de atualização futuras evitem uma situação semelhante (para saber mais sobre o tamanho de sub-rede mais adequado, consulte [como determinar o tamanho da sub-rede vnet](sql-database-managed-instance-determine-size-vnet-subnet.md)). Depois que a nova instância for provisionada, você poderá fazer backup e restaurar dados manualmente entre a instância antiga e nova ou executar a [recuperação pontual](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Desempenho de ajuste

**Como fazer para ajustar o desempenho da minha instância gerenciada?**

A instância gerenciada de Uso Geral usa o armazenamento remoto porque o tamanho dos arquivos de dados e de log é importante para o desempenho. Para obter mais informações, consulte [Impacto do tamanho do arquivo de log no desempenho da Instância Gerenciada de Uso Geral](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se sua carga de trabalho consistir em muitas transações pequenas, considere alternar o tipo de conexão do proxy para o modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo de armazenamento

**Qual é o tamanho máximo de armazenamento para a instância gerenciada?**

O tamanho do armazenamento para a instância gerenciada depende da camada de serviço selecionada (Uso Geral ou Comercialmente Crítico). Para limitações de armazenamento dessas camadas de serviço, consulte [Características da camada de serviço](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Custo do armazenamento de backup 

**O armazenamento de backup foi deduzido do meu armazenamento de instância gerenciada?**

Não, o armazenamento de backup não é deduzido do seu espaço de armazenamento de instância gerenciada. O armazenamento de backup é independente do espaço de armazenamento da instância e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter o backup dos bancos de dados de instância, configuráveis de 7 a 35 dias. Para mais detalhes, consulte [Backups automatizados](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Rastrear cobrança

**Há uma maneira de acompanhar meu custo de cobrança para minha instância gerenciada?**

Você pode fazer isso usando a [Solução de Gerenciamento de Custos do Azure](/azure/cost-management/). Navegue até **Assinaturas** no [portal do Azure](https://portal.azure.com) e selecione **Análise de Custo**. 

Use a opção **Custos acumulados** e, em seguida, filtre pelo **Tipo de recurso** como `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Regras de NSG de entrada

**Como posso definir regras de NSG de entrada em portas de gerenciamento?**

O plano de controle de instância gerenciada mantém as regras NSG que protegem as portas de gerenciamento.

Veja quais portas de gerenciamento são usadas para:

As portas 9000 e 9003 são usadas pela infraestrutura do Service Fabric. A função primária do Service Fabric é manter o cluster virtual íntegro e manter o estado da meta em termos de número de réplicas de componente.

As portas 1438, 1440 e 1452 são usadas pelo agente de nó. O agente de nó é um aplicativo que é executado dentro do cluster e é usado pelo plano de controle para executar comandos de gerenciamento.

Além das regras NSG, o firewall interno protege a instância na camada de rede. Na camada de aplicativo, a comunicação é protegida com os certificados.
  
Para obter mais informações e saber como verificar o firewall interno, consulte [Firewall interno de instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Reduzir riscos de vazamento de dados  

**Como posso reduzir os riscos de vazamento de dados?**

Para reduzir os riscos de vazamento de dados, é recomendável que os clientes apliquem um conjunto de configurações e controles de segurança:

- Ative a [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todos os bancos de dados.
- Desative o CLR (Common Language Runtime). Isso também é recomendado no local.
- Use somente a autenticação do AAD (Azure Active Directory).
- Instância de acesso com conta DBA de privilégios baixos.
- Configure o acesso ao JiT jumpbox para a conta sysadmin.
- Ative a [auditoria do SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) e integre-a nos mecanismos de alerta.
- Ative a [Detecção de Ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) do pacote [ADS (Segurança de Dados Avançada)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="cost-saving-use-cases"></a>Casos de uso de economia de custos

**Onde posso encontrar casos de uso e economias de custos resultantes com a instância gerenciada?**

Estudos de caso de instância gerenciada:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Para obter uma melhor compreensão dos benefícios, custos e riscos associados à implantação da instância gerenciada do Banco de Dados SQL do Azure, também há um estudo da Forrester: [Impacto econômico total de Instância Gerenciada](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização de DNS 

**Posso fazer a atualização de DNS?**

No momento, não fornecemos um recurso para atualizar a configuração do servidor DNS para a instância gerenciada.

A configuração de DNS é, por fim, atualizada:

- Quando a concessão de DHCP expirar.
- Na atualização da plataforma.

Como alternativa, faça o downgrade da instância gerenciada para 4 vCore e atualize-a novamente depois. Isso tem um efeito colateral de atualizar a configuração de DNS.


## <a name="ip-address"></a>Endereço IP

**Posso me conectar à instância gerenciada usando o endereço IP?**

Não há suporte para se conectar à instância gerenciada usando o endereço IP. O nome do host da instância gerenciada mapeia para o balanceador de carga na frente do cluster virtual da instância gerenciada. Como um cluster virtual pode hospedar várias instâncias gerenciadas, a conexão não pode ser roteada para uma instância gerenciada adequada sem especificar seu nome.

Para obter mais informações sobre a arquitetura de cluster virtual de instância gerenciada, consulte [Arquitetura de conectividade de cluster virtual](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Uma instância gerenciada pode ter um endereço IP estático?**

Em situações raras, mas necessárias, talvez seja preciso fazer uma migração online de uma instância gerenciada para um novo cluster virtual. Se necessário, essa migração ocorre devido a alterações em nossa pilha de tecnologia destinadas a melhorar a segurança e a confiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome do host da instância gerenciada. O serviço de instância gerenciada não alega suporte a endereços IP estáticos e reserva o direito de alterá-lo sem aviso como parte dos ciclos de manutenção regulares.

Por esse motivo, é altamente recomendável depender da imutabilidade do endereço IP, pois isso poderia causar um tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Alterar fuso horário

**Posso alterar o fuso horário de uma instância gerenciada existente?**

A configuração de fuso horário pode ser definida quando uma instância gerenciada é provisionada pela primeira vez. Não há suporte para a alteração do fuso horário da instância gerenciada existente. Para obter detalhes, consulte [limitações de fuso horário](sql-database-managed-instance-timezone.md#limitations).

As soluções alternativas incluem a criação de uma nova instância gerenciada com o fuso horário adequado. Em seguida, execute um backup e restauração manuais ou, o que é recomendável, execute uma [restauração pontual de instância cruzada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como fazer para resolver problemas de desempenho com minha instância gerenciada?**

Para uma comparação de desempenho entre a instância gerenciada e o SQL Server, um bom ponto de partida é o artigo [Práticas recomendadas para comparação de desempenho entre a instância gerenciada do Azure SQL e o SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

O carregamento de dados geralmente é mais lento na instância gerenciada do que no SQL Server devido ao modelo de recuperação completa obrigatório e aos [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na taxa de transferência de gravação do log de transações. Às vezes, isso pode ser solucionado carregando dados transitórios no tempdb, em vez de no banco de dados do usuário, ou usando o columnstore clusterizado ou tabelas com otimização de memória.


## <a name="restore-encrypted-backup"></a>Restaurar backup criptografado

**Posso restaurar meu banco de dados criptografado para a instância gerenciada?**

Sim, você não precisa descriptografar seu banco de dados para poder restaurá-lo na instância gerenciada. Você precisa fornecer um certificado/chave usado como um protetor de chave de criptografia no sistema de origem para a instância gerenciada para poder ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- *Carregue o protetor de certificado para a instância gerenciada*. Isso só pode ser feito usando o PowerShell. O [script de exemplo](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Carregue o protetor de chave assimétrica no Azure Key Vault (AKV) e aponte e instância gerenciada para ele*. Essa abordagem é semelhante ao caso de uso da TDE do BYOK (traga sua própria chave), que também usa a integração do AKV para armazenar a chave de criptografia. Se você não quiser usar a chave como um protetor de chave de criptografia e apenas quiser disponibilizar a chave para a instância gerenciada para restaurar bancos de dados criptografados, siga as instruções para [configurar o TDE do BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption) e não marque a caixa de seleção *Tornar a chave selecionada o protetor de TDE padrão*.

Depois de disponibilizar o protetor de criptografia para a instância gerenciada, você pode prosseguir com o procedimento de restauração do banco de dados padrão.

## <a name="migrate-from-single-db"></a>Migrar de um único BD 

**Como posso migrar do Banco de Dados SQL do Azure ou de um pool elástico para uma instância gerenciada?**

A instância gerenciada oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que outras opções de implantação do Banco de Dados SQL do Azure. Se você quiser consolidar dados em uma única instância ou se simplesmente precisar de um recurso com suporte exclusivo na instância gerenciada, poderá migrar seus dados usando a funcionalidade de exportação/importação (BACPAC).
