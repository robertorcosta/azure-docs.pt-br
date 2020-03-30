---
title: O que é o serviço do Banco de Dados SQL do Azure?
description: 'Obtenha uma introdução ao SQL Database: detalhes técnicos e recursos do sistema de gerenciamento de banco de dados relacional da Microsoft (RDBMS) na nuvem.'
keywords: introdução ao sql, introdução ao sql, o que é o banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 209b4136678e6f04666b4a2b6180f4768bf6afc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500821"
---
# <a name="what-is-the-azure-sql-database-service"></a>O que é o serviço do Banco de Dados SQL do Azure?

O Azure SQL Database é um banco de dados relacional de propósito geral, fornecido como um serviço gerenciado. Com ele, você pode criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para os aplicativos e soluções no Azure. O Banco de Dados SQL pode ser a escolha certa para uma variedade de aplicações modernas em nuvem, pois permite processar dados relacionais e [estruturas não-relacionais,](sql-database-multi-model-features.md)como gráficos, JSON, espacial e XML.

É baseado na versão estável mais recente do mecanismo de banco de [dados Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Você pode usar recursos avançados de processamento de consultas, como [tecnologias de memória em memória de alto desempenho](sql-database-in-memory.md) e processamento inteligente de [consultas.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) Na verdade, os recursos mais novos do SQL Server são liberados primeiro para o SQL Database e, em seguida, para o próprio SQL Server. Você recebe os mais novos recursos do SQL Server sem sobrecarga para patches ou atualização, testados em milhões de bancos de dados. 

O Banco de Dados SQL permite definir e dimensionar facilmente o desempenho em dois modelos de compra diferentes: um [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e um modelo de compra baseado em [DTU.](sql-database-service-tiers-dtu.md) O SQL Database é um serviço totalmente gerenciado que possui alta disponibilidade incorporada, backups e outras operações comuns de manutenção. A Microsoft lida com todos os patches e atualizações do SQL e do código do sistema operacional. Você não tem que gerenciar a infra-estrutura subjacente.

> [!NOTE]
> Para termos relevantes e suas definições, consulte o [glossário dos termos do Banco de Dados SQL](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modelos de implantação

O banco de dados SQL do Azure fornece as seguintes opções de implantação para um banco de dados SQL do Azure:

![Diagrama de opções de implantação](./media/sql-database-technical-overview/deployment-options.png)

- [O banco de dados único](sql-database-single-database.md) representa um banco de dados totalmente gerenciado e isolado. Você pode usar essa opção se tiver aplicativos e microsserviços modernos em nuvem que precisam de uma única fonte de dados confiável. Um único banco de dados é semelhante a um [banco de dados contido](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) no Microsoft [SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [A instância gerenciada](sql-database-managed-instance.md) é uma instância totalmente gerenciada do [Microsoft SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Ele contém um conjunto de bancos de dados que podem ser usados juntos. Use essa opção para facilitar a migração de bancos de dados SQL Server no local para a nuvem do Azure e para aplicativos que precisam usar os recursos de banco de dados que o SQL Server Database Engine fornece.
- [O grupo elástico](sql-database-elastic-pool.md) é uma coleção de [bancos](sql-database-single-database.md) de dados únicos com um conjunto compartilhado de recursos, como CPU ou memória. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico.

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o Banco de Dados SQL e o SQL Server, bem como as diferenças entre as diferentes opções de implantação do Banco de Dados SQL do Azure, consulte os [recursos do Banco de Dados SQL](sql-database-features.md).

O Banco de Dados SQL oferece desempenho previsível com vários tipos de recursos, níveis de serviço e tamanhos de computação. Ele fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente incorporada, escalabilidade e disponibilidade globais e opções avançadas de segurança. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e na aceleração do seu tempo de mercado, em vez de gerenciar máquinas virtuais e infra-estrutura. O serviço SQL Database está atualmente em 38 data centers em todo o mundo, para que você possa executar seu banco de dados em um data center perto de você.

## <a name="scalable-performance-and-pools"></a>Pools e desempenho dimensionável

Você pode definir a quantidade de recursos atribuídos. 
- Com bancos de dados únicos, cada banco de dados é isolado de outros e é portátil. Cada um tem sua própria quantidade garantida de recursos de computação, memória e armazenamento. A quantidade de recursos atribuídos ao banco de dados é dedicada a esse banco de dados e não é compartilhada com outros bancos de dados no Azure. Você pode [dimensionar](sql-database-single-database-scale.md) dinamicamente os recursos de banco de dados para cima e para baixo. A opção de banco de dados único fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, você pode obter de 1 a 80 vCores, ou 32 GB a 4 TB. O [nível de serviço de hiperescala](sql-database-service-tier-hyperscale.md) para banco de dados único permite que você dimensione para 100 TB, com recursos rápidos de backup e restauração.
- Com piscinas elásticas, você pode atribuir recursos que são compartilhados por todos os bancos de dados no pool. Você pode criar um novo banco de dados ou mover os bancos de dados únicos existentes em um pool de recursos para maximizar o uso de recursos e economizar dinheiro. Essa opção também lhe dá a capacidade de dimensionar dinamicamente [os recursos elásticos do pool](sql-database-elastic-pool-scale.md) para cima e para baixo.
- Com instâncias gerenciadas, cada instância gerenciada é isolada de outras instâncias com recursos garantidos. Em uma instância gerenciada, os bancos de dados de instância compartilham um conjunto de recursos. Você pode [dimensionar](sql-database-managed-instance-resource-limits.md) dinamicamente os recursos de instância gerenciados para cima e para baixo.

Você pode construir seu primeiro aplicativo em um pequeno banco de dados único a um baixo custo por mês no nível de serviço de uso geral. Em seguida, você pode alterar seu nível de serviço manualmente ou programáticamente a qualquer momento para o nível de serviço crítico para os negócios, para atender às necessidades de sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas os recursos que precisa quando precisa deles.

*A escalabilidade dinâmica* é diferente da *autoescala*. O dimensionamento automático é quando um serviço pode ser dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmico permite o redimensionamento manual sem tempo de inatividade. A opção de banco de dados único suporta escalabilidade dinâmica manual, mas não em escala automática. Para uma experiência mais automática, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados. Outra opção é usar scripts que podem ajudar a automatizar a escalabilidade de um único banco de dados. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

O Banco de Dados SQL oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade de memória e a quantidade e a velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos. Para obter mais informações sobre o Azure Hybrid Benefit, consulte a seção "Perguntas frequentes" mais tarde neste artigo.
- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de recursos de computação, memória e I/O em três níveis de serviço, para oferecer suporte a cargas de trabalho leves e pesadas de banco de dados. Os tamanhos da computação dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo sem servidor](sql-database-serverless.md) dimensiona automaticamente a computação com base na demanda de carga de trabalho e fatura a quantidade de cálculo usada por segundo. O nível de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

### <a name="service-tiers"></a>Camadas de serviço

O Azure SQL Database oferece três níveis de serviço projetados para diferentes tipos de aplicativos:
- [General Purpose/Standard](sql-database-service-tier-general-purpose.md) service tier projetado para cargas de trabalho comuns. Oferece opções de computação e armazenamento balanceadas orientadas ao orçamento.
- Nível de serviço [Business Critical/Premium](sql-database-service-tier-business-critical.md) projetado para aplicativos OLTP com alta taxa de transação e I/O de menor latência. Oferece a maior resiliência às falhas usando várias réplicas isoladas.
- Nível de serviço [de hiperescala](sql-database-service-tier-hyperscale.md) projetado para banco de dados OLTP muito grande e a capacidade de armazenamento em escala automática e dimensionar a computação de forma fluida.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Padrões de uso imprevisíveis podem dificultar a gestão de custos e seu modelo de negócio. [Piscinas elásticas](sql-database-elastic-pool.md) são projetadas para resolver este problema. Você aloca recursos de desempenho para um pool em vez de um banco de dados individual. Você paga pelos recursos de desempenho coletivo do pool em vez do desempenho de um único banco de dados.

   ![Gráfico que mostra piscinas elásticas em edições básicas, padrão e premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com piscinas elásticas, você não precisa se concentrar em discar o desempenho do banco de dados para cima e para baixo à medida que a demanda por recursos flutua. Os bancos de dados em pool consomem os recursos de desempenho do pool elástico conforme necessário. Bancos de dados agrupados consomem, mas não excedem os limites do pool, então seu custo permanece previsível mesmo que o uso de banco de dados individual não o faça.

Você pode [adicionar e remover bancos de dados para o pool,](sql-database-elastic-pool-manage-portal.md)dimensionando seu aplicativo de um punhado de bancos de dados para milhares, tudo dentro de um orçamento que você controla. Você também pode controlar os recursos mínimos e máximos disponíveis para bancos de dados no pool, para garantir que nenhum banco de dados no pool use todos os recursos do pool, e que cada banco de dados agrupado tenha uma quantidade mínima garantida de recursos. Para saber mais sobre padrões de design para aplicativos de software como serviço (SaaS) que usam piscinas elásticas, consulte [padrões de design para aplicativos SaaS multi-inquilinos com banco de dados SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Scripts podem ajudar com o monitoramento e dimensionamento pools elásticos. Por exemplo, consulte [Use PowerShell para monitorar e dimensionar um pool elástico SQL no Banco de Dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Uma instância gerenciada não suporta piscinas elásticas. Em vez disso, uma instância gerenciada é uma coleção de bancos de dados de instância que compartilham recursos de instância gerenciada.

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bancos de dados individuais a bancos de dados em pool

Você pode misturar bancos de dados únicos com piscinas elásticas e alterar os níveis de serviço de bancos de dados únicos e piscinas elásticas para se adaptar à sua situação. Você também pode misturar e combinar outros serviços do Azure com o SQL Database para atender às suas necessidades exclusivas de design de aplicativos, aumentar o custo e a eficiência dos recursos e desbloquear novas oportunidades de negócios.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Monitoramento abrangente e recursos de alerta

O Azure SQL Database fornece recursos avançados de monitoramento e solução de problemas que ajudam você a obter insights mais profundos sobre as características da carga de trabalho. Esses recursos e ferramentas incluem:
 - Os recursos de monitoramento incorporados fornecidos pela versão mais recente do SQL Server Database Engine. Eles permitem que você encontre insights de desempenho em tempo real. 
 - Os recursos de monitoramento do PaaS fornecidos pelo Azure permitem monitorar e solucionar problemas de um grande número de instâncias de banco de dados.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), um recurso de monitoramento do SQL Server incorporado, registra o desempenho de suas consultas em tempo real e permite identificar os possíveis problemas de desempenho e os principais consumidores de recursos. Ajustes automáticos e recomendações fornecem conselhos sobre as consultas com o desempenho regredido e índices ausentes ou duplicados. O ajuste automático no Banco de Dados SQL permite que você aplique manualmente os scripts que podem corrigir os problemas ou deixe o SQL Database aplicar a correção. O Banco de Dados SQL também pode testar e verificar se a correção fornece algum benefício, e reter ou reverter a alteração dependendo do resultado. Além do Query Store e dos recursos de ajuste automático, você pode usar [DMVs](sql-database-monitoring-with-dmvs.md) padrão e XEvent para monitorar o desempenho da carga de trabalho.

O Azure fornece ferramentas de monitoramento e [alerta](sql-database-insights-alerts-portal.md) [de desempenho incorporadas,](sql-database-performance-guidance.md) combinadas com classificações de desempenho, que permitem monitorar o status de milhares de bancos de dados. Usando essas ferramentas, você pode avaliar rapidamente o impacto do dimensionamento para cima ou para baixo, com base nas suas necessidades atuais ou projetadas de desempenho. Além disso, o Banco de Dados SQL pode [emitir métrica e logs de diagnóstico](sql-database-metrics-diag-logging.md) para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:

- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um preço baixo.
- **Azure Event Hubs**: Para integrar a telemetria do Banco de Dados SQL com sua solução de monitoramento personalizado ou pipelines quentes.
- **Logs do Monitor do Azure**: Para uma solução de monitoramento incorporada com recursos de emissão de relatórios, alertas e mitigação.

![Diagrama da arquitetura de monitoramento do Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Recursos de disponibilidade

Em um ambiente tradicional do SQL Server, você geralmente tem pelo menos duas máquinas configuradas localmente. Essas máquinas têm cópias exatas e sincronicamente mantidas dos dados para proteger contra uma falha de uma única máquina ou componente. Este ambiente oferece alta disponibilidade, mas não protege contra um desastre natural que destrói seu data center.

A recuperação de desastres pressupõe que um evento catastrófico seja geograficamente localizado o suficiente para ter outra máquina ou conjunto de máquinas com uma cópia de seus dados longe. No SQL Server, você pode usar always on availability groups em execução no modo async para obter esse recurso. As pessoas muitas vezes não querem esperar que a replicação aconteça tão longe antes de cometer uma transação, então há potencial para perda de dados quando você faz failovers não planejados.

Bancos de dados nos níveis de serviços críticos premium e empresarial já [fazem algo muito semelhante](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) à sincronização de um grupo de disponibilidade. Bancos de dados em níveis de serviço inferiores fornecem redundância através do armazenamento usando um [mecanismo diferente, mas equivalente.](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) A lógica incorporada ajuda a proteger contra uma única falha da máquina. O recurso de georeplicação ativa oferece a capacidade de proteger contra desastres onde toda uma região é destruída.

O Azure Availability Zones tenta proteger contra a paralisação de um único edifício de datacenter em uma única região. Ele ajuda você a proteger contra a perda de energia ou rede para um edifício. No Banco de Dados SQL, você coloca as diferentes réplicas em diferentes zonas de disponibilidade (diferentes edifícios, efetivamente).

Na verdade, o contrato de nível de serviço [(SLA)](https://azure.microsoft.com/support/legal/sla/) do Azure, alimentado por uma rede global de data centers gerenciados pela Microsoft, ajuda a manter seu aplicativo funcionando 24 horas por dia, 7 dias por semana. A plataforma Azure gerencia totalmente todos os bancos de dados, e não garante nenhuma perda de dados e uma alta porcentagem de disponibilidade de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. A disponibilidade padrão é obtida por uma separação de camadas de computação e armazenamento. A disponibilidade premium é alcançada integrando computação e armazenamento em um único nó para desempenho e, em seguida, implementando tecnologia semelhante a Always On Availability Groups. Para conferir uma discussão completa sobre os recursos de alta disponibilidade do Banco de Dados SQL do Azure, veja [Disponibilidade do Banco de Dados SQL](sql-database-high-availability.md). 

Além disso, o SQL Database fornece recursos integrados de continuidade de [negócios e escalabilidade global.](sql-database-business-continuity.md) Eles incluem:

- [Backups automáticos:](sql-database-automated-backups.md)

  O SQL Database executa automaticamente backups completos, diferenciais e de registro de transações de bancos de dados SQL para permitir que você restaure a qualquer momento. Para bancos de dados únicos e bancos de dados agrupados, você pode configurar o SQL Database para armazenar backups completos do banco de dados no Azure Storage para retenção de backup a longo prazo. Para instâncias gerenciadas, você também pode executar backups somente cópia para retenção de backup de longo prazo.

- [Restaurações point-in-time:](sql-database-recovery-using-backups.md)

  Todas as opções de implantação do Banco de Dados SQL suportam a recuperação a qualquer momento dentro do período de retenção automática de backup para qualquer banco de dados SQL.
- [Replicação geográfica ativa](sql-database-active-geo-replication.md):

  As opções de banco de dados único e bancos de dados agrupados permitem configurar até quatro bancos de dados secundários legíveis nos mesmos data centers do Azure ou distribuídos globalmente. Por exemplo, se você tiver um aplicativo SaaS com um banco de dados de catálogo que tenha um alto volume de transações simultâneas somente leitura, use a geo-replicação ativa para permitir a escala global de leitura. Isso remove gargalos nas primárias que são devidos à leitura de cargas de trabalho. Para instâncias gerenciadas, use grupos de failover automático.
- [Grupos de failover automático](sql-database-auto-failover-group.md):

  Todas as opções de implantação do Banco de Dados SQL permitem que você use grupos de failover para permitir alta disponibilidade e balanceamento de carga em escala global. Isso inclui georeplicação transparente e failover de grandes conjuntos de bancos de dados, piscinas elásticas e instâncias gerenciadas. Os grupos failover permitem a criação de aplicativos SaaS distribuídos globalmente, com sobrecarga de administração mínima. Isso deixa toda a orquestração complexa de monitoramento, roteamento e failover para o Banco de Dados SQL.
- [Bancos de dados redundantes de zona:](sql-database-high-availability.md)

  O Banco de Dados SQL permite provisionar bancos de dados premium ou comercialmente críticos ou pools elásticos em várias zonas de disponibilidade. Como esses bancos de dados e piscinas elásticas possuem várias réplicas redundantes para alta disponibilidade, colocar essas réplicas em várias zonas de disponibilidade proporciona maior resiliência. Isso inclui a capacidade de recuperar automaticamente das falhas na escala do datacenter, sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência interna

Com o SQL Database, você recebe inteligência incorporada que ajuda a reduzir drasticamente os custos de execução e gerenciamento de bancos de dados, e isso maximiza o desempenho e a segurança do seu aplicativo. Executando milhões de cargas de trabalho de clientes 24 horas por dia, o SQL Database coleta e processa uma enorme quantidade de dados de telemetria, ao mesmo tempo em que respeita totalmente a privacidade do cliente. Vários algoritmos avaliam continuamente os dados de telemetria para que o serviço possa aprender e se adaptar com sua aplicação.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoramento e ajuste de desempenho automáticos

O Banco de Dados SQL fornece informações detalhadas sobre as consultas que você precisa monitorar. O Banco de Dados SQL aprende sobre os padrões do banco de dados e permite que você adapte seu esquema de banco de dados à sua carga de trabalho. O Banco de Dados SQL fornece [recomendações de ajuste de desempenho](sql-database-advisor.md) em que você pode examinar as ações de ajuste e aplicá-las.

No entanto, monitorar constantemente um banco de dados é uma tarefa difícil e tediosa, especialmente quando se lida com muitos bancos de dados. [O Intelligent Insights](sql-database-intelligent-insights.md) faz esse trabalho para você monitorando automaticamente o desempenho do Banco de Dados SQL em escala. Ele informa sobre problemas de degradação de desempenho, identifica a causa raiz de cada problema e fornece recomendações de melhoria de desempenho quando possível.

Gerenciar um grande número de bancos de dados pode ser impossível de fazer de forma eficiente, mesmo com todas as ferramentas e relatórios disponíveis que o SQL Database e o Azure fornecem. Em vez de monitorar e ajustar seu banco de dados manualmente, você pode considerar delegar algumas das ações de monitoramento e ajuste ao Banco de Dados SQL usando [ajuste automático](sql-database-automatic-tuning.md). O SQL Database aplica automaticamente recomendações, testes e verifica cada uma de suas ações de ajuste para garantir que o desempenho continue melhorando. Dessa forma, o Banco de Dados SQL se adapta automaticamente à sua carga de trabalho de forma controlada e segura. Ajuste automático significa que o desempenho do seu banco de dados é cuidadosamente monitorado e comparado antes e depois de cada ação de ajuste. Se o desempenho não melhorar, a ação de ajuste é revertida.

Muitos de nossos parceiros que [executam aplicativos multilocatários SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) em cima do Banco de Dados SQL estão confiando em ajustes automáticos de desempenho para garantir que seus aplicativos tenham sempre desempenho estável e previsível. Para eles, esse recurso reduz o risco de ter um incidente de desempenho no meio da noite. Além disso, como parte de sua base de clientes também usa o SQL Server, eles estão usando as mesmas recomendações de indexação fornecidas pelo SQL Database para ajudar seus clientes do SQL Server.

Dois aspectos de ajuste automático estão [disponíveis no Banco de Dados SQL](sql-database-automatic-tuning.md):

- **Gerenciamento automático de índice**: identifica os índices que devem ser adicionados ao seu banco de dados e os que devem ser removidos.
- **Correção automática do plano**: Identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consulta adaptável

Você pode usar [processamento de consulta adaptativa,](/sql/relational-databases/performance/intelligent-query-processing)incluindo execução intercalada para funções de tabela de várias declarações, feedback de concessão de memória em modo de lote e adesão ao modo de lote adaptativo. Cada um desses recursos de processamento de consulta adaptativa aplica técnicas semelhantes de "aprender e adaptar", ajudando a resolver ainda mais problemas de desempenho relacionados a problemas de otimização de consultas historicamente intratáveis.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O SQL Database fornece uma série de [recursos integrados de segurança e conformidade](sql-database-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Microsoft certificou o Azure SQL Database (todas as opções de implantação) contra uma série de padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), onde você pode encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

### <a name="advance-threat-protection"></a>Proteção avançada contra ameaças

A Segurança de Dados Avançada do SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ele inclui funcionalidade para descobrir e classificar dados confidenciais, gerenciar suas vulnerabilidades de banco de dados e detectar atividades anômalas que possam indicar uma ameaça ao seu banco de dados. Fornece um local único para habilitar e gerenciar essas funcionalidades.

- [Detecção e classificação de dados:](sql-database-data-discovery-and-classification.md)

  Esse recurso fornece recursos incorporados ao Banco de Dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Ele fornece visibilidade ao seu estado de classificação de banco de dados e rastreia o acesso a dados confidenciais dentro do banco de dados e além de suas fronteiras.
- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Esse serviço pode descobrir, controlar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- [Detecção de ameaças](sql-database-threat-detection.md):

  Esse recurso detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas de detecção de ameaças fornecem detalhes da atividade suspeita e recomendam ações sobre como investigar e mitigar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria de segurança e conformidade

A [Auditoria](sql-database-auditing.md) acompanha eventos do banco de dados e os grava em um log de auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Criptografia de dados

O Banco de Dados SQL ajuda a proteger seus dados fornecendo criptografia. Para dados em movimento, ele usa [a segurança da camada de transporte](https://support.microsoft.com/kb/3135244). Para dados em repouso, ele usa [criptografia de dados transparente.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) Para dados em uso, ele usa [sempre criptografado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O Banco de Dados SQL permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory suporta [autenticação multifatorial](sql-database-ssms-mfa-authentication.md) para aumentar a segurança de dados e aplicativos, ao mesmo tempo em que suporta um único processo de login.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de usar

O Banco de Dados SQL torna a compilação e o gerenciamento de aplicativos mais fáceis e produtivos. O Banco de Dados SQL permite a você focar no que há de melhor: compilar aplicativos ótimos. Você pode gerenciar e desenvolver no Banco de Dados SQL usando ferramentas e habilidades que você já tem.

- [O portal Azure:](https://portal.azure.com/)

  Um aplicativo baseado na Web para gerenciar todos os serviços do Azure.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Um aplicativo cliente gratuito e para download para gerenciar qualquer infra-estrutura SQL, do SQL Server ao SQL Database.
- [Ferramentas de dados do servidor SQL no Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Um aplicativo cliente gratuito e para download para o desenvolvimento de bancos de dados relacionais do SQL Server, bancos de dados SQL, pacotes de serviços de integração, modelos de dados dos Serviços de Análise e relatórios de serviços de relatórios.
- [Código visual do estúdio](https://code.visualstudio.com/docs):

  Um editor de código aberto gratuito, para download e código aberto para Windows, macOS e Linux. Ele suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta ao Microsoft SQL Server, Azure SQL Database e Azure SQL Data Warehouse.

O Banco de Dados SQL suporta a construção de aplicativos com Python, Java, Node.js, PHP, Ruby e .NET no macOS, Linux e Windows. O Banco de Dados SQL dá suporte às mesmas [bibliotecas de conexão](sql-database-libraries.md) do SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Banco de dados SQL frequentemente perguntas

### <a name="what-is-the-current-version-of-sql-database"></a>O que é a versão atual do banco de dados SQL?

A versão atual do banco de dados SQL é V12. Versão V11 foi desativado.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando ocorre o tempo de inatividade de correção?

Não. O impacto de patch geralmente não será perceptível, se você [empregar lógica de repetição](sql-database-develop-overview.md#resiliency) no aplicativo. Para obter mais informações, consulte [Planejamento para eventos de manutenção do Azure no Banco de Dados SQL do Azure](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Perguntas do Benefício Híbrido do Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para SQL Server?

Você tem 180 dias de direitos de uso duplo da licença para garantir que as migrações estejam executando perfeitamente. Após esse período de 180 dias, você só pode usar a licença sql server na nuvem no Banco de Dados SQL. Você não tem mais direitos de uso duplo no local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como o Benefício Híbrido do Azure do SQL Server difere da mobilidade de licenças?

Oferecemos benefícios de mobilidade de licença para clientes SQL Server com Garantia de Software. Isso permite a redesignação de suas licenças para os servidores compartilhados de um parceiro. Você pode usar este benefício no Azure IaaS e AWS EC2.

O Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes do SQL Server Enterprise Edition podem obter quatro núcleos no Azure no SKU de Propósito Geral para cada núcleo que possuem no local para aplicativos altamente virtualizados. A mobilidade da licença não permite nenhum custo especial para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece um destino PaaS no Azure (instância gerenciada do Banco de Dados SQL) altamente compatível com o SQL Server no local.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido do Azure para SQL Server?

Os clientes do Banco de Dados SQL têm os seguintes direitos associados ao Azure Hybrid Benefit para o SQL Server:

|Pegada de licença|O que o Azure Hybrid Benefit para o SQL Server lhe dá?|
|---|---|
|Clientes principais do SQL Server Enterprise Edition com SA|<li>Pode pagar taxa básica em Propósito Geral ou SKU Crítico de Negócios</li><br><li>1 núcleo local = 4 núcleos na SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo em SKU para Comercialmente Crítico</li>|
|Clientes principais do SQL Server Standard Edition com SA|<li>Pode pagar taxa básica apenas no SKU de Propósito Geral</li><br><li>1 núcleo local = 1 núcleo na SKU de Uso Geral</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver-se com a equipe de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Faça perguntas sobre administração de banco de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Faça perguntas sobre desenvolvimento.
- [Fóruns MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Faça perguntas técnicas.
- [Feedback](https://aka.ms/sqlfeedback): Reporte bugs e solicite recurso.
- [Reddit](https://www.reddit.com/r/SQLServer/): Discuta o sql server.

## <a name="next-steps"></a>Próximas etapas

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para comparações de custos e calculadoras relativas a bancos de dados únicos e piscinas elásticas.
- Veja essas partidas rápidas para começar:

  - [Criar um banco de dados SQL no portal do Azure](sql-database-single-database-get-started.md)  
  - [Criar um banco de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar um banco de dados SQL usando o PowerShell](sql-database-get-started-powershell.md)

- Para ver vários exemplos da CLI do Azure e do PowerShell, consulte:
  - [Exemplos da CLI do Azure para o Banco de Dados SQL do Azure](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para o Banco de Dados SQL do Azure](sql-database-powershell-samples.md)

- Para obter informações sobre novos recursos à medida que eles são anunciados, consulte [Azure Roadmap for SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database), onde os membros da equipe de produtos do SQL Server blogam sobre notícias e recursos do Banco de Dados SQL.

