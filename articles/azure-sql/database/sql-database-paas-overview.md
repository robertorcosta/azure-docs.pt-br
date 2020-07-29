---
title: O que é o serviço do Banco de Dados SQL do Azure?
description: 'Obtenha uma introdução ao banco de dados SQL: detalhes técnicos e recursos do RDBMS (sistema de gerenciamento de banco de dados relacional) da Microsoft na nuvem.'
keywords: introdução ao sql, introdução ao sql, o que é o banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: fcad4f02f3fdfcbdc95617da7344d06feb70d1af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343244"
---
# <a name="what-is-azure-sql-database"></a>O que é o Banco de Dados SQL do Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O banco de dados SQL do Azure é um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que lida com a maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches, backups e monitoramento sem envolvimento do usuário. O banco de dados SQL do Azure está sempre em execução na versão estável mais recente do mecanismo de banco de dados SQL Server e no sistema operacional com patches com 99,99% de disponibilidade. Os recursos de PaaS criados no banco de dados SQL do Azure permitem que você se concentre nas atividades de administração e otimização de banco de dados específicas de domínio que são essenciais para sua empresa.

Com o banco de dados SQL do Azure, você pode criar uma camada de armazenamento altamente disponível e de alto desempenho para os aplicativos e soluções no Azure. O banco de dados SQL pode ser a escolha certa para uma variedade de aplicativos de nuvem modernos, pois permite processar dados relacionais e [estruturas não relacionais](../multi-model-features.md), como grafos, JSON, espaciais e XML.

O banco de dados SQL do Azure é baseado na versão estável mais recente do [mecanismo de banco de dados Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Você pode usar recursos avançados de processamento de consulta, como [tecnologias de alto desempenho na memória](../in-memory-oltp-overview.md) e [processamento de consulta inteligente](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Na verdade, os recursos mais recentes de SQL Server são liberados primeiro para o banco de dados SQL e, em seguida, para SQL Server si mesmo. Você Obtém os recursos de SQL Server mais recentes sem sobrecarga para aplicação de patch ou atualização, testados em milhões de bancos de dados. 

O banco de dados SQL permite que você defina e dimensione facilmente o desempenho em dois modelos de compra diferentes: um [modelo de compra baseado em vCore](service-tiers-vcore.md) e um [modelo de compra baseado em DTU](service-tiers-dtu.md). O banco de dados SQL é um serviço totalmente gerenciado que tem alta disponibilidade interna, backups e outras operações de manutenção comuns. A Microsoft trata de todas as correções e atualizações do código do sistema operacional e do SQL. Você não precisa gerenciar a infraestrutura subjacente.

## <a name="deployment-models"></a>Modelos de implantação

O banco de dados SQL do Azure fornece as seguintes opções de implantação para um banco de dados:

- Um [banco de dados individual](single-database-overview.md) representa um banco de dados isolado e totalmente gerenciado. Você pode usar essa opção se tiver aplicativos de nuvem modernos e microservices que precisam de uma única fonte de dados confiável. Um banco de dados individual é semelhante a um banco de dados [independente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) no mecanismo de [banco de dados SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- O [pool elástico](elastic-pool-overview.md) é uma coleção de [bancos de dados individuais](single-database-overview.md) com um conjunto compartilhado de recursos, como CPU ou memória. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico.

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o banco de dados SQL e SQL Server, bem como as diferenças entre as diferentes opções do banco de dados SQL do Azure, consulte [recursos do banco de dados SQL](features-comparison.md).

O banco de dados SQL fornece desempenho previsível com vários tipos de recursos, camadas de serviço e tamanhos de computação. Ele fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente interna, escalabilidade e disponibilidade globais e opções de segurança avançadas. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e na aceleração do tempo de colocação no mercado, em vez de gerenciar máquinas virtuais e infraestrutura. No momento, o banco de dados SQL está em 38 data centers em todo o mundo, para que você possa executar seu banco de dados em um datacenter perto de você.

## <a name="scalable-performance-and-pools"></a>Pools e desempenho dimensionável

Você pode definir a quantidade de recursos atribuídos. 
- Com bancos de dados individuais, cada um deles é isolado de outros e é portátil. Cada uma tem sua própria quantidade garantida de recursos de computação, memória e armazenamento. A quantidade de recursos atribuídos ao banco de dados é dedicada a esse banco de dados, e não é compartilhada com outros banco de dados no Azure. Você pode dimensionar dinamicamente os [recursos de banco de dados individual](single-database-scale.md) para cima e para baixo. A opção de banco de dados individual fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, você pode obter de 1 a 80 vCores ou 32 GB a 4 TB. A [camada de serviço de hiperescala](service-tier-hyperscale.md) para bancos de dados individuais permite que você dimensione para 100 TB, com recursos rápidos de backup e restauração.
- Com os pools elásticos, você pode atribuir recursos que são compartilhados por todos os bancos de dados no pool. Você pode criar um novo banco de dados ou movê-los para um pool de recursos para maximizar o uso de recursos e economizar dinheiro. Essa opção também oferece a capacidade de dimensionar dinamicamente os [recursos do pool elástico](elastic-pool-scale.md) para cima e para baixo.

Você pode criar seu primeiro aplicativo em um banco de dados pequeno e único com um baixo custo por mês na camada de serviço de uso geral. Você pode alterar sua camada de serviço manualmente ou programaticamente a qualquer momento para a camada de serviço crítica para os negócios, para atender às necessidades da sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa quando precisa deles.

A *escalabilidade dinâmica* é diferente do *dimensionamento automático*. O dimensionamento automático é quando um serviço pode ser dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmico permite o redimensionamento manual sem tempo de inatividade. A opção banco de dados único dá suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para uma experiência mais automática, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados. Outra opção é usar scripts que podem ajudar a automatizar a escalabilidade de um único banco de dados. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um banco de dados individual](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

O banco de dados SQL oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore](service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade de memória e a quantidade e a velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos. Para obter mais informações sobre o Benefício Híbrido do Azure, consulte a seção "perguntas frequentes" mais adiante neste artigo.
- O [modelo de compra baseado em DTU](service-tiers-dtu.md) oferece uma mistura de computação, memória e recursos de e/s em três camadas de serviço, para dar suporte a cargas de trabalho leves de banco de dados. Os tamanhos da computação dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo sem servidor](serverless-tier-overview.md) dimensiona automaticamente a computação com base na demanda de carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

### <a name="service-tiers"></a>Camadas de serviço

O banco de dados SQL do Azure oferece três camadas de serviço que são projetadas para diferentes tipos de aplicativos:
- Camada de serviço [uso geral/Standard](service-tier-general-purpose.md) projetada para cargas de trabalho comuns. Ele oferece opções de armazenamento e computação balanceadas e voltadas para o orçamento.
- Camada de serviço [comercialmente crítico/Premium](service-tier-business-critical.md) projetada para aplicativos OLTP com alta taxa de transação e e/s de latência mais baixa. Ele oferece a resiliência mais alta para falhas usando várias réplicas isoladas.
- Camada de serviço de [hiperescala](service-tier-hyperscale.md) projetada para um banco de dados OLTP muito grande e a capacidade de fazer o dimensionamento automático do armazenamento e escala de computação de forma fluida.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Padrões de uso imprevisíveis podem dificultar o gerenciamento de custos e seu modelo de negócios. Os [pools elásticos](elastic-pool-overview.md) são projetados para resolver esse problema. Você aloca recursos de desempenho para um pool em vez de um banco de dados individual. Você paga pelos recursos de desempenho coletivo do pool em vez de para o desempenho de um banco de dados individual.

   ![Gráfico que mostra pools elásticos nas edições Basic, Standard e Premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Com os pools elásticos, você não precisa se concentrar na discagem e redução do desempenho do banco de dados, pois a demanda por recursos flutua. Os bancos de dados em pool consomem os recursos de desempenho do pool elástico conforme necessário. Os bancos de dados em pool consomem, mas não excedem os limites do pool, para que seu custo permaneça previsível mesmo que o uso individual do banco de dados não.

Você pode [Adicionar e Remover bancos de dados para o pool](elastic-pool-overview.md), dimensionando seu aplicativo de alguns bancos de dados para milhares, tudo isso dentro de um orçamento que você controla. Você também pode controlar os recursos mínimo e máximo disponíveis para bancos de dados no pool, para garantir que nenhum banco de dados no pool use todos os recursos de pool e que cada banco de dados em pool tenha uma quantidade mínima garantida de recursos. Para saber mais sobre padrões de design para aplicativos SaaS (software como serviço) que usam pools elásticos, consulte [padrões de design para aplicativos SaaS multilocatários com o banco de dados SQL](saas-tenancy-app-design-patterns.md).

Scripts podem ajudar com o monitoramento e dimensionamento pools elásticos. Para obter um exemplo, consulte [usar o PowerShell para monitorar e dimensionar um pool elástico no banco de dados SQL do Azure](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bancos de dados individuais a bancos de dados em pool

Você pode misturar bancos de dados individuais com pools elásticos e alterar as camadas de serviço de bancos de dados individuais e pools elásticos para se adaptar à sua situação. Você também pode misturar e combinar outros serviços do Azure com o banco de dados SQL para atender às suas necessidades exclusivas de design de aplicativo, impulsionar as eficiências de custo e recursos e desbloquear novas oportunidades de negócios.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Monitoramento abrangente e recursos de alerta

O banco de dados SQL do Azure fornece recursos avançados de monitoramento e solução de problemas que ajudam você a obter informações mais aprofundadas sobre as características da carga de trabalho. Esses recursos e ferramentas incluem:
 - Os recursos de monitoramento internos fornecidos pela versão mais recente do mecanismo de banco de dados SQL Server. Eles permitem que você encontre informações de desempenho em tempo real. 
 - Recursos de monitoramento de PaaS fornecidos pelo Azure que permitem que você monitore e solucione problemas de um grande número de instâncias de banco de dados.

[Repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), um recurso interno de monitoramento de SQL Server, registra o desempenho de suas consultas em tempo real e permite que você identifique os possíveis problemas de desempenho e os principais consumidores de recursos. O ajuste automático e as recomendações fornecem conselhos sobre as consultas com o desempenho regressivo e índices ausentes ou duplicados. O ajuste automático no banco de dados SQL permite que você aplique manualmente os scripts que podem corrigir os problemas ou deixe que o banco de dados SQL aplique a correção. O banco de dados SQL também pode testar e verificar se a correção fornece algum benefício e reter ou reverter a alteração dependendo do resultado. Além dos recursos de Repositório de Consultas e de ajuste automático, você pode usar [DMVs e XEvent](monitoring-with-dmvs.md) padrão para monitorar o desempenho da carga de trabalho.

O Azure fornece ferramentas [internas de monitoramento](performance-guidance.md) e [alerta](alerts-insights-configure-portal.md) de desempenho, combinadas com classificações de desempenho, que permitem monitorar o status de milhares de bancos de dados. Usando essas ferramentas, você pode avaliar rapidamente o impacto de escalar ou reduzir verticalmente, com base em suas necessidades de desempenho atuais ou projetadas. Além disso, o banco de dados SQL pode [emitir métricas e logs de recursos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:

- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um preço baixo.
- **Hubs de eventos do Azure**: para integrar a telemetria do banco de dados SQL com sua solução de monitoramento Personalizada ou pipelines quentes.
- **Logs de Azure monitor**: para uma solução de monitoramento interna com relatórios, alertas e recursos de mitigação.

![Diagrama da arquitetura de monitoramento do Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Recursos de disponibilidade

O banco de dados SQL do Azure permite que sua empresa continue a operar durante as interrupções. Em um ambiente de SQL Server tradicional, você geralmente tem pelo menos duas máquinas configuradas localmente. Essas máquinas possuem cópias exatas, em sincronia, mantidas dos dados para proteger contra uma falha de um único computador ou componente. Esse ambiente fornece alta disponibilidade, mas não protege contra um desastre natural destruindo seu datacenter.

A recuperação de desastres pressupõe que um evento catastrófico seja traduzido geograficamente para ter outro computador ou conjunto de computadores com uma cópia dos seus dados de longe. No SQL Server, você pode usar Always On grupos de disponibilidade em execução no modo assíncrono para obter esse recurso. Muitas vezes, as pessoas não querem esperar que a replicação ocorra antes de confirmar uma transação, portanto, há potencial para perda de dados quando você faz failovers não planejados.

Os bancos de dados nas camadas de serviço Premium e Comercialmente Crítico já [fazem algo semelhante](high-availability-sla.md#premium-and-business-critical-service-tier-availability) à sincronização de um grupo de disponibilidade. Os bancos de dados em camadas de serviço inferiores fornecem redundância por meio do armazenamento usando um [mecanismo diferente, mas equivalente](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability). A lógica interna ajuda a proteger contra uma falha de máquina única. O recurso de replicação geográfica ativa oferece a capacidade de proteger contra desastres em que uma região inteira é destruída.

Zonas de Disponibilidade do Azure tenta se proteger contra a interrupção de um único datacenter criando dentro de uma única região. Ele ajuda você a se proteger contra a perda de energia ou rede para um prédio. No banco de dados SQL, você coloca as diferentes réplicas em diferentes zonas de disponibilidade (prédios diferentes, efetivamente).

Na verdade, o [SLA (](https://azure.microsoft.com/support/legal/sla/) contrato de nível de serviço) do Azure, equipado com uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução 24/7. A plataforma Azure gerencia totalmente todos os bancos de dados e garante que não haja perda e um alto percentual de disponibilidade de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. A disponibilidade padrão é obtida por uma separação de camadas de computação e armazenamento. A disponibilidade Premium é obtida com a integração da computação e do armazenamento em um único nó para o desempenho e, em seguida, a implementação de uma tecnologia semelhante à Always On grupos de disponibilidade. Para conferir uma discussão completa sobre os recursos de alta disponibilidade do Banco de Dados SQL do Azure, veja [Disponibilidade do Banco de Dados SQL](high-availability-sla.md). 

Além disso, o banco de dados SQL fornece recursos internos de [continuidade de negócios e escalabilidade global](business-continuity-high-availability-disaster-recover-hadr-overview.md) . Elas incluem:

- [Backups automáticos](automated-backups-overview.md):

  O banco de dados SQL executa automaticamente backups completos, diferenciais e de log de transações de bancos de dados para permitir que você restaure em qualquer ponto no tempo. Para bancos de dados individuais e bancos de dados em pool, você pode configurar o banco de dados SQL para armazenar backups de banco de dados completos no armazenamento do Azure para retenção de backup de longo prazo. Para instâncias gerenciadas, você também pode executar backups somente cópia para retenção de backup de longo prazo.

- [Restaurações point-in-time](recovery-using-backups.md):

  Todas as opções de implantação de banco de dados SQL dão suporte à recuperação para qualquer ponto no tempo dentro do período de retenção de backup automático de qualquer banco de dados.
- [Replicação geográfica ativa](active-geo-replication-overview.md):

  As opções de banco de dados individual e bancos de dados em pool permitem que você configure até quatro bancos de dados secundários legíveis no mesmo ou em datacenters do Azure distribuídos globalmente. Por exemplo, se você tiver um aplicativo SaaS com um banco de dados de catálogo que tem um alto volume de transações simultâneas somente leitura, use a replicação geográfica ativa para habilitar a escala de leitura global. Isso remove afunilamentos no primário que são devido a cargas de trabalho de leitura. Para instâncias gerenciadas, use grupos de failover automático.
- [Grupos de failover automático](auto-failover-group-overview.md):

  Todas as opções de implantação do banco de dados SQL permitem que você use grupos de failover para habilitar a alta disponibilidade e o balanceamento de carga em escala global. Isso inclui a replicação geográfica transparente e o failover de grandes conjuntos de bancos de dados, pools elásticos e instâncias gerenciadas. Os grupos de failover permitem a criação de aplicativos SaaS distribuídos globalmente, com sobrecarga de administração mínima. Isso deixa toda a orquestração de monitoramento, roteamento e failover complexos para o banco de dados SQL.
- [Bancos de dados com redundância de zona](high-availability-sla.md):

  O Banco de Dados SQL permite provisionar bancos de dados Premium ou Business Critical ou pools elásticos em várias zonas de disponibilidade. Como esses bancos de dados e pools elásticos têm várias réplicas redundantes para alta disponibilidade, colocar essas réplicas em várias zonas de disponibilidade fornece maior resiliência. Isso inclui a capacidade de recuperar automaticamente das falhas de escala do datacenter, sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência interna

Com o banco de dados SQL, você obtém inteligência interna que ajuda a reduzir drasticamente os custos de execução e gerenciamento de bancos de dados e que maximiza o desempenho e a segurança do seu aplicativo. Executando milhões de cargas de trabalho de clientes em todo o tempo, o banco de dados SQL coleta e processa uma grande quantidade de data de telemetria, ao mesmo tempo em que respeita totalmente a privacidade do cliente. Vários algoritmos avaliam continuamente os dados de telemetria para que o serviço possa aprender e se adaptar ao seu aplicativo.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoramento e ajuste de desempenho automáticos

O Banco de Dados SQL fornece informações detalhadas sobre as consultas que você precisa monitorar. O banco de dados SQL aprende sobre seus padrões de banco de dados e permite que você adapte seu esquema de banco de dados à sua carga de trabalho. O Banco de Dados SQL fornece [recomendações de ajuste de desempenho](database-advisor-implement-performance-recommendations.md) em que você pode examinar as ações de ajuste e aplicá-las.

No entanto, o monitoramento constante de um banco de dados é uma tarefa difícil e entediante, especialmente quando você está lidando com muitos bancos de dados. [Intelligent insights](intelligent-insights-overview.md) faz esse trabalho para você monitorando automaticamente o desempenho do banco de dados SQL em escala. Ele informa sobre problemas de degradação de desempenho, identifica a causa raiz de cada problema e fornece recomendações de melhoria de desempenho quando possível.

O gerenciamento de um grande número de bancos de dados pode ser impossível de fazer com eficiência, mesmo com todas as ferramentas e relatórios disponíveis que o banco de dados SQL e o Azure fornecem. Em vez de monitorar e ajustar seu banco de dados manualmente, você pode considerar a delegação de algumas das ações de monitoramento e ajuste ao banco de dados SQL usando o [ajuste automático](automatic-tuning-overview.md). O banco de dados SQL aplica automaticamente recomendações, testes e verifica cada uma de suas ações de ajuste para garantir que o desempenho continue a melhorar. Dessa forma, o banco de dados SQL se adapta automaticamente à sua carga de trabalho de forma controlada e segura. O ajuste automático significa que o desempenho do banco de dados é monitorado cuidadosamente e comparado antes e depois de cada ação de ajuste. Se o desempenho não melhorar, a ação de ajuste será revertida.

Muitos de nossos parceiros que executam [aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md) sobre o banco de dados SQL estão contando com o ajuste de desempenho automático para garantir que seus aplicativos sempre tenham desempenho estável e previsível. Para eles, esse recurso reduz o risco de ter um incidente de desempenho no meio da noite. Além disso, como parte de sua base de clientes também usa SQL Server, eles estão usando as mesmas recomendações de indexação fornecidas pelo banco de dados SQL para ajudar seus SQL Server clientes.

Dois aspectos de ajuste automático estão [disponíveis no banco de dados SQL](automatic-tuning-overview.md):

- **Gerenciamento automático de índice**: identifica os índices que devem ser adicionados ao seu banco de dados e os que devem ser removidos.
- **Correção automática de plano**: identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consulta adaptável

Você pode usar o [processamento de consulta adaptável](/sql/relational-databases/performance/intelligent-query-processing), incluindo a execução intercalada para funções com valor de tabela de várias instruções, comentários de concessão de memória de modo de lote e junções adaptáveis de modo de lote. Cada um desses recursos de processamento de consulta adaptável aplica técnicas de "aprendizado e adaptação" semelhantes, ajudando a resolver problemas de desempenho relacionados a problemas de otimização de consulta historicamente inmanejáveis.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O banco de dados SQL fornece uma variedade de [recursos internos de segurança e conformidade](../../active-directory/identity-protection/security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Microsoft certificou o banco de dados SQL do Azure (todas as opções de implantação) contra vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

### <a name="advance-threat-protection"></a>Proteção avançada contra ameaças

A Segurança de Dados Avançada do SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ele inclui a funcionalidade de descoberta e classificação de dados confidenciais, o gerenciamento de vulnerabilidades de banco de dado e a detecção de atividades anormais que podem indicar uma ameaça ao seu banco de dados. Fornece um local único para habilitar e gerenciar essas funcionalidades.

- [Descoberta e classificação de dados](data-discovery-and-classification-overview.md):

  Esse recurso fornece recursos incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos. Ele fornece visibilidade sobre o estado de classificação do banco de dados e controla o acesso a dados confidenciais no banco e além de suas bordas.
- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Esse serviço pode descobrir, controlar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- [Detecção de ameaças](threat-detection-configure.md):

  Esse recurso detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas de detecção de ameaças fornecem detalhes da atividade suspeita e recomendam ações sobre como investigar e mitigar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria de segurança e conformidade

A [Auditoria](../../azure-sql/database/auditing-overview.md) acompanha eventos do banco de dados e os grava em um log de auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Criptografia de dados

O banco de dados SQL ajuda a proteger os seus dados fornecendo criptografia. Para dados em movimento, ele usa a [segurança da camada de transporte](https://support.microsoft.com/kb/3135244). Para dados em repouso, ele usa a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Para dados em uso, ele usa [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O Banco de Dados SQL permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](authentication-aad-overview.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory dá suporte à [autenticação multifator](authentication-mfa-ssms-overview.md) para aumentar a segurança de dados e aplicativos, ao mesmo tempo em que dá suporte a um processo de logon único.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de usar

O Banco de Dados SQL torna a compilação e o gerenciamento de aplicativos mais fáceis e produtivos. O Banco de Dados SQL permite a você focar no que há de melhor: compilar aplicativos ótimos. Você pode gerenciar e desenvolver no banco de dados SQL usando as ferramentas e as habilidades que você já tem.

|Ferramenta|Descrição|
|:---|:---|
|[O portal do Azure](https://portal.azure.com/)|Um aplicativo baseado na Web para gerenciar todos os serviços do Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Uma ferramenta de banco de dados de plataforma cruzada que é executada no Windows, no MacOS e no Linux.|
|[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|Um aplicativo cliente gratuito e que pode ser baixado para gerenciar qualquer infraestrutura do SQL, de SQL Server para o banco de dados SQL.|
|[SQL Server Data Tools no Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|Um aplicativo cliente gratuito e que pode ser baixado para o desenvolvimento de SQL Server bancos de dados relacionais, bancos de dados no banco de dados SQL do Azure, pacotes de Integration Services, modelos de Analysis Services data e relatórios de Reporting Services.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Um editor de código aberto gratuito, que pode ser baixado para Windows, macOS e Linux. Ele dá suporte a extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, o Banco de Dados SQL do Azure e o SQL Data Warehouse.|

O banco de dados SQL dá suporte à criação de aplicativos com Python, Java, Node.js, PHP, Ruby e .NET no macOS, Linux e Windows. O Banco de Dados SQL dá suporte às mesmas [bibliotecas de conexão](connect-query-content-reference-guide.md#libraries) do SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Perguntas frequentes sobre o banco de dados SQL

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando ocorre a aplicação de um tempo de inatividade?

Não. O impacto de patch geralmente não será perceptível, se você [empregar lógica de repetição](develop-overview.md#resiliency) no aplicativo. Para obter mais informações, consulte [planejando eventos de manutenção do Azure no banco de dados SQL do Azure](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver-se com a equipe de engenharia do SQL Server

- [Intercâmbio de pilha do DBA](https://dba.stackexchange.com/questions/tagged/sql-server): faça perguntas sobre administração de banco de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): faça perguntas de desenvolvimento.
- [Microsoft Q&uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html): faça perguntas técnicas.
- [Comentários](https://aka.ms/sqlfeedback): Relatar bugs e solicitar recursos.
- [Reddit](https://www.reddit.com/r/SQLServer/): discutir SQL Server.

## <a name="next-steps"></a>Próximas etapas

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para obter comparações de custo e calculadoras referentes a bancos de dados individuais e pools elásticos.
- Consulte estes guias de início rápido para começar:

  - [Criar um banco de dados no Portal do Azure](single-database-create-quickstart.md)  
  - [Criar um banco de dados com o CLI do Azure](az-cli-script-samples-content-guide.md)
  - [Criar um banco de dados usando o PowerShell](powershell-script-content-guide.md)

- Para ver vários exemplos da CLI do Azure e do PowerShell, consulte:
  - [Exemplos da CLI do Azure para o Banco de Dados SQL do Azure](az-cli-script-samples-content-guide.md)
  - [Exemplos do Azure PowerShell para o Banco de Dados SQL do Azure](powershell-script-content-guide.md)

- Para obter informações sobre novos recursos conforme eles são anunciados, consulte o [roteiro do Azure para o banco de dados SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/topics/database), no qual SQL Server os membros da equipe do produto sobre notícias e recursos do banco de dados SQL.

