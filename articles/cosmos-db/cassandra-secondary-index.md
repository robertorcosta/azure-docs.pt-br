---
title: Indexação na conta API Azure Cosmos DB Cassandra
description: Saiba como funciona a indexação secundária na conta Azure Azure Azure Cosmos DB Cassandra API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758021"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indexação secundária no Azure Cosmos DB Cassandra API

A API Cassandra no Azure Cosmos DB aproveita a infra-estrutura de indexação subjacente para expor a força de indexação que é inerente à plataforma. No entanto, ao contrário da API SQL principal, a API Cassandra no Azure Cosmos DB não indexa todos os atributos por padrão. Em vez disso, ele suporta indexação secundária para criar um índice em certos atributos, que se comporta da mesma maneira que Apache Cassandra.  

Em geral, não é aconselhável executar consultas de filtro nas colunas que não estão particionadas. Você deve usar a sintaxe ALLOW FILTERING explicitamente, o que resulta em uma operação que pode não funcionar bem. No Azure Cosmos DB você pode executar tais consultas em atributos de baixa cardinalidade porque eles se espalharem por partições para recuperar os resultados.

Não é aconselhável criar um índice em uma coluna atualizada com freqüência. É prudente criar um índice quando você define a tabela. Isso garante que os dados e índices estejam em um estado consistente. No caso de você criar um novo índice sobre os dados existentes, atualmente, você não pode acompanhar a alteração do progresso do índice para a tabela. Se você precisar acompanhar o progresso desta operação, você tem que solicitar a mudança de progresso através de um [bilhete de suporte]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> O índice secundário não é suportado nos seguintes objetos:
> - tipos de dados, como tipos de coleta congelada, decimais e tipos de variantes.
> - Colunas estáticas
> - Chaves de agrupamento

## <a name="indexing-example"></a>Exemplo de indexação

Primeiro, crie um espaço de chave de exemplo e uma tabela executando os seguintes comandos no prompt de shell CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Em seguida, insira os dados do usuário da amostra com os seguintes comandos:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Se você tentar executar a seguinte declaração, você encontrará `ALLOW FILTERING`um erro que lhe pede para usar: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Embora a API Cassandra apoie a filtragem permitida, como mencionado na seção anterior, não é recomendada. Em vez disso, você deve criar um índice no que for mostrado no exemplo a seguir:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Depois de criar um índice no campo "sobrenome", agora você pode executar a consulta anterior com sucesso. Com Cassandra API em Azure Cosmos DB, você não precisa fornecer um nome de índice. Um índice padrão `tablename_columnname_idx` com formato é usado. Por exemplo, ` t1_lastname_idx` é o nome do índice da tabela anterior.

## <a name="dropping-the-index"></a>Queda do índice 
Você precisa saber qual é o nome do índice para baixar o índice. Execute `desc schema` o comando para obter a descrição de sua tabela. A saída deste comando inclui o nome `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`do índice no formato . Em seguida, você pode usar o nome do índice para baixar o índice como mostrado no exemplo a seguir:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como funciona a [indexação automática](index-overview.md) no Azure Cosmos DB
* [Apache Cassandra apresenta suporte da Azure Cosmos DB Cassandra API](cassandra-support.md)
