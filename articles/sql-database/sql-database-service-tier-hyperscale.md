---
title: Visão geral de Hiperescala do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve a camada de serviço da Hiperescala no modelo de compra baseado em vCore no Banco de Dados SQL do Azure e explica como ele é diferente do que as camadas de serviço de Uso Geral e Comercialmente Crítico.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 074a28af8c80c109dbe97306900e8f00618e435a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411691"
---
# <a name="hyperscale-service-tier"></a>Tipo de serviço de Hiperescala

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:
- Uso Geral/Padrão 
-  Hiperescala
-  Comercialmente Crítico/Premium

A camada de serviço em hiperescala no banco de dados SQL do Azure é a camada de serviço mais recente em que o modelo de compra baseado em vCore. Essa camada de serviço é um armazenamento altamente escalonável e o nível de desempenho de computação que aproveita a arquitetura do Azure para escalar horizontalmente o armazenamento e recursos de computação para um banco de dados SQL do Azure substancialmente além dos limites disponíveis para uso geral e negócios Comercialmente Crítico.

> 
> [!NOTE]
> Para obter detalhes sobre os níveis de serviços General Purpose e Business Critical no modelo de compras baseado em vCore, consulte [os](sql-database-service-tier-general-purpose.md) níveis de serviço suscional e de serviços de negócios [críticos.](sql-database-service-tier-business-critical.md) Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Quais são as funcionalidades de Hiperescala

A camada de serviço da Hiperescala no Banco de Dados SQL do Azure fornece os seguintes recursos adicionais:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups de banco de dados quase instantâneos (baseados em instantâneos de arquivos armazenados no armazenamento Azure Blob) independentemente do tamanho sem impacto de IO nos recursos de computação  
- Rápidas restaurações de banco de dados (com base em instantâneos de arquivo) em minutos, em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho geral devido à maior taxa de transferência de log e tempos mais rápidos de confirmação de transação, independentemente dos volumes de dados
- Rápida expansão - você pode provisionar uma ou mais somente leitura nós para o descarregamento de sua carga de trabalho de leitura e para uso como reserva quente
- Escala rápida - você pode, em tempo constante, escalar seus recursos de computação para acomodar cargas de trabalho pesadas quando necessário e, em seguida, escalar os recursos de computação de volta para baixo quando não necessário.

A camada de serviço da Hiperescala elimina muitos dos limites práticos vistos tradicionalmente em bancos de dados de nuvem. Onde mais outros bancos de dados são limitados pelos recursos disponíveis em um único nó, bancos de dados na camada de serviço da Hiperescala não têm esses limites. Com sua arquitetura de armazenamento flexível, o armazenamento aumenta conforme necessário. Na verdade, os bancos de dados da Hiperescala não são criados com um tamanho máximo definido. Um banco de dados da Hiperescala aumenta conforme necessário – e você será cobrado apenas pela capacidade de que usar. Para cargas de trabalho de leitura intensa, a camada de serviço da Hiperescala fornece rápida expansão por meio do provisionamento de leitura de réplicas adicionais conforme necessário para o descarregamento de cargas de trabalho leitura.

Além disso, o tempo necessário para criar backups de banco de dados ou para aumentar ou diminuir a escala não está mais vinculado ao volume de dados no banco de dados. Bancos de dados de Hiperescala podem ser armazenados virtualmente instantaneamente. Você também pode dimensionar um banco de dados em dezenas de terabytes para cima ou para baixo em minutos. Esse recurso libera você das preocupações sobre ser encaixotado pelas opções iniciais de configuração.

Para obter mais informações sobre os tamanhos da computação para a camada de serviço em Hiperescala, confira [Características da camada de serviço](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviço da Hiperescala

O nível de serviço hyperscale destina-se à maioria das cargas de trabalho dos negócios, pois fornece grande flexibilidade e alto desempenho com recursos de computação e armazenamento escaláveis independentemente. Com a capacidade de dimensionar automaticamente o armazenamento de até 100 TB, é uma ótima escolha para clientes que:

- Tenha grandes bancos de dados no local e queira modernizar seus aplicativos movendo-se para a nuvem
- Já estão na nuvem e são limitados pelas restrições máximas de tamanho do banco de dados de outros níveis de serviço (1-4 TB)
- Tem bancos de dados menores, mas requerem dimensionamento rápido de computação vertical e horizontal, alto desempenho, backup instantâneo e restauração rápida do banco de dados.

O nível de serviço Hyperscale suporta uma ampla gama de cargas de trabalho do SQL Server, desde o OLTP puro até a análise pura, mas é otimizado principalmente para cargas de trabalho oLTP e de processamento analítico (HTAP).

> [!IMPORTANT]
> Pools Elásticos não dão suporte a camada de serviço da Hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de Hiperescala

A camada de serviço em Hiperescala só está disponível no [modelo vCore](sql-database-service-tiers-vcore.md). Para alinhar-se com a nova arquitetura, o modelo de preços é ligeiramente diferente de camadas de serviço de Uso Geral ou Comercialmente Críticas:

- **Computação**:

  O preço de unidade de computação em Hiperescala é por réplica. O preço do [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado para réplicas em escala de leitura automaticamente. Criamos uma réplica primária e uma réplica somente leitura por banco de dados Hyperscale por padrão.  Os usuários podem ajustar o número total de réplicas, incluindo a principal de 1 a 5.

- **Armazenamento**:

  Você não precisa especificar o tamanho máximo de dados ao configurar um banco de dados da Hiperescala. No nível de hiperescala, você é cobrado pelo armazenamento do seu banco de dados com base na alocação real. O armazenamento é automaticamente alocado entre 40 GB e 100 TB, em incrementos de 10 GB. Vários arquivos de dados podem crescer ao mesmo tempo, se necessário. Um banco de dados Hyperscale é criado com um tamanho inicial de 10 GB e começa a crescer 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB.

Para obter mais informações sobre os preços da Hiperescala, confira [Preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário de mecanismos de banco de dados tradicional que centralizou a todas as funções de gerenciamento de dados em um local/processo (até mesmo então chamados bancos de dados distribuídos em produção hoje têm várias cópias de um mecanismo de dados monolítico), um banco de dados da Hiperescala separa o mecanismo de processamento de consulta, em que a semântica de diversos mecanismos de dados divergem dos componentes que fornecem armazenamento de longo prazo e a durabilidade dos dados. Dessa forma, a capacidade de armazenamento pode ser perfeitamente expandida até onde conforme necessário (o destino inicial é 100 TB). Réplicas somente leitura compartilham os mesmos componentes de armazenamento para que nenhuma cópia de dados seja necessária para girar uma nova réplica legível. 

O diagrama a seguir ilustra os diferentes tipos de nós em um banco de dados de Hiperescala:

![Arquitetura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Um banco de dados Hyperscale contém os seguintes tipos diferentes de componentes:

### <a name="compute"></a>Computação

O nó de computação é onde reside o mecanismo relacional, portanto, os elementos de linguagem, processamento de consulta e assim por diante, ocorrerem. Todas as interações do usuário com um banco de dados da Hiperescala ocorrem por meio de nós de computação. Nós têm caches baseado em SSD (rotulado como RBPEX - extensão do Pool de buffers resiliente no diagrama anterior) para minimizar o número da rede de computação viagens de ida e necessárias para buscar uma página de dados. Há um nó de computação principal em que todas as transações e cargas de trabalho de leitura / gravação são processadas. Há um ou mais nós de computação secundária que atuam como nós em espera ativos para fins de failover, bem como para atuam como nós de computação de somente leitura para o descarregamento de cargas de trabalho de leitura (se essa funcionalidade é desejada).

### <a name="page-server"></a>Servidor de página

Servidores de página são sistemas que representam um mecanismo de armazenamento dimensionado.  Cada servidor de páginas é responsável por um subconjunto das páginas no banco de dados.  Nominalmente, cada servidor de página controla entre 128 GB e 1 TB de dados. Nenhum dado é compartilhado em mais de um servidor de página (fora de réplicas que são mantidos por redundância e disponibilidade). O trabalho de um servidor de páginas é servir páginas de banco de dados para os nós de computação sob demanda e manter as páginas atualizadas à medida que as transações atualizam dados. Servidores de página são mantidas atualizadas ao reproduzir os registros de log do serviço de log. Servidores de página também mantenham caches baseado em SSD para melhorar o desempenho. Armazenamento de longo prazo de páginas de dados é mantido no armazenamento do Azure para confiabilidade adicional.

### <a name="log-service"></a>Serviço de log

O serviço de log aceita registros de log da réplica de computação primária, persiste-os em um cache durável e encaminha os registros de log para o resto das réplicas de computação (para que possam atualizar seus caches) bem como o servidor de página (s) relevante, para que os dados possam ser atualizados lá. Desta forma, todas as alterações de dados da réplica de computação primária são propagadas através do serviço de log para todas as réplicas de computação secundárias e servidores de página. Finalmente, os registros de registro são empurrados para armazenamento de longo prazo no Azure Storage, que é um repositório de armazenamento praticamente infinito. Este mecanismo remove a necessidade de truncação freqüente de log. O serviço de log também tem cache local para acelerar o acesso aos registros de log.

### <a name="azure-storage"></a>Armazenamento do Azure

O Azure Storage contém todos os arquivos de dados em um banco de dados. Os servidores de página mantêm os arquivos de dados no Azure Storage atualizados. Este armazenamento é usado para fins de backup, bem como para replicação entre as regiões do Azure. Os backups são implementados usando instantâneos de armazenamento de arquivos de dados. As operações de restauração usando instantâneos são rápidas, independentemente do tamanho dos dados. Os dados podem ser restaurados a qualquer momento dentro do período de retenção de backup do banco de dados.

## <a name="backup-and-restore"></a>Backup e restauração

Os backups são baseados em snapshot de arquivo e, portanto, são quase instantâneos. A separação de armazenamento e computação permite reduzir a operação de backup/restauração para a camada de armazenamento para reduzir a carga de processamento na réplica de computação principal. Como resultado, o backup do banco de dados não afeta o desempenho do nó de computação principal; da mesma forma, as restaurações são feitas revertendo para snapshots de arquivos e, como tal, não são um tamanho de operação de dados. A restauração é uma operação de tempo constante, e até mesmo bancos de dados de vários terabytes podem ser restaurados em minutos em vez de horas ou dias. A criação de novos bancos de dados restaurando um backup existente também aproveita esse recurso: a criação de cópias de bancos de dados para fins de desenvolvimento ou teste, mesmo de bancos de dados de tamanho terabyte, é possível em minutos.

## <a name="scale-and-performance-advantages"></a>Vantagens de desempenho e escala

Com a capacidade de criar rapidamente nós de computação adicionais de somente leitura para cima/para baixo, a arquitetura permite significativa ler recursos de escala e Hiperescala também pode liberar o nó de computação principal para atender às solicitações de gravação mais. Além disso, os nós de computação podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento compartilhado da arquitetura da Hiperescala.

## <a name="create-a-hyperscale-database"></a>Criar um banco de dados hyperscale

Um banco de dados Hyperscale pode ser criado usando o [portal Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) ou [CLI.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) Os bancos de dados de hiperescala estão disponíveis apenas usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O comando T-SQL a seguir cria um banco de dados em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `CREATE DATABASE`. Consulte os [limites de recursos](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) para uma lista de objetivos de serviço válidos.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Isso criará um banco de dados Hyperscale no hardware Gen5 com 4 núcleos.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrar um Banco de Dados SQL do Azure existente para a camada de serviço em Hiperescala

Você pode mover seus bancos de dados Azure SQL existentes para hyperscale usando o [portal Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Neste momento, esta é uma migração unidirecional. Você não pode mover bancos de dados da Hyperscale para outro nível de serviço, exceto exportando e importando dados. Para provas de conceito (POCs), recomendamos fazer uma cópia de seus bancos de dados de produção e migrar a cópia para hyperscale. Migrar um banco de dados Azure SQL existente para o nível Hyperscale é um tamanho de operação de dados.

O comando T-SQL a seguir move um banco de dados para a camada de serviço em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `ALTER DATABASE`.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Conectar-se a uma réplica em escala de leitura de um banco de dados em Hiperescala

Nos bancos de `ApplicationIntent` dados Hyperscale, o argumento na seqüência de conexões fornecida pelo cliente dita se a conexão é roteada para a réplica de gravação ou para uma réplica secundária somente leitura. Se o `ApplicationIntent` definido como `READONLY` e o banco de dados não tiverem uma réplica secundária, a conexão será roteada para a réplica primária e o padrão será o comportamento `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

As réplicas secundárias de hiperescala são todas idênticas, usando o mesmo Objetivo de Nível de Serviço da réplica primária. Se houver mais de uma réplica secundária, a carga de trabalho será distribuída em todos os secundários disponíveis. Cada réplica secundária é atualizada independentemente, assim, diferentes réplicas poderiam ter latência de dados diferentes em relação à réplica primária.

## <a name="database-high-availability-in-hyperscale"></a>Banco de dados de alta disponibilidade em hiperescala

Como em todos os outros níveis de serviço, o Hyperscale garante a durabilidade dos dados para transações comprometidas, independentemente da disponibilidade de réplicas computacionais. A extensão do tempo de inatividade devido à replicação primária se tornar indisponível depende do tipo de failover (planejado versus não planejado), e da presença de pelo menos uma réplica secundária. Em um failover planejado (ou seja, um evento de manutenção), o sistema cria a nova réplica primária antes de começar um failover ou usa uma réplica secundária existente como alvo de failover. Em um failover não planejado (ou seja, uma falha de hardware na réplica principal), o sistema usa uma réplica secundária como um alvo de failover se existir ou cria uma nova réplica primária do pool de capacidade computacional disponível. Neste último caso, a duração do tempo de inatividade é maior devido às etapas extras necessárias para criar a nova réplica primária.

Para SLA de hiperescala, consulte [SLA para Banco de Dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastres para bancos de dados de hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Restaurando um banco de dados hyperscale para uma geografia diferente
Se você precisar restaurar um DB de hiperescala de banco de dados SQL do Azure sql para uma região diferente da que está hospedada atualmente, como parte de uma operação de recuperação de desastres ou perfuração, realocação ou qualquer outra razão, o método principal é fazer uma georestauração do banco de dados.  Isso envolve exatamente as mesmas etapas que o que você usaria para restaurar qualquer outro AZURE SQL DB para uma região diferente:
1. Crie um servidor de banco de dados SQL azure na região de destino se você ainda não tiver um servidor apropriado lá.  Este servidor deve ser de propriedade da mesma assinatura do servidor original (fonte).
2. Siga as instruções no tópico [de georestauração](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) da página sobre a restauração de bancos de dados SQL do Azure a partir de backups automáticos.

> [!NOTE]
> Como a origem e o destino estão em regiões separadas, o banco de dados não pode compartilhar o armazenamento instantâneo com o banco de dados de origem como em restaurações não-geo, que se completam extremamente rapidamente. No caso de uma georestauração de um banco de dados Hyperscale, será uma operação de tamanho de dados, mesmo que o alvo esteja na região emparelhada do armazenamento geo-replicado.  Isso significa que fazer uma georestauração levará tempo proporcional ao tamanho do banco de dados que está sendo restaurado.  Se o alvo estiver na região emparelhada, a cópia estará dentro de uma região, que será significativamente mais rápida do que uma cópia transversal, mas ainda será uma operação de tamanho de dados.

## <a name="available-regions"></a><a name=regions></a>Regiões disponíveis

O nível de hiperescala do banco de dados Azure SQL está disponível atualmente nas seguintes regiões:

- Leste da Austrália
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- Centro dos EUA
- Leste da China 2
- Norte da China 2
- Leste da Ásia
- Leste dos EUA
- Leste dos EUA 2
- França Central
- Leste do Japão
- Oeste do Japão
- Coreia Central
- Sul da Coreia
- Centro-Norte dos EUA
- Norte da Europa
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Sudeste Asiático
- Sul do Reino Unido
- Oeste do Reino Unido
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

Se você quiser criar um banco de dados Hyperscale em uma região que não esteja listada como suportada, você pode enviar uma solicitação de onboarding através do portal Azure. Para obter instruções, [consulte Solicitar aumentos de cotas para o Banco de Dados SQL do Azure](quota-increase-request.md) para obter instruções. Ao enviar sua solicitação, use as seguintes diretrizes:

- Use o [outro tipo de](quota-increase-request.md#other) cota de banco de dados SQL.
- Nos detalhes do texto, adicione os núcleos SKU/total de cálculo, incluindo réplicas legíveis.
- Também especifique a TB estimada.

## <a name="known-limitations"></a>Limitações conhecidas

Estas são as limitações atuais para o nível de serviço Hyperscale a partir de GA.  Estamos trabalhando ativamente para remover o maior número possível dessas limitações.

| Problema | Descrição |
| :---- | :--------- |
| O painel Gerenciar backups para um servidor lógico não mostra bancos de dados hyperscale, estes serão filtrados a partir da exibição  | A hyperscale tem um método separado para gerenciar backups e, como tal, as configurações de retenção de longo prazo e de retenção de ponto no tempo não se aplicam / são invalidadas. Da mesma forma, os bancos de dados da Hiperescala não aparecem no painel Gerenciar Backup. |
| Restauração em um momento determinado | Você pode restaurar um banco de dados Hyperscale em um banco de dados não-Hyperscale, dentro de um período de retenção de banco de dados não-Hyperscale. Não é possível restaurar um banco de dados não-Hyperscale em um banco de dados Hyperscale.|
| Se um banco de dados tiver um ou mais arquivos de dados maiores que 1 TB, a migração falhará | Em alguns casos, pode ser possível contornar esse problema diminuindo os arquivos grandes para menos de 1 TB. Se migrar um banco de dados que está sendo usado durante o processo de migração, certifique-se de que nenhum arquivo fique maior que 1 TB. Use a seguinte consulta para determinar o tamanho dos arquivos do banco de dados. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Banco de Dados SQL | A instância gerenciada do banco de dados Do Azure SQL não é suportada atualmente com bancos de dados Hyperscale. |
| Pools elásticos |  Os Pools Elásticos não são suportados atualmente com hyperscale de banco de dados SQL.|
| Migração para Hiperescala é, no momento, uma operação unidirecional | Depois que um banco de dados é migrado para Hiperescala, ele não pode ser migrado diretamente para uma camada de serviço que não esteja em Hiperescala. Atualmente, a única maneira de migrar um banco de dados de Hyperscale para não-Hyperscale é exportar/importar usando um arquivo BACPAC ou outras tecnologias de movimentação de dados (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS, etc.)|
| Migração de bancos de dados com objetos persistentes na memória | A hiperescala só suporta objetos in-memory não persistentes (tipos de tabela, SPs nativos e funções).  Tabelas in-memory persistentes e outros objetos devem ser descartados e recriados como objetos não-in-memory antes de migrar um banco de dados para o nível de serviço Hyperscale.|
| Replicação geográfica  | Ainda não é possível configurar a replicação geográfica para o Azure SQL Database Hyperscale. |
| Cópia de banco de dados | Você ainda não pode usar o Database Copy para criar um novo banco de dados no Azure SQL Hyperscale. |
| Integração TDE/AKV | A criptografia de banco de dados transparente usando o Azure Key Vault (comumente referido como Bring-Your-Own-Key ou BYOK) ainda não é suportada para o Azure SQL Database Hyperscale, no entanto, o TDE com chaves gerenciadas de serviço é totalmente suportado. |
|Recursos inteligentes do banco de dados | Com exceção da opção "Plano de Força", todas as outras opções de ajuste automático ainda não são suportadas no Hyperscale: as opções podem parecer habilitadas, mas não haverá recomendações ou ações feitas. |
|Insights de Desempenho de Consulta | O Query Performance Insights não é suportado atualmente para bancos de dados Hyperscale. |
| Reduzir Banco de Dados | DBCC SHRINKDATABASE ou DBCC SHRINKFILE não é suportado atualmente para bancos de dados Hyperscale. |
| Verificação de integridade do banco de dados | O DBCC CHECKDB não é suportado atualmente para bancos de dados Hyperscale. Consulte [a integridade dos dados no banco de dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) para obter detalhes sobre o gerenciamento da integridade dos dados no Banco de Dados SQL do Azure. |

## <a name="next-steps"></a>Próximas etapas

- Para perguntas frequentes sobre Hiperescala, confira [Perguntas frequentes sobre Hiperescala](sql-database-service-tier-hyperscale-faq.md).
- Para obter informações sobre as camadas de serviço, consulte [camadas de serviço](sql-database-service-tiers.md)
- Consulte [Visão geral dos limites de recursos em um servidor lógico](sql-database-resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e assinatura.
- Para comprar os limites de modelo para um banco de dados individual, confira [Limites de modelo de compra baseado em vCore do Banco de Dados SQL do Azure para um banco de dados individual](sql-database-vcore-resource-limits-single-databases.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
