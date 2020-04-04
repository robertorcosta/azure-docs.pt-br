---
title: Gerenciamento de carga de trabalho
description: Orientação para implementar o gerenciamento de carga de trabalho no Azure Synapse Analytics.
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
ms.openlocfilehash: a6d46c2adf1d886f804a3a542a208558d7f935b9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632384"
---
# <a name="what-is-workload-management"></a>O que é o gerenciamento de carga de trabalho?

Executar cargas de trabalho mistas pode representar desafios de recursos em sistemas ocupados.  Os arquitetos de soluções buscam maneiras de separar as atividades clássicas de armazenamento de dados (como carregar, transformar e consultar dados) para garantir que existam recursos suficientes para atingir SLAs.  

O isolamento físico do servidor pode levar a bolsões de infra-estrutura que são subutilizados, superlotados ou em um estado onde os caches estão constantemente sendo preparados com o hardware iniciando e parando.  Um esquema de gerenciamento de carga de trabalho bem-sucedido gerencia efetivamente os recursos, garante uma utilização de recursos altamente eficiente e maximiza o retorno sobre o investimento (ROI).

Uma carga de trabalho de data warehouse refere-se a todas as operações que transpidem em relação a um data warehouse. A profundidade e amplitude desses componentes dependem do nível de maturidade do data warehouse.  A carga de trabalho do data warehouse abrange:

- Todo o processo de carregamento de dados para o armazém
- Realizando análisee e relatórios de data warehouse
- Gerenciamento de dados no data warehouse
- Exportação de dados do data warehouse

A capacidade de desempenho de um data warehouse é determinada pelo [unidades do data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Para visualizar os recursos alocados para todos os perfis de desempenho, consulte [Limites de memória e simultâneos](memory-concurrency-limits.md).
- Para ajustar a capacidade, você pode [escalar para cima ou para baixo](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Conceitos de gerenciamento de carga de trabalho

No passado, para o SQL Analytics no Azure Synapse, você gerenciava o desempenho da consulta através de [classes de recursos](resource-classes-for-workload-management.md).  As classes de recursos permitiram atribuir memória a uma consulta com base na adesão à função.  O principal desafio com as classes de recursos é que, uma vez configurado, não havia governança ou capacidade de controlar a carga de trabalho.  

Por exemplo, a concessão de uma função de usuário ad-hoc ao smallrc permitiu que o usuário consumisse 100% da memória no sistema.  Com as classes de recursos, não há como reservar e garantir que os recursos estejam disponíveis para cargas de trabalho críticas.

O gerenciamento da carga de trabalho do pool SQL sinapse no Azure Synapse consiste em três conceitos de alto nível: Classificação da [Carga de Trabalho,](sql-data-warehouse-workload-classification.md) [Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md) e Isolamento da Carga [de Trabalho](sql-data-warehouse-workload-isolation.md).  Esses recursos dão mais controle sobre como sua carga de trabalho utiliza recursos do sistema.

Classificação da carga de trabalho é o conceito de atribuir uma solicitação a um grupo de carga de trabalho e definir níveis de importância.  Historicamente, essa atribuição era feita por meio de adesão à função usando [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Isso agora pode ser feito através do [CREATE WORKLOAD CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  O recurso de classificação fornece um conjunto mais rico de opções, como rótulo, sessão e tempo para classificar solicitações.

A importância da carga de trabalho influencia a ordem em que uma solicitação obtém acesso aos recursos.  Em um sistema movimentado, uma solicitação com maior importância tem primeiro acesso aos recursos.  A importância também pode garantir o acesso ordenado aos bloqueios.

O isolamento da carga de trabalho reserva recursos para um grupo de carga de trabalho.  Os recursos reservados em um grupo de carga de trabalho são mantidos exclusivamente para esse grupo de carga de trabalho para garantir a execução.  Os grupos de carga de trabalho também permitem definir a quantidade de recursos que são atribuídos por solicitação, assim como as classes de recursos fazem.  Grupos de carga de trabalho dão a você a capacidade de reservar ou limitar a quantidade de recursos que um conjunto de solicitações pode consumir.  Finalmente, os grupos de carga de trabalho são um mecanismo para aplicar regras, como o tempo de consulta, às solicitações.  

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a classificação da carga de trabalho, consulte [Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md).  
- Para obter mais informações sobre o isolamento da carga de trabalho, consulte [Isolamento da carga de trabalho](sql-data-warehouse-workload-isolation.md).  
- Para obter mais informações sobre a importância da carga horária, consulte [A importância da carga horária](sql-data-warehouse-workload-importance.md).  
- Para obter mais informações sobre o monitoramento do gerenciamento da carga de trabalho, consulte [Monitoramento do Portal de Gerenciamento de Carga de Trabalho](sql-data-warehouse-workload-management-portal-monitor.md).  
