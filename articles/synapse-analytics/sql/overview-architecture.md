---
title: Arquitetura do SQL Synapse do Azure
description: Saiba como o Azure Synapse SQL combina o processamento paralelo maciço (MPP) com o armazenamento do Azure para atingir alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431807"
---
# <a name="azure-synapse-sql-architecture"></a>Arquitetura do SQL Synapse do Azure 

Este artigo descreve os componentes de arquitetura do SQL Synapse.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Componentes da arquitetura do SQL Synapse

O Synapse SQL aproveita uma arquitetura de expansão para distribuir o processamento computacional de dados em vários nós. A computação é separada do armazenamento, o que permite que você dimensione a computação independentemente dos dados em seu sistema. 

Para o pool do SQL, a unidade de escala é uma abstração da potência de computação que é conhecida como uma [unidade de data warehouse](resource-consumption-models.md). 

Para o SQL sob demanda, sem servidor, o dimensionamento é feito automaticamente para acomodar os requisitos de recursos de consulta. À medida que a topologia é alterada ao longo do tempo adicionando, removendo nós ou failovers, ele se adapta a alterações e garante que sua consulta tenha recursos suficientes e seja concluída com êxito. Por exemplo, a imagem abaixo mostra o SQL sob demanda utilizando 4 nós de computação para executar uma consulta.

![Arquitetura SQL do Synapse](./media//overview-architecture/sql-architecture.png)

O Synapse SQL usa uma arquitetura baseada em nó. Os aplicativos conectam e emitem comandos T-SQL para um nó de controle, que é o único ponto de entrada para Synapse SQL. 

O nó de controle do pool do SQL utiliza o mecanismo MPP para otimizar consultas para processamento paralelo e, em seguida, passa as operações para nós de computação para fazer seu trabalho em paralelo. 

O nó de controle do SQL sob demanda utiliza o mecanismo DQP (processamento de consultas distribuídas) para otimizar e orquestrar a execução distribuída da consulta do usuário, dividindo-a em consultas menores que serão executadas em nós de computação. Cada consulta pequena é chamada Task e representa a unidade de execução distribuída. Ele lê arquivo (s) do armazenamento, une resultados de outras tarefas, grupos ou dados de pedidos recuperados de outras tarefas. 

Os nós de Computação armazenam todos os dados de usuário no Armazenamento do Azure e executam as consultas paralelas. O serviço de movimentação de dados (DMS) é um serviço de nível de sistema interno que move dados entre os nós, conforme necessário para executar consultas em paralelo e retornar resultados precisos. 

Com o armazenamento e a computação separados, ao usar o Synapse SQL, é possível se beneficiar de um dimensionamento independente de potência de computação, independentemente das suas necessidades de armazenamento. Para o dimensionamento sob demanda do SQL é feito automaticamente, enquanto para o pool do SQL, um pode:

* Aumente ou reduza a potência de computação em um pool do SQL (data warehouse), sem mover os dados.
* Pause a capacidade de computação ao deixar dados intactos para que você só pague pelo armazenamento.
* Retomar a capacidade de computação durante horas operacionais.

## <a name="azure-storage"></a>Armazenamento do Azure

O Synapse SQL aproveita o armazenamento do Azure para manter os dados do usuário seguros. Como os dados são armazenados e gerenciados pelo armazenamento do Azure, há um encargo separado para o consumo de armazenamento. 

O SQL sob demanda permite consultar arquivos em seu data Lake em modo somente leitura, enquanto o pool do SQL também permite que você ingerir dados. Quando os dados são ingeridos no pool do SQL, os dados são fragmentados em **distribuições** para otimizar o desempenho do sistema. Você pode escolher qual padrão de fragmentação usar para distribuir os dados quando você define a tabela. Esses padrões de fragmentação têm suporte:

* Hash
* Round Robin
* Replicar

## <a name="control-node"></a>Nó de controle

O nó de controle é o cérebro da arquitetura. É o front-end que interage com todos os aplicativos e conexões. 

No pool do SQL, o mecanismo MPP é executado no nó de controle para otimizar e coordenar consultas paralelas. Quando você envia uma consulta T-SQL para o pool do SQL, o nó de controle a transforma em consultas executadas em cada distribuição em paralelo.

No SQL sob demanda, o mecanismo do DQP é executado no nó de controle para otimizar e coordenar a execução distribuída da consulta do usuário, dividindo-a em consultas menores que serão executadas em nós de computação. Ele também atribui conjuntos de arquivos a serem processados por cada nó.

## <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem capacidade de computação. 

No pool SQL, as distribuições são mapeadas para nós de computação para processamento. À medida que você paga mais recursos de computação, o pool remapeia as distribuições para os nós de computação disponíveis. O número de nós de computação varia de 1 a 60 e é determinado pelo nível de serviço para o pool SQL. Cada nó de computação tem uma ID de nó que está visível nas exibições do sistema. Você pode ver a ID do nó de Computação olhando para a coluna node_id nas exibições do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista das exibições de sistema, consulte [Exibição do sistema MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

No SQL sob demanda, cada nó de computação recebe a tarefa e o conjunto de arquivos para executar a tarefa. A tarefa é uma unidade de execução de consulta distribuída que, na verdade, faz parte do usuário de consulta enviado. O dimensionamento automático está em vigor para garantir que nós de computação suficientes sejam utilizados para executar a consulta do usuário.

## <a name="data-movement-service"></a>Serviço de movimentação de dados

O serviço de movimentação de dados (DMS) é a tecnologia de transporte de dados no pool SQL que coordena a movimentação de dados entre os nós de computação. Algumas consultas exigem a movimentação de dados para garantir que as consultas paralelas retornem resultados precisos. Quando a movimentação de dados é necessária, DMS garante que os dados corretos cheguem ao local correto.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que são executadas em dados distribuídos no pool do SQL. Quando o pool do SQL executa uma consulta, o trabalho é dividido em 60 consultas menores que são executadas em paralelo. 

Cada uma das 60 consultas menores é executada em uma das distribuições de dados. Cada nó de computação gerencia um ou mais de 60 distribuições. Um pool SQL com recursos de computação máximo tem uma distribuição por nó de computação. Um pool SQL com recursos de computação mínimos tem todas as distribuições em um nó de computação. 

## <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash
Uma tabela de hash distribuída pode fornecer o melhor desempenho de consulta para junções e agregações em tabelas grandes. 

Para fragmentar dados em uma tabela distribuída por hash, o pool do SQL usa uma função de hash para atribuir de forma determinística cada linha a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama a seguir ilustra como uma (tabela não distribuída) completa é armazenada como uma tabela distribuída em hash. 

![Tabela distribuída](media//overview-architecture/hash-distributed-table.png "Tabela distribuída") 

* Cada linha pertence a uma distribuição. 
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição. 
* O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e tipos de consultas executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin

Uma tabela de round-robin é a tabela mais simples para criar e oferece um desempenho rápido quando usada como uma tabela de preparo para cargas.

Uma tabela distribuída round-robin distribui dados uniformemente entre a tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida primeiramente de forma aleatória e, em seguida, buffers de linhas são atribuídos a distribuições em sequência. É rápido carregar dados em uma tabela de round-robin, mas o desempenho da consulta geralmente pode ser melhor com tabelas de hash distribuídas. As junções em tabelas Round Robin exigem dados embaralhando, o que leva mais tempo.

## <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela replicada fornece o melhor desempenho de consulta para tabelas pequenas.

Uma tabela replicada faz cache de uma cópia completa da tabela em cada nó de computação. Consequentemente, replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Tabelas replicadas são melhor usadas com tabelas pequenas. O armazenamento extra é necessário e há uma sobrecarga adicional incorrida ao gravar dados, o que torna as tabelas grandes impraticável. 

O diagrama a seguir mostra uma tabela replicada que é armazenada em cache na primeira distribuição em cada nó de computação. 

![Tabela replicada](media/overview-architecture/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe um pouco sobre Synapse SQL, saiba como [criar rapidamente um pool SQL](../quickstart-create-sql-pool.md) e [carregar dados de exemplo](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-data-warehouse-Load-Sample-Databases.MD). Ou você começa [a usar o SQL sob demanda](../quickstart-sql-on-demand.md). Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure](../../azure-glossary-cloud-terminology.md) útil à medida que encontrar nova terminologia. 
