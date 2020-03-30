---
title: Migrar do SQL Server para a instância gerenciada
description: Saiba como migrar um banco de dados da instância do SQL Server para o Azure SQL Database - Instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208938"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migração de uma Instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure

Neste artigo, você aprenderá sobre os métodos para migrar uma Instância do SQL Server 2005 ou versão posterior para uma [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Para obter informações sobre como migrar para um banco de dados individual ou pool elástico, confira [Migrar para um banco de dados individual ou em pool](sql-database-cloud-migrate.md). Para obter informações de migração sobre como migrar de outras plataformas, confira o [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Se você quiser iniciar rapidamente e tentar Instância Gerenciada, você pode querer ir para [o guia Quick-start](sql-database-managed-instance-quickstart-guide.md) em vez desta página. 

Em um nível alto, o processo de migração de banco de dados se parece com:

![processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avalie a compatibilidade de instâncias gerenciadas](#assess-managed-instance-compatibility) onde você deve garantir que não há problemas de bloqueio que possam impedir suas migrações.
  - Essa etapa também inclui a criação de linha de base de [desempenho](#create-performance-baseline) para determinar o uso de recursos na instância do SQL Server de origem. Essa etapa é necessária se você quiser que o implante corretamente a Instância Gerenciada e verifique se os desempenhos após a migração não são afetados.
- [Escolha opções de conectividade de aplicativos](sql-database-managed-instance-connect-app.md)
- [Implante em uma instância gerenciada de tamanho ideal,](#deploy-to-an-optimally-sized-managed-instance) onde você escolherá características técnicas (número de vCores, quantidade de memória) e nível de desempenho (Business Critical, General Purpose) da sua Instância Gerenciada.
- [Selecione o método de migração e migre](#select-migration-method-and-migrate) para onde você migra seus bancos de dados usando migração off-line (backup/restauração nativo, importação/exportação de banco de dados) ou migração on-line (Serviço de Migração de Dados, Replicação Transacional).
- [Monitore os aplicativos](#monitor-applications) para garantir que você tenha esperado desempenho.

> [!NOTE]
> Para migrar um banco de dados individual para um banco de dados individual ou um pool elástico, confira [Migrar um banco de dados do Microsoft SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerenciada

Primeiro, determine se a instância gerenciada é compatível com os requisitos de banco de dados do aplicativo. A opção de implantação de instância gerenciada foi projetada para fornecer migração lift-and-shift fácil para a maioria dos aplicativos existentes que usam o SQL Server local ou em máquinas virtuais. No entanto, às vezes é necessário recursos ou capacidades que ainda não têm suporte e o custo de implementar uma solução alternativa é muito alto.

Utilize o [DMA (Assistente de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) para detectar possíveis problemas de compatibilidade que afetam a funcionalidade do banco de dados no Banco de Dados SQL do Azure. O AMD ainda não dá suporte à instância gerenciada como destino de migração, mas é recomendável executar a avaliação no Banco de Dados SQL do Azure e examinar atentamente a lista de problemas de compatibilidade e paridade de recursos relatados em relação à documentação do produto. Confira [Recursos do Banco de Dados SQL do Azure](sql-database-features.md) para verificar se há problemas de bloqueio relatados que não bloqueiam a instância gerenciada, porque a maioria dos problemas de bloqueio que impedem uma migração para o Banco de Dados SQL do Azure foi removida com a instância gerenciada. Por exemplo, recursos como consultas entre banco de dados, transações entre banco de dados na mesma instância, servidor vinculado a outras fontes do SQL, CLR, tabelas temporárias globais, exibições em nível de instância, Service Broker e similares estão disponíveis nas instâncias gerenciadas.

Se houver problemas de bloqueio relatados que não foram removidos com a opção de implantação de instância gerenciada, talvez seja preciso considerar uma opção alternativa, como o [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Estes são alguns exemplos:

- Se você precisar de acesso direto ao sistema operacional ou ao sistema de arquivos, por exemplo, para instalar agentes personalizados ou de terceiros na mesma máquina virtual com o SQL Server.
- Se você tiver uma dependência estrita de recursos que ainda não têm suporte, como transações entre instâncias, PolyBase e FileStream/FileTable.
- Se absolutamente necessário ficar em uma versão específica do SQL Server (2012, por exemplo).
- Se os requisitos de computação forem muito menores do que o oferecido pela instância gerenciada (um vCore, por exemplo) e a fusão de banco de dados não for uma opção aceitável.

Se você tiver resolvido todos os bloqueadores de migração identificados e continuar a migração para Instância Gerenciada, observe que algumas das alterações podem afetar o desempenho da sua carga de trabalho:
- O modelo de recuperação total obrigatório e o cronograma de backup automatizado regular podem afetar o desempenho de sua carga de trabalho ou ações de manutenção/ETL se você tiver usado periodicamente modelo simples/em massa ou backups interrompidos demanda.
- Diferentes configurações de nível de servidor ou banco de dados, como sinalizadores de rastreamento ou níveis de compatibilidade
- Novos recursos que você está usando, como O TDE (Transparent Database Encryption, criptografia de banco de dados transparente) ou grupos de failover automático podem afetar o uso de CPU e IO.

A Instância Gerenciada garante 99,99% de disponibilidade mesmo nos cenários críticos, de modo que a sobrecarga causada por esses recursos não pode ser desativada. Para obter mais informações, consulte [as causas básicas que podem causar desempenho diferente no SQL Server e na Instância Gerenciada](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Criar linha de base de desempenho

Se você precisar comparar o desempenho da sua carga de trabalho na Instância Gerenciada com a carga de trabalho original em execução no SQL Server, você precisará criar uma linha de base de desempenho que será usada para comparação. 

A linha de base de desempenho é um conjunto de parâmetros como uso médio/máximo da CPU, latência média/max do disco IO, throughput, IOPS, expectativa de vida média/máxima da página, tamanho médio máximo do tempdb. Você gostaria de ter parâmetros semelhantes ou até melhores após a migração, por isso é importante medir e registrar os valores de base para esses parâmetros. Além dos parâmetros do sistema, você precisaria selecionar um conjunto de consultas representativas ou as consultas mais importantes em sua carga de trabalho e medir a duração mínima/média/máxima, o uso da CPU para as consultas selecionadas. Esses valores permitiriam comparar o desempenho da carga de trabalho em execução na Instância Gerenciada com os valores originais do servidor SQL de origem.

Alguns dos parâmetros que você precisaria medir na instância do SQL Server são: 
- [Monitore o uso da CPU na instância do SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registre o uso médio e máximo da CPU.
- [Monitore o uso da memória na instância do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determine a quantidade de memória usada por diferentes componentes, como pool de buffer, cache de plano, pool de colunas, [OLTP de memória,](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)etc. Além disso, você deve encontrar valores médios e máximos do contador de desempenho de memória Page Life Expectancy.
- Monitore o uso do IO do disco na instância de origem do SQL Server usando [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) visualização ou [contadores de desempenho](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitore o desempenho da carga de trabalho e consulta ou a instância do SQL Server examinando as visualizações dinâmicas de gerenciamento ou a loja de consultas se você estiver migrando da versão SQL Server 2016+. Identifique a duração média e o uso da CPU das consultas mais importantes em sua carga de trabalho para compará-las com as consultas que estão sendo realizadas na Instância Gerenciada.

> [!Note]
> Se você notar qualquer problema com sua carga de trabalho no SQL Server, como alto uso de CPU, pressão de memória constante, problemas de temperatura ou parametrização, você deve tentar resolvê-los na instância do SQL Server de origem antes de pegar a linha de base e a migração. Migrar problemas de know para qualquer novo sistema migh causar resultados inesperados e invalidar qualquer comparação de desempenho.

Como resultado dessa atividade, você deve ter documentado valores médios e máximos para o uso de CPU, memória e IO em seu sistema de origem, bem como duração média e máxima e uso da CPU das consultas dominantes e mais críticas em sua carga de trabalho. Você deve usar esses valores mais tarde para comparar o desempenho de sua carga de trabalho na Instância Gerenciada com o desempenho da carga de trabalho da carga de trabalho no SQL Server de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implantar em uma instância gerenciada de tamanho ideal

A instância gerenciada é adaptada para cargas de trabalho locais cuja movimentação para a nuvem é planejada. Ele introduz um [novo modelo de compra](sql-database-service-tiers-vcore.md) que proporciona maior flexibilidade na seleção do nível certo de recursos para suas cargas de trabalho. No ambiente local, você provavelmente está acostumado a dimensionar essas cargas de trabalho usando núcleos físicos e largura de banda de E/S. O modelo de compra para a instância gerenciada baseia-se em núcleos virtuais, ou “vCores”, com armazenamento adicional e E/S disponíveis separadamente. O modelo vCore é uma maneira mais simples de compreender os requisitos de computação na nuvem em relação ao que você utiliza no local atualmente. Esse novo modelo permite que você dimensione adequadamente o ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher o nível de serviço e características certas são descritas aqui:
- Com base no uso da CPU de linha de base, você pode provisionar uma instância gerenciada que corresponda ao número de núcleos que você está usando no SQL Server, tendo em mente que as características da CPU podem precisar ser dimensionadas para corresponder às [características da VM onde a Instância Gerenciada é instalada](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Com base no uso da memória de linha de base, escolha [o nível de serviço que tenha memória correspondente](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, então você precisaria selecionar a Instância Gerenciada com a quantidade de vCores que tem memória correspondente (por exemplo, 5,1 GB/vCore no Gen5). 
- Com base na latência io de linha de base do subsistema de arquivos, escolha entre os níveis de Uso Geral (latência superior a 5ms) e os níveis de serviço Business Critical (latência inferior a 3 ms).
- Com base no throughput da linha de base, pré-aloque o tamanho dos dados ou arquivos de log para obter o desempenho esperado de IO.

Você pode escolher os recursos de computação e armazenamento no momento da implantação e, em seguida, alterá-lo depois sem introduzir tempo de inatividade para o seu aplicativo usando o [portal Azure](sql-database-scale-resources.md):

![dimensionamento da instância gerenciada](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Para saber como criar a infraestrutura de VNet e uma instância gerenciada, confira [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> É importante manter seu VNet e sub-rede de destino sempre de acordo com [os requisitos de VNet de instância gerenciada.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Qualquer incompatibilidade pode impedi-lo de criar novas instâncias ou utilizar aquelas que você já criou. Saiba mais sobre a [criação de novas redes](sql-database-managed-instance-create-vnet-subnet.md) e a [configuração de redes existentes](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="select-migration-method-and-migrate"></a>Selecionar o método de migração e migrar

A opção de implantação de instância gerenciada destina-se a cenários de usuários que exigem a migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS. Essa é a escolha ideal quando for necessário fazer lift-and-shift do back-end dos aplicativos que utilizam regularmente as funcionalidades entre banco de dados e/ou nível de instância. Se este é o seu cenário, você pode mover uma instância inteira para um ambiente correspondente no Azure sem a necessidade de arquitetar novamente seus aplicativos.

Para mover instâncias do SQL, é necessário planejar atentamente:

- A migração de todos os bancos de dados que precisam ser colocados (os que estão executando na mesma instância)
- A migração de objetos de nível de instância que o aplicativo depende, incluindo logons, credenciais, Operadores e Trabalhos SQL Agent e gatilhos de nível de servidor.

A instância gerenciada é um serviço gerenciado que permite delegar algumas das atividades regulares do DBA para a plataforma conforme são inseridas. Portanto, alguns dados de nível de instância não precisam ser migrados, como trabalhos de manutenção para backups regulares ou configuração Always On, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

A instância gerenciada dá suporte às seguintes opções de migração de banco de dados (atualmente, estes são os únicos métodos de migração compatíveis):

- Serviço de Migração de Banco de Dados do Azure - migração com tempo de inatividade próximo de zero,
- `RESTORE DATABASE FROM URL` nativo - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Serviço de Migração de Banco de Dados do Azure

O [DMS (Serviço de Migração de Banco de Dados do Azure)](../dms/dms-overview.md) é um serviço totalmente gerenciado projetado para permitir migrações contínuas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e SQL Server existentes para o Azure. As opções de implantação na versão prévia pública incluem bancos de dados no Banco de Dados SQL do Azure e bancos de dados do SQL Server em uma Máquina Virtual do Azure. O DMS é o método recomendado de migração para as cargas de trabalho empresariais.

Se você usa o SSIS (SQL Server Integration Services) no SQL Server local, o DMS ainda não dá suporte à migração do catálogo do SSIS (SSISDB) que armazena pacotes SSIS. No entanto, você pode provisionar um Azure-SSIS IR (Integration Runtime) no ADF (Azure Data Factory) que criará um SSISDB em uma instância gerenciada e, em seguida, reimplantar os pacotes nele. Confira [Criar um Azure-SSIS IR no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Para saber mais sobre esse cenário e as etapas de configuração do DMS, consulte [Migrar seu banco de dados local para uma instância gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURAÇÃO nativa da URL

A RESTAURAÇÃO de backups nativos (arquivos .bak) obtidos do SQL Server local ou do [SQL Server em Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponível no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), é uma das principais funcionalidades da opção de implantação de instância gerenciada que permite uma rápida e fácil migração de banco de dados offline.

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![fluxo de migração](./media/sql-database-managed-instance-migration/migration-flow.png)

A tabela a seguir fornece mais informações sobre os métodos que podem ser utilizados, dependendo da versão do SQL Server de origem em execução:

|Etapa|Mecanismo SQL e versão|Método de backup/restauração|
|---|---|---|
|Coloque o backup no Armazenamento do Microsoft Azure|SQL 2012 SP1 CU2 anterior|Upload do arquivo .bak diretamente para Armazenamento do Microsoft Azure|
||2012 SP1 CU2 - 2016|Backup direto utilizando a sintaxe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e posterior|Backup direto utilizando [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restauração do Armazenamento do Azure para uma instância gerenciada|[RESTORE FROM URL com SAS CREDENTIAL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Ao migrar um banco de dados protegido pela [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) para uma instância gerenciada usando a opção de restauração nativa, o certificado correspondente do SQL Server local ou IaaS precisará ser migrado antes da restauração do banco de dados. Para obter etapas detalhadas, confira [Migrar o certificado TDE para uma instância gerenciada](sql-database-managed-instance-migrate-tde-certificate.md)
> - Não há suporte para restauração de bancos de dados do sistema. Para migrar objetos de nível de instância (armazenados em bancos de dados mestres ou msdb), é recomendável script e executar scripts T-SQL na instância de destino.

Para obter um Início Rápido que mostra como restaurar um backup de banco de dados em uma instância gerenciada usando uma credencial SAS, confira [Restauração do backup para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorar aplicativos

Depois de concluir a migração para Instância Gerenciada, você deve acompanhar o comportamento do aplicativo e o desempenho da sua carga de trabalho. Este processo inclui as seguintes atividades:
- [Compare o desempenho da carga de trabalho em execução na Instância Gerenciada](#compare-performance-with-the-baseline) com a [linha de base de desempenho que você criou no SQL Server de origem](#create-performance-baseline).
- [Monitore](#monitor-performance) continuamente o desempenho de sua carga de trabalho para identificar possíveis problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Compare o desempenho com a linha de base

A primeira atividade que você precisaria tomar imediatamente após a migração bem sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho da linha de base. O objetivo desta atividade é confirmar que o desempenho da carga de trabalho em sua Instância Gerenciada atende às suas necessidades. 

A migração do banco de dados para a Instância Gerenciada mantém as configurações do banco de dados e seu nível de compatibilidade original na maioria dos casos. As configurações originais são preservadas sempre que possível, a fim de reduzir o risco de algumas degradações de desempenho em comparação com o sql server de origem. Se o nível de compatibilidade de um banco de dados de usuário fosse 100 ou superior antes da migração, ele permaneceria o mesmo após a migração. Se o nível de compatibilidade de um banco de dados de usuário era 90 antes da migração, no banco de dados atualizado, o nível de compatibilidade é definido como 100, que é o nível de compatibilidade mais baixo compatível com a instância gerenciada. O nível de compatibilidade dos bancos de dados do sistema é 140. Uma vez que a migração para instância gerenciada está realmente migrando para a versão mais recente do SQL Server Database Engine, você deve estar ciente de que você precisa testar novamente o desempenho de sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, certifique-se de que você tenha concluído as seguintes atividades:
- Alinhe suas configurações na Instância Gerenciada com as configurações da instância sql server de origem, investigando várias instâncias, banco sumário, configurações e configurações. Certifique-se de que você não alterou configurações como níveis de compatibilidade ou criptografia antes de executar a primeira comparação de desempenho ou aceitar o risco de que alguns dos novos recursos que você habilitou possam afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade do banco de dados somente após o monitoramento do desempenho.
- Implemente [as diretrizes de práticas recomendadas](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) de armazenamento para fins gerais, como pré-alocar o tamanho dos arquivos para obter o melhor desempenho.
- Conheça as principais diferenças de ambiente que podem causar as diferenças de desempenho entre a Instância Gerenciada e o [SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identifique os riscos que podem afetar o desempenho.
- Certifique-se de manter a loja de consulta ativada e a sintonia automática em sua instância gerenciada. Esses recursos permitem medir o desempenho da carga de trabalho e corrigir automaticamente os possíveis problemas de desempenho. Saiba como usar o Query Store como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração do nível de compatibilidade do banco de dados, conforme explicado em [Manter a estabilidade de desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Depois de preparar o ambiente que é comparável tanto quanto possível com o seu ambiente no local, você pode começar a executar sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que você mediu [enquanto cria o desempenho da linha de base de suas medidas de carga de trabalho no SQL Server de origem](#create-performance-baseline).
Como resultado, você deve comparar parâmetros de desempenho com a linha de base e identificar diferenças críticas.

> [!NOTE]
> Em muitos casos, você não seria capaz de obter exatamente o desempenho correspondente em Instância Gerenciada e SQL Server. Instance Gerenciado é um mecanismo de banco de dados SQL Server, mas a infra-estrutura e a configuração de alta disponibilidade na Instância Gerenciada podem introduzir alguma diferença. Você pode esperar que algumas consultas seriam mais rápidas, enquanto algumas outras podem ser mais lentas. O objetivo da comparação é verificar se o desempenho da carga de trabalho na Instância Gerenciada corresponde ao desempenho no SQL Server (em média) e identificar se há alguma consulta crítica com o desempenho que não corresponda ao seu desempenho original.

O resultado da comparação de desempenho pode ser:
- O desempenho da carga de trabalho na Instância Gerenciada está alinhado ou melhor que o desempenho da carga de trabalho no SQL Server. Neste caso, você confirmou com sucesso que a migração é bem sucedida.
- A maioria dos parâmetros de desempenho e as consultas na carga de trabalho funcionam bem, com algumas exceções com desempenho degradado. Neste caso, você precisaria identificar as diferenças e sua importância. Se houver algumas consultas importantes com desempenho degradado, você deve investigar se os planos SQL subjacentes foram alterados ou as consultas estão atingindo alguns limites de recursos. A mitigação neste caso pode ser aplicar algumas dicas sobre as consultas críticas (por exemplo, o nível de compatibilidade alterado, estimador de cardinalidade legado) diretamente ou usando guias de planos, reconstruir ou criar estatísticas e índices que possam afetar os planos. 
- A maioria das consultas são mais lentas na Instância Gerenciada em comparação com o sql server de origem. Neste caso, tente identificar as causas básicas da diferença, como atingir algum limite de [recursos,]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) como limites de IO, limite de memória, limite de registro de instâncias, etc. Se não houver limites de recursos que possam causar a diferença, tente alterar o nível de compatibilidade do banco de dados ou alterar as configurações do banco de dados, como a estimativa de cardinalidade legado e reiniciar o teste. Revise as recomendações fornecidas pelas exibições de Instância Gerenciada ou Query Store para identificar as consultas que regrediram o desempenho.

> [!IMPORTANT]
> A instância gerenciada tem recurso de correção automática de plano incorporado que é habilitado por padrão. Esse recurso garante que as consultas que funcionaram bem na pasta não se degradariam no futuro. Certifique-se de que esse recurso está ativado e que você executou a carga de trabalho por tempo suficiente com as configurações antigas antes de alterar novas configurações, a fim de habilitar a Instância Gerenciada para aprender sobre o desempenho e os planos da linha de base.

Faça a alteração dos parâmetros ou atualize os níveis de serviço para convergir para a configuração ideal até obter o desempenho da carga de trabalho que se ajusta às suas necessidades.

### <a name="monitor-performance"></a>Monitorar desempenho

A Instância Gerenciada fornece muitas ferramentas avançadas para monitoramento e solução de problemas, e você deve usá-las para monitorar o desempenho em sua instância. Alguns dos parâmetros que você precisaria monitorar são:
- O uso da CPU na instância para determinar se o número de vCores que você provisionou é a correspondência certa para sua carga de trabalho.
- Expectativa de vida de página em sua instância gerenciada para determinar [se você precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Espere estatísticas `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` como ou que dirão se você tem problemas de IO de armazenamento, especialmente no nível de Propósito Geral, onde você pode precisar pré-alocar arquivos para obter melhor desempenho de IO.

## <a name="leverage-advanced-paas-features"></a>Aproveite os recursos avançados do PaaS

Uma vez que você esteja em uma plataforma totalmente gerenciada e tenha verificado que os desempenhos da carga de trabalho estão correspondendo ao desempenho da carga de trabalho do SQL Server, aproveite as vantagens que são fornecidas automaticamente como parte do serviço sql database. 

Mesmo que você não faça algumas alterações na instância gerenciada durante a migração, há grandes chances de que você ligue alguns dos novos recursos enquanto estiver operando sua instância para aproveitar as últimas melhorias no mecanismo de banco de dados. Algumas alterações só são ativadas depois que o [nível de compatibilidade do banco de dados foi alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Por exemplo, não é necessário criar backups na instância gerenciada – o serviço faz backups para você automaticamente. Não é mais necessário preocupar-se com agendamento, execução e gerenciamento de backups. A instância gerenciada fornece a capacidade de restaurar a qualquer ponto dentro deste período de retenção usando [pitr (Point in Time Recovery, recuperação](sql-database-recovery-using-backups.md#point-in-time-restore)de tempo) . Além disso, não é necessário preocupar-se com a configuração de alta disponibilidade, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

Para reforçar a segurança, considere usar [a autenticação do Diretório Ativo do Azure,](sql-database-security-overview.md) [auditoria,](sql-database-managed-instance-auditing.md) [detecção de ameaças,](sql-database-advanced-data-security.md) [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e [mascaramento dinâmico de dados](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Além dos recursos avançados de gerenciamento e segurança, a Instância Gerenciada fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorar e ajustar sua carga de trabalho.](sql-database-monitor-tune-overview.md) [A análise SQL do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) permite monitorar um grande conjunto de instâncias gerenciadas e centralizar o monitoramento de um grande número de instâncias e bancos de dados. [O ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) na Instância Gerenciada monitora continuamente o desempenho das estatísticas de execução do plano SQL e corrige automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre instâncias gerenciadas, confira [O que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para obter um tutorial que inclua uma restauração do backup, consulte [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial mostrando a migração usando dMS, consulte [Migrar seu banco de dados local para uma instância gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
