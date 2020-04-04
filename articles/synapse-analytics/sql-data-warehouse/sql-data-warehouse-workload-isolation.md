---
title: Isolamento de carga de trabalho
description: Orientação para definir o isolamento da carga de trabalho com grupos de carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c3fcbf69e7dae14ccd2114a14c685b0443f70fef
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632434"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Isolamento do grupo de carga de trabalho do Azure Synapse Analytics (Visualização)

Este artigo explica como grupos de carga de trabalho podem ser usados para configurar o isolamento da carga de trabalho, conter recursos e aplicar regras de tempo de execução de consultas.

## <a name="workload-groups"></a>Grupos de carga de trabalho

Os grupos de carga de trabalho são contêineres para um conjunto de solicitações e são a base para como o gerenciamento da carga de trabalho, incluindo o isolamento da carga de trabalho, é configurado em um sistema.  Grupos de carga de trabalho são criados usando a sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Uma configuração simples de gerenciamento de carga de trabalho pode gerenciar cargas de dados e consultas de usuário.  Por exemplo, um `wgDataLoads` grupo de carga de trabalho chamado definirá aspectos de carga de trabalho para os dados que estão sendo carregados no sistema. Além disso, um `wgUserQueries` grupo de carga de trabalho chamado definirá aspectos de carga de trabalho para os usuários que executam consultas para ler dados do sistema.

As seções a seguir destacarão como os grupos de carga de trabalho fornecem a capacidade de definir isolamento, contenção, solicitar definição de recursos e aderir às regras de execução.

## <a name="workload-isolation"></a>Isolamento de carga de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de carga de trabalho.  O isolamento da carga de trabalho é alcançado configurando o parâmetro MIN_PERCENTAGE_RESOURCE para maior que zero na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Para cargas de trabalho de execução contínua suscetida a SLAs apertados, o isolamento garante que os recursos estejam sempre disponíveis para o grupo de carga de trabalho.

Configurar o isolamento da carga de trabalho define implicitamente um nível garantido de concorrência. Por exemplo, um grupo `MIN_PERCENTAGE_RESOURCE` de carga de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` trabalho com um conjunto de 30% e definido como 2% é garantido 15 simultâneo.  O nível de supor é garantido porque 15-2% dos slots de recursos `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` são reservados dentro do grupo de carga de trabalho em todos os momentos (independentemente de como está configurado).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` for `REQUEST_MIN_RESOURCE_GRANT_PERCENT` maior `CAP_PERCENTAGE_RESOURCE` do `MIN_PERCENTAGE_RESOURCE` que e for maior do que recursos adicionais são adicionados por solicitação.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` e são `CAP_PERCENTAGE_RESOURCE` iguais `MIN_PERCENTAGE_RESOURCE`e é maior do que , simultâneo adicional é possível.  Considere o método abaixo para determinar a concorrência garantida:

[Concorrência Garantida] =`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Existem valores mínimos de nível de serviço específicos para min_percentage_resource.  Para obter mais informações, consulte [Valores Efetivos](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) para obter mais detalhes.

Na ausência de isolamento da carga de trabalho, as solicitações operam no [pool compartilhado](#shared-pool-resources) de recursos.  O acesso aos recursos no pool compartilhado não é garantido e é atribuído de [forma importante.](sql-data-warehouse-workload-importance.md)

A configuração do isolamento da carga de trabalho deve ser feita com cautela, pois os recursos são alocados no grupo de carga de trabalho, mesmo que não haja solicitações ativas no grupo de carga de trabalho. O isolamento de configuração excessiva pode levar à diminuição da utilização geral do sistema.

Os usuários devem evitar uma solução de gerenciamento de carga de trabalho que configure 100% de isolamento da carga de trabalho: 100% de isolamento é alcançado quando a soma de min_percentage_resource configurada em todos os grupos de carga de trabalho é igual a 100%.  Esse tipo de configuração é excessivamente restritiva e rígida, deixando pouco espaço para solicitações de recursos que são acidentalmente classificadas incorretamente. Há uma disposição para permitir que uma solicitação seja executada a partir de grupos de carga de trabalho não configurados para isolamento. Os recursos alocados para esta solicitação aparecerão como zero nos sistemas de DMVs e tomarão emprestado um pequeno nível de subvenção de recursos do sistema reservado.

> [!NOTE]
> Para garantir a melhor utilização dos recursos, considere uma solução de gerenciamento de carga de trabalho que aproveite algum isolamento para garantir que os SLAs sejam atendidos e misturados com recursos compartilhados que são acessados com base na importância da [carga de trabalho.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Contenção de carga de trabalho

A contenção da carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção da carga de trabalho é obtida configurando o parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Considere o cenário pelo qual os usuários precisam ler o acesso ao sistema para que possam executar uma análise e-se através de consultas ad-hoc.  Esses tipos de solicitações podem ter um impacto negativo em outras cargas de trabalho que estão sendo executados no sistema.  A configuração da contenção garante que a quantidade de recursos seja limitada.

A configuração da contenção da carga de trabalho define implicitamente um nível máximo de concorrência.  Com um CAP_PERCENTAGE_RESOURCE definido para 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido para 1%, até um nível de 60 simultâneos é permitido para o grupo de carga de trabalho.  Considere o método incluído abaixo para determinar a concorrência máxima:

[Max Concurrency]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> A CAP_PERCENTAGE_RESOURCE efetiva de um grupo de carga de trabalho não atingirá 100% quando grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE em um nível superior a zero forem criados.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para obter valores efetivos de tempo de execução.

## <a name="resources-per-request-definition"></a>Recursos por definição de solicitação

Os grupos de carga de trabalho fornecem um mecanismo para definir a quantidade mínima e máxima de recursos que são alocados por solicitação com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Os recursos neste caso são CPU e memória.  A configuração desses valores dita quantos recursos e que nível de concorrência podem ser alcançados no sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que é padrão para o mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como escolher uma classe de recursos, configurar REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor dos recursos utilizados por uma solicitação.  A quantidade de recursos indicados pelo valor definido é garantida para alocação à solicitação antes de iniciar a execução.  Para clientes que migram de classes de recursos para grupos de carga de trabalho, considere seguir o artigo [Como fazer](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) para mapear de classes de recursos para grupos de carga de trabalho como ponto de partida.

Configurar REQUEST_MAX_RESOURCE_GRANT_PERCENT para um valor maior do que REQUEST_MIN_RESOURCE_GRANT_PERCENT permite que o sistema aloque mais recursos por solicitação.  Ao agendar uma solicitação, o sistema determina a alocação real de recursos para a solicitação, que é entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade de recursos no pool compartilhado e na carga atual no sistema.  Os recursos devem existir no [pool compartilhado](#shared-pool-resources) de recursos quando a consulta for agendada.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT possuem valores efetivos que dependem dos valores efetivos MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para obter valores efetivos de tempo de execução.

## <a name="execution-rules"></a>Regras de Execução

Em sistemas de relatórios ad-hoc, os clientes podem acidentalmente executar consultas desgovernadas que afetam severamente a produtividade de outros.  Os administradores do sistema são forçados a gastar tempo matando consultas fugitivas para liberar recursos do sistema.  Grupos de carga de trabalho oferecem a capacidade de configurar uma regra de tempo de execução de consulta para cancelar consultas que excederam o valor especificado.  A regra é configurada `QUERY_EXECUTION_TIMEOUT_SEC` definindo o parâmetro na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Recursos compartilhados de pool

Os recursos de pool compartilhados são os recursos não configurados para isolamento.  Grupos de carga de trabalho com um MIN_PERCENTAGE_RESOURCE definido para zerar recursos de alavancagem no pool compartilhado para executar solicitações.  Grupos de carga de trabalho com um CAP_PERCENTAGE_RESOURCE maior do que MIN_PERCENTAGE_RESOURCE também usaram recursos compartilhados.  A quantidade de recursos disponíveis no pool compartilhado é calculada da seguinte forma.

[Pool compartilhado] = 100 - `MIN_PERCENTAGE_RESOURCE` [soma de todos os grupos de carga de trabalho]

O acesso aos recursos no pool compartilhado é alocado de [forma importante.](sql-data-warehouse-workload-importance.md)  Solicitações com o mesmo nível de importância acessarão recursos de pool compartilhados em uma primeira base de entrada/primeira saída.

## <a name="next-steps"></a>Próximas etapas

- [Quickstart: configure o isolamento da carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Converta classes de recursos em grupos de carga de trabalho](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitoramento do Portal de Gerenciamento de Cargas de Trabalho](sql-data-warehouse-workload-management-portal-monitor.md).  
