---
title: Perguntas frequentes de instância gerenciada
description: SQL Database instância gerenciada freqüentemente perguntas (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364155"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database instância gerenciada freqüentemente perguntas (FAQ)

Este artigo contém muitas das perguntas mais comuns sobre [a instância gerenciada do SQL Database](sql-database-managed-instance.md).

## <a name="supported-features"></a>Recursos compatíveis

**Onde posso encontrar uma lista de recursos suportados em instância gerenciada?**

Para obter uma lista de recursos suportados na instância gerenciada, consulte [Azure SQL Database versus SQL Server](sql-database-features.md).

Para obter diferenças de sintaxe e comportamento entre a instância gerenciada do Banco de Dados SQL do Azure e o SQL Server no local, consulte [as diferenças T-SQL do SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Especificação técnica & limites de recursos
 
**Onde posso encontrar características técnicas e limites de recursos para instância gerenciada?**

Para as características de geração de hardware disponíveis, veja [diferenças técnicas nas gerações de hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Para os níveis de serviço disponíveis e suas características, consulte [diferenças técnicas entre os níveis de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemas conhecidos & bugs

**Onde posso encontrar problemas conhecidos e bugs?**

Para bugs e problemas conhecidos, consulte [problemas conhecidos](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Novos recursos

**Onde posso encontrar os recursos mais recentes e os recursos na pré-visualização pública?**

Para recursos novos e visualizados, consulte [notas de versão](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo leva para criar ou atualizar a instância ou restaurar um banco de dados?**

O tempo esperado para criar uma nova instância gerenciada ou alterar o nível de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas [operações de gestão](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenção de nomenclatura

**Uma instância gerenciada pode ter o mesmo nome do SQL Server no local?**

A alteração do nome da instância gerenciada não é suportada.

A região de DNS padrão de instância gerenciada *.database.windows.net* pode ser alterada. 

Para usar outra zona De DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use CliConfig para definir um alias. A ferramenta é apenas um invólucro de configurações de registro, para que possa ser feito usando política de grupo ou script também.
- Use *CNAME* com *TrustServerCertificate=opção verdadeira.*

## <a name="move-db-from-mi"></a>Mover DB de MI 

**Como posso mover o banco de dados da instância gerenciada de volta para o SQL Server ou o Azure SQL Database?**

Você pode [exportar banco de dados para bacpac](sql-database-export.md) e, em seguida, importar o arquivo [BACPAC]( sql-database-import.md). Esta é uma abordagem recomendada se o seu banco de dados for menor que 100 GB.

A replicação transacional pode ser usada se todas as tabelas do banco de dados tiverem chaves primárias.

Os `COPY_ONLY` backups nativos retirados da instância gerenciada não podem ser restaurados no SQL Server porque a instância gerenciada tem uma versão de banco de dados mais alta em comparação com o SQL Server.

## <a name="migrate-instance-db"></a>Migração de exemplo DB

**Como posso migrar meu banco de dados de instâncias para um único banco de dados SQL do Azure?**

Uma opção é [exportar o banco de dados para um BACPAC](sql-database-export.md) e, em seguida, importar o arquivo [BACPAC](sql-database-import.md). 

Esta é a abordagem recomendada se o seu banco de dados for menor que 100 GB. A replicação transacional pode ser usada se todas as tabelas do banco de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Alternar geração de hardware 

**Posso alternar minha geração de hardware de instância gerenciada entre gen 4 e Gen 5 on-line?**

A comutação on-line automatizada entre as gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região onde sua instância gerenciada está provisionada. Neste caso, você pode verificar a [página de visão geral do modelo vCore](sql-database-service-tiers-vcore.md) explicando como alternar entre as gerações de hardware.

Esta é uma operação de longa duração, pois uma nova instância gerenciada será provisionada em segundo plano e os bancos de dados transferidos automaticamente entre a instância antiga e a nova com um failover rápido no final do processo. 

Se ambas as gerações de hardware não forem suportadas na mesma região, mudar a geração de hardware é possível, mas deve ser feito manualmente. Isso exige que você prover uma nova instância na região onde a geração de hardware desejada esteja disponível e fazer backup e restaurar manualmente dados entre a instância antiga e a nova.


## <a name="tune-performance"></a>Desempenho de ajuste

**Como sintonizo o desempenho da minha instância gerenciada?**

A instância gerenciada do General Purpose usa o armazenamento remoto devido ao tamanho dos dados e arquivos de registro que importam para o desempenho. Para obter mais informações, consulte [Impacto do tamanho do arquivo de log no desempenho da instância gerenciada de propósito geral](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se sua carga de trabalho consistir em muitas pequenas transações, considere mudar o tipo de conexão do proxy para o modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo de armazenamento

**Qual é o tamanho máximo de armazenamento para a instância gerenciada?**

O tamanho do armazenamento para instância gerenciada depende do nível de serviço selecionado (Objetivo Geral ou Business Critical). Para obter limitações de armazenamento desses níveis de serviço, consulte [a característica do nível de serviço](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Fazer backup do custo de armazenamento 

**O armazenamento de backup é deduzido do meu armazenamento de instância gerenciado?**

Não, o armazenamento de backup não é deduzido do espaço de armazenamento de instância gerenciada. O armazenamento de backup é independente do espaço de armazenamento de instâncias e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter o backup de seus bancos de dados de instância, configuráveis de 7 a 35 dias. Para obter detalhes, consulte [backups automatizados](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Acompanhar o faturamento

**Existe uma maneira de acompanhar meu custo de faturamento para a minha instância gerenciada?**

Você pode fazê-lo usando a [solução Azure Cost Management](/azure/cost-management/). Navegue até **assinaturas** no [portal Azure](https://portal.azure.com) e selecione **Análise de Custos**. 

Use a opção **Custos Acumulados** e, `microsoft.sql/managedinstances`em seguida, filtre pelo tipo de **recurso** como . 
  
## <a name="inbound-nsg-rules"></a>Regras do NSG de entrada

**Como posso definir as regras de NSG de entrada em portas de gerenciamento?**

O plano de controle de instâncias gerenciadas mantém regras de NSG que protegem as portas de gerenciamento.

Aqui está para que as portas de gerenciamento são usadas:

Os portos 9000 e 9003 são usados pela infra-estrutura service fabric. O principal papel do Service Fabric é manter o cluster virtual saudável e manter o estado de meta em termos de número de réplicas de componentes.

As portas 1438, 1440 e 1452 são usadas pelo agente nó. O agente node é um aplicativo que é executado dentro do cluster e é usado pelo plano de controle para executar comandos de gerenciamento.

Além das regras do NSG, o firewall incorporado protege a instância na camada de rede. Na camada de aplicação, a comunicação é protegida com os certificados.
  
Para obter mais informações e como verificar o firewall incorporado, consulte [o firewall de instância incorporada do Azure SQL Database](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)gerenciado .


## <a name="mitigate-data-exfiltration-risks"></a>Mitigar riscos de exfiltração de dados  

**Como posso mitigar os riscos de exfiltração de dados?**

Para mitigar quaisquer riscos de exfiltração de dados, recomenda-se que os clientes apliquem um conjunto de configurações e controles de segurança:

- Ative [o TDE (Transparent Data Encryption, criptografia de dados transparente)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todos os bancos de dados.
- Desligue o Tempo de Execução da Linguagem Comum (CLR). Isso também é recomendado no local.
- Use apenas a autenticação aad (AAD) do Azure Active Directory (AAD).
- Instância de acesso com conta DBA de baixo privilégio.
- Configure o acesso à caixa de salto JiT para conta sysadmin.
- Ligue a [auditoria SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integre-a com mecanismos de alerta.
- Ative a [detecção de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) do conjunto [ADS (Advanced Data Security).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Casos de uso de economia de custos

**Onde posso encontrar casos de uso e consequente economia de custos com instância gerenciada?**

Estudos de caso de instância gerenciada:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [POWERDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Todos os scripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Para obter uma melhor compreensão dos benefícios, custos e riscos associados à implantação do Azure SQL Database, há também um estudo da Forrester: [Total Economic Impact of MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização dns 

**Posso atualizar o DNS?**

Atualmente, não fornecemos um recurso para atualizar a configuração do servidor DNS para instância gerenciada.

A configuração do DNS é eventualmente atualizada:

- Quando a locação do DHCP expirar.
- Na atualização da plataforma.

Como solução de solução, faça o downgrade da instância gerenciada para 4 vCore e atualize-a novamente depois. Isso tem um efeito colateral de atualizar a configuração do DNS.


## <a name="ip-address"></a>Endereço IP

**Posso me conectar à instância gerenciada usando endereço IP?**

A conexão à instância gerenciada usando endereço IP não é suportada. Mapas de nome de exemplo gerenciados para carregar balanceador na frente do cluster virtual de instância gerenciada. Como um cluster virtual poderia hospedar várias instâncias gerenciadas, a conexão não poderia ser roteada para instância gerenciada adequada sem especificar seu nome.

Para obter mais informações sobre a arquitetura de cluster virtual de instância gerenciada, consulte [arquitetura de conectividade de cluster virtual](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Uma instância gerenciada pode ter um endereço IP estático?**

Em situações raras, mas necessárias, talvez precisemos fazer uma migração on-line de uma instância gerenciada para um novo cluster virtual. Se necessário, essa migração se deve a mudanças em nossa pilha de tecnologia visando melhorar a segurança e a confiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP mapeado para o nome de host de instância gerenciada. O serviço de instância gerenciada não reivindica suporte de endereço IP estático e reserva o direito de alterá-lo sem aviso prévio como parte de ciclos regulares de manutenção.

Por essa razão, desencorajamos fortemente a dependência da imutabilidade do endereço IP, pois isso poderia causar um tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Mudar fuso horário

**Posso alterar o fuso horário para uma instância gerenciada existente?**

A configuração do fuso horário pode ser definida quando uma instância gerenciada é provisionada pela primeira vez. A alteração do fuso horário da instância gerenciada existente não é suportada. Para obter detalhes, consulte [limitações de fuso horário](sql-database-managed-instance-timezone.md#limitations).

As soluçãos incluem a criação de uma nova instância gerenciada com o fuso horário adequado e, em seguida, executar um backup manual e restaurar, ou o que recomendamos, executar uma [restauração ponto-em-tempo de instância cruzada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como resolver problemas de desempenho com minha instância gerenciada?**

Para uma comparação de desempenho entre instância gerenciada e SQL Server, um bom ponto de partida são [as práticas recomendadas para comparação de desempenho entre a instância gerenciada do Azure SQL e o artigo do SQL Server.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

O carregamento de dados é muitas vezes mais lento na instância gerenciada do que no SQL Server devido ao modelo de recuperação total obrigatório e [aos limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na transferência de gravação de registro de transações. Às vezes, isso pode ser trabalhado carregando dados transitórios em tempdb em vez de banco de dados do usuário, ou usando columnstore clustered ou tabelas otimizadas para a memória.


## <a name="restore-encrypted-backup"></a>Restaurar backup criptografado

**Posso restaurar meu banco de dados criptografado para instância gerenciada?**

Sim, você não precisa descriptografar seu banco de dados para ser capaz de restaurá-lo para instância gerenciada. Você precisa fornecer um certificado/chave usado como protetor de chave de criptografia no sistema de origem para a instância gerenciada para poder ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- *Faça upload do protetor de certificados para a instância gerenciada*. Pode ser feito usando apenas powershell. O [script de amostra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Carregue o protetor de chave assimétrico para o Azure Key Vault (AKV) e a instância gerenciada de ponto para ele*. Essa abordagem se assemelha ao caso de uso do TDE (Bring-your-own-key) TDE que também usa a integração AKV para armazenar a chave de criptografia. Se você não quiser usar a chave como protetor de chave de criptografia e apenas quiser disponibilizar a chave para a instância gerenciada para restaurar bancos de dados criptografados, siga as instruções para [configurar o BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e não verifique a caixa de seleção *Faça da chave selecionada o protetor TDE padrão*.

Uma vez que você disponibilize o protetor de criptografia para a instância gerenciada, você pode prosseguir com o procedimento padrão de restauração do banco de dados.

## <a name="migrate-from-single-db"></a>Migrar de um único DB 

**Como posso migrar do azure SQL Database single ou elástico pool para instância gerenciada?**

A instância gerenciada oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que outras opções de implantação do Banco de Dados SQL do Azure. Se você quiser consolidar dados em uma única instância ou simplesmente precisar de um recurso suportado exclusivamente em instância gerenciada, você pode migrar seus dados usando a funcionalidade BACPAC (exportação/importação).
