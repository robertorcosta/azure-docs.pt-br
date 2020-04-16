---
title: Arquitetura SqL Azure Synapse
description: Saiba como o Azure Synapse SQL combina processamento massivamente paralelo (MPP) com o Azure Storage para alcançar alto desempenho e escalabilidade.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431807"
---
# <a name="azure-synapse-sql-architecture"></a>Arquitetura SqL Azure Synapse 

Este artigo descreve os componentes de arquitetura do Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Componentes de arquitetura Synapse SQL

O Synapse SQL aproveita uma arquitetura de escala para distribuir o processamento computacional de dados em vários latlos. O cálculo é separado do armazenamento, o que permite que você dimensione a computação independentemente dos dados em seu sistema. 

Para pool SQL, a unidade de escala é uma abstração do poder computacional que é conhecida como [uma unidade de data warehouse](resource-consumption-models.md). 

Para SQL sob demanda, sem servidor, o dimensionamento é feito automaticamente para acomodar os requisitos de recursos de consulta. À medida que a topologia muda ao longo do tempo adicionando, removendo álos ou failovers, ele se adapta às alterações e garante que sua consulta tenha recursos suficientes e termine com sucesso. Por exemplo, a imagem abaixo mostra SQL sob demanda utilizando 4 nós de computação para executar uma consulta.

![Arquitetura Synapse SQL](./media//overview-architecture/sql-architecture.png)

O Synapse SQL usa uma arquitetura baseada em nó. Os aplicativos conectam e emitem comandos T-SQL para um nó controle, que é o único ponto de entrada para Synapse SQL. 

O nó SQL pool Control utiliza o mecanismo MPP para otimizar consultas para processamento paralelo e, em seguida, passa operações para os nós de Computação para fazer seu trabalho em paralelo. 

O nó de controle sob demanda SQL utiliza o mecanismo DQP (Distributed Query Processing, processamento de consulta distribuída) para otimizar e orquestrar a execução distribuída da consulta do usuário, dividindo-a em consultas menores que serão executadas em nós de Computação. Cada pequena consulta é chamada de tarefa e representa unidade de execução distribuída. Ele lê arquivos do armazenamento, junta resultados de outras tarefas, grupos ou pedidos de dados recuperados de outras tarefas. 

Os nós de Computação armazenam todos os dados de usuário no Armazenamento do Azure e executam as consultas paralelas. O serviço de movimentação de dados (DMS) é um serviço de nível de sistema interno que move dados entre os nós, conforme necessário para executar consultas em paralelo e retornar resultados precisos. 

Com armazenamento e computação desacoplados, ao usar o Synapse SQL, pode-se beneficiar do dimensionamento independente do poder computacional, independentemente de suas necessidades de armazenamento. Para o dimensionamento sob demanda SQL é feito automaticamente, enquanto para o pool SQL pode::

* Cresça ou reduza o poder de computação, dentro de um pool SQL (data warehouse), sem mover dados.
* Pause a capacidade de computação ao deixar dados intactos para que você só pague pelo armazenamento.
* Retomar a capacidade de computação durante horas operacionais.

## <a name="azure-storage"></a>Armazenamento do Azure

O Synapse SQL aproveita o Armazenamento Azure para manter os dados do usuário seguros. Uma vez que seus dados são armazenados e gerenciados pelo Azure Storage, há uma taxa separada para o seu consumo de armazenamento. 

O SQL sob demanda permite que você consulte arquivos em seu lago de dados de forma apenas leitura, enquanto o pool SQL permite que você ingeri dados também. Quando os dados são ingeridos no pool SQL, os dados são agrupados em **distribuições** para otimizar o desempenho do sistema. Você pode escolher qual padrão de fragmentação usar para distribuir os dados quando você define a tabela. Estes padrões de fragmentação são suportados:

* Hash
* Round Robin
* Replicar

## <a name="control-node"></a>Nó de controle

O nó controle é o cérebro da arquitetura. É o front-end que interage com todos os aplicativos e conexões. 

No pool SQL, o motor MPP é executado no nó Controle para otimizar e coordenar consultas paralelas. Quando você envia uma consulta T-SQL para o pool SQL, o nó Controle transforma-o em consultas que são executadas contra cada distribuição em paralelo.

No SQL sob demanda, o mecanismo DQP é executado no nó Control para otimizar e coordenar a execução distribuída da consulta do usuário, dividindo-a em consultas menores que serão executadas em nós de Computação. Ele também atribui conjuntos de arquivos a serem processados por cada nó.

## <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem capacidade de computação. 

No pool SQL, as distribuições mapeiam os nós de computação para processamento. À medida que você paga por mais recursos de computação, o pool remapeia as distribuições para os nós de Computação disponíveis. O número de nódulos computacionais varia de 1 a 60, e é determinado pelo nível de serviço para pool SQL. Cada nó de computação tem uma ID de nó que está visível nas exibições do sistema. Você pode ver a ID do nó de Computação olhando para a coluna node_id nas exibições do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista das exibições de sistema, consulte [Exibição do sistema MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

No SQL sob demanda, cada nó de computação é atribuído tarefa e conjunto de arquivos para executar a tarefa. A tarefa é a unidade de execução de consulta distribuída, que na verdade faz parte da consulta enviada pelo usuário. O dimensionamento automático está em vigor para garantir que nós computacionais suficientes sejam utilizados para executar a consulta do usuário.

## <a name="data-movement-service"></a>Serviço de movimentação de dados

O Data Movement Service (DMS) é a tecnologia de transporte de dados no pool SQL que coordena a movimentação de dados entre os nós da Computação. Algumas consultas exigem a movimentação de dados para garantir que as consultas paralelas retornem resultados precisos. Quando a movimentação de dados é necessária, DMS garante que os dados corretos cheguem ao local correto.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que são executadas em dados distribuídos no pool SQL. Quando o pool SQL executa uma consulta, o trabalho é dividido em 60 consultas menores que são executadas em paralelo. 

Cada uma das 60 consultas menores é executada em uma das distribuições de dados. Cada nó de computação gerencia um ou mais de 60 distribuições. Um pool SQL com recursos máximos de computação tem uma distribuição por nó de computação. Um pool SQL com recursos mínimos de computação tem todas as distribuições em um nó de computação. 

## <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash
Uma tabela de hash distribuída pode fornecer o melhor desempenho de consulta para junções e agregações em tabelas grandes. 

Para fragmentos de dados em uma tabela distribuída por hash, o pool SQL usa uma função hash para atribuir determinicamente cada linha a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama a seguir ilustra como uma (tabela não distribuída) completa é armazenada como uma tabela distribuída em hash. 

![Tabela distribuída](media//overview-architecture/hash-distributed-table.png "Tabela distribuída") 

* Cada linha pertence a uma distribuição. 
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição. 
* O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e tipos de consultas executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin

Uma tabela de round-robin é a tabela mais simples para criar e oferece um desempenho rápido quando usada como uma tabela de preparo para cargas.

Uma tabela distribuída round-robin distribui dados uniformemente entre a tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida primeiramente de forma aleatória e, em seguida, buffers de linhas são atribuídos a distribuições em sequência. É rápido carregar dados em uma tabela de round-robin, mas o desempenho da consulta geralmente pode ser melhor com tabelas de hash distribuídas. As adesões em tabelas redondas exigem a reformulação de dados, o que leva mais tempo.

## <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela replicada fornece o melhor desempenho de consulta para tabelas pequenas.

Uma tabela replicada faz cache de uma cópia completa da tabela em cada nó de computação. Consequentemente, replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Tabelas replicadas são melhor usadas com tabelas pequenas. O armazenamento extra é necessário e há sobrecarga adicional que é incorrida ao escrever dados, o que torna as grandes tabelas impraticáveis. 

O diagrama abaixo mostra uma tabela replicada que é armazenada em cache na primeira distribuição em cada nó de computação. 

![Tabela replicada](media/overview-architecture/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe um pouco sobre o Synapse SQL, aprenda a criar rapidamente [um pool SQL](../quickstart-create-sql-pool.md) e [carregar dados de amostra](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Ou você começa a [usar SQL sob demanda](../quickstart-sql-on-demand.md). Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure](../../azure-glossary-cloud-terminology.md) útil à medida que encontrar nova terminologia. 
