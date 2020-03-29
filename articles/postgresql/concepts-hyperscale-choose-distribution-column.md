---
title: Escolha colunas de distribuição - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Saiba como escolher colunas de distribuição em cenários comuns de hiperescala no Banco de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975662"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Escolha colunas de distribuição no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

Escolher a coluna de distribuição de cada tabela é uma das decisões de modelagem mais importantes que você tomará. O Banco de Dados Azure para PostgreSQL – Hyperscale (Citus) armazena linhas em fragmentos com base no valor da coluna de distribuição das linhas.

Os grupos de escolha corretas se juntam aos mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte para todos os recursos SQL. Uma escolha incorreta faz com que o sistema seja executado lentamente e não suportará todos os recursos SQL entre nós.

Este artigo dá dicas de coluna de distribuição para os dois cenários mais comuns de Hyperscale (Citus).

### <a name="multi-tenant-apps"></a>Aplicativos multilocatário

A arquitetura multi-inquilino usa uma forma de modelagem hierárquica de banco de dados para distribuir consultas entre os álos no grupo de servidores. O topo da hierarquia de dados é conhecido como *ID* do inquilino e precisa ser armazenado em uma coluna em cada tabela.

Hyperscale (Citus) inspeciona consultas para ver qual iD de inquilino eles envolvem e encontra o fragmento da mesa correspondente. Ele encaminha a consulta para um único nó de trabalhador que contém o fragmento. Executar uma consulta com todos os dados relevantes colocados no mesmo nó é chamado de colocation.

O diagrama a seguir ilustra a colocalização no modelo de dados de vários inquilinos. Contém duas tabelas, Contas e `account_id`Campanhas, cada uma distribuída por . As caixas sombreadas representam fragmentos. Os fragmentos verdes são armazenados juntos em um nó de trabalhador, e os fragmentos azuis são armazenados em outro nó de trabalhador. Observe como uma consulta de adesão entre Contas e Campanhas tem todos os dados\_necessários em conjunto em um nó quando ambas as tabelas estão restritas ao mesmo id da conta.

![Colocalização de vários inquilinos](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar este design em seu próprio esquema, identifique o que constitui um inquilino em sua aplicação. Exemplos comuns incluem empresa, conta, organização ou cliente. O nome da coluna `company_id` `customer_id`será algo como ou . Examine cada uma de suas perguntas e pergunte a si mesmo, funcionaria se tivesse cláusulas adicionais ONDE restringir todas as tabelas envolvidas a linhas com o mesmo ID do inquilino?
As consultas no modelo multi-inquilino são escopo para um inquilino. Por exemplo, as consultas sobre vendas ou inventário são escopo dentro de uma determinada loja.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Tabelas distribuídas de\_partição por uma coluna de id de inquilino comum.** Por exemplo, em um aplicativo SaaS onde\_os inquilinos são empresas, é provável que a id do inquilino seja a id da empresa.\_
-   **Converta pequenas tabelas entre inquilinos em tabelas de referência.** Quando vários inquilinos compartilham uma pequena tabela de informações, distribua-as como uma tabela de referência.
-   **Restringir o filtro de\_todas as consultas de aplicativo por id do inquilino.** Cada consulta deve solicitar informações para um inquilino por vez.

Leia o [tutorial de vários inquilinos](./tutorial-design-database-hyperscale-multi-tenant.md) para um exemplo de como construir esse tipo de aplicativo.

### <a name="real-time-apps"></a>Aplicativos em tempo real

A arquitetura multi-inquilino introduz uma estrutura hierárquica e usa colocation de dados para encaminhar consultas por inquilino. Em contrapartida, as arquiteturas em tempo real dependem de propriedades de distribuição específicas de seus dados para alcançar um processamento altamente paralelo.

Usamos "Entity ID" como termo para colunas de distribuição no modelo em tempo real. Entidades típicas são usuários, hosts ou dispositivos.

Consultas em tempo real geralmente pedem agregados numéricos agrupados por data ou categoria. Hyperscale (Citus) envia essas consultas para cada fragmento para resultados parciais e monta a resposta final no nó do coordenador. As consultas são mais rápidas quando o maior número de nós contribui possível, e quando nenhum nó deve fazer uma quantidade desproporcional de trabalho.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Escolha uma coluna com alta cardinalidade como a coluna de distribuição.** Para comparação, um campo Status em uma tabela de pedidos com valores Novos, Pagos e Enviados é uma má escolha da coluna de distribuição. Ele assume apenas os poucos valores, o que limita o número de fragmentos que podem conter os dados, e o número de nódulos que podem processá-los. Entre colunas com alta cardinalidade, também é bom escolher aquelas colunas que são frequentemente usadas em cláusulas de grupo ou como chaves de adesão.
-   **Escolha uma coluna com distribuição uniforme.** Se você distribuir uma tabela em uma coluna enviesada a certos valores comuns, os dados na tabela tendem a se acumular em certos fragmentos. Os nós que seguram esses fragmentos acabam fazendo mais trabalho do que outros nós.
-   **Distribua tabelas de fatos e dimensões em suas colunas comuns.**
    Sua tabela de fatos pode ter apenas uma chave de distribuição. As tabelas que se juntarem a outra chave não serão anotadas com a tabela de fatos. Escolha uma dimensão para colocar com base na freqüência com que é juntada e o tamanho das linhas de junção.
-   **Altere algumas tabelas de dimensões em tabelas de referência.** Se uma tabela de dimensões não puder ser colocada com a tabela de fatos, você pode melhorar o desempenho da consulta distribuindo cópias da tabela de dimensões para todos os nós na forma de uma tabela de referência.

Leia o [tutorial de painel em tempo real](./tutorial-design-database-hyperscale-realtime.md) para um exemplo de como construir esse tipo de aplicativo.

### <a name="time-series-data"></a>Dados de séries tempois

Em uma carga de trabalho de série temporal, os aplicativos consultam informações recentes enquanto arquivam informações antigas.

O erro mais comum na modelagem de informações de séries tempométricas em Hyperscale (Citus) é usar o próprio carimbo de tempo como uma coluna de distribuição. Uma distribuição de hash baseada no tempo distribui tempos aparentemente aleatoriamente em diferentes fragmentos em vez de manter intervalos de tempo juntos em fragmentos. Consultas que envolvem tempo geralmente referenciam faixas de tempo, por exemplo, os dados mais recentes. Esse tipo de distribuição de hash leva à sobrecarga da rede.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Não escolha um carimbo de data e hora como a coluna de distribuição.** Escolha uma coluna de distribuição diferente. Em um aplicativo multi-inquilino, use o ID do inquilino ou em um aplicativo em tempo real use o ID da entidade.
-   **Use a particionamento da tabela PostgreSQL por tempo.** Use particionamento de tabela para quebrar uma grande tabela de dados ordenados pelo tempo em várias tabelas herdadas com cada tabela contendo diferentes intervalos de tempo. Distribuir uma tabela particionada por Postgres em Hyperscale (Citus) cria fragmentos para as tabelas herdadas.

Leia o [tutorial da série temporal](https://aka.ms/hyperscale-tutorial-timeseries) para um exemplo de como construir esse tipo de aplicativo.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [a colocalização](concepts-hyperscale-colocation.md) entre dados distribuídos ajuda as consultas a ser executadas rapidamente.
