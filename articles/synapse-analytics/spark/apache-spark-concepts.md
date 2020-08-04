---
title: Conceitos principais do Apache Spark
description: Este artigo fornece uma introdução ao Apache Spark no Azure Synapse Analytics e os diferentes conceitos.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 806f4dff49e9650dba073721109e7d54a18ecbbe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052341"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Conceitos principais do Apache Spark no Azure Synapse Analytics

O Apache Spark é uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análise de Big Data. O Apache Spark no Azure Synapse Analytics é uma das implementações da Microsoft do Apache Spark na nuvem. 

O Azure Synapse facilita a criação e a configuração de recursos do Spark no Azure. O Azure Synapse fornece uma implementação diferente dos recursos do Spark que estão documentados aqui.

## <a name="spark-pools-preview"></a>Pools do Spark (versão prévia)

Um Pool do Spark (versão prévia) é criado no portal do Azure. É a definição de um Pool do Spark que, quando instanciado, é usado para criar uma instância do Spark que processa dados. Quando um Pool do Spark é criado, ele existe somente como metadados; nenhum recurso é consumido, executado ou cobrado. Um Pool do Spark tem uma série de propriedades que controla as características de uma instância do Spark; essas características incluem, mas não se limitam a: nome, tamanho, comportamento de dimensionamento, vida útil etc.

Como não há nenhum custo monetário ou de recurso associado à criação de Pools do Spark, qualquer número pode ser criado com qualquer quantidade de configurações diferentes. Permissões também podem ser aplicadas a Pools do Spark, permitindo que os usuários tenham acesso apenas a alguns pools específicos.

A melhor prática é criar Pools do Spark menores que possam ser usados para desenvolvimento e depuração e, em seguida, criar maiores para executar cargas de trabalho de produção.

Você pode ler como criar um Pool do Spark e ver todas as suas propriedades em [Introdução aos Pools do Spark no Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Instâncias do Spark

As instâncias do Spark são criadas quando você se conecta a um Pool do Spark, cria uma sessão e executa um trabalho. Como vários usuários podem ter acesso a um Pool do Spark, uma instância do Spark é criada para cada usuário que se conecta. 

Quando você envia um segundo trabalho, se houver capacidade no pool, a instância existente do Spark também terá capacidade e ela processará o trabalho; caso contrário e se houver capacidade no nível do pool, uma instância do Spark será criada.

## <a name="examples"></a>Exemplos

### <a name="example-1"></a>Exemplo 1

- Você cria um Pool do Spark chamado SP1; ele tem um tamanho de cluster fixo de 20 nós.
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Então você envia outro trabalho, J2, que usa 10 nós porque ainda há capacidade no pool e na instância; o J2, é processado pela SI1.
- Se o J2 solicitasse 11 nós, não haveria capacidade no SP1 nem na SI1. Nesse caso, se o J2 vier de um notebook, o trabalho será rejeitado; se o J2 vier de um trabalho em lotes, ele será enfileirado.

### <a name="example-2"></a>Exemplo 2

- Você cria uma chamada ao Pool do Spark SP2; ele tem um dimensionamento automático habilitado de 10 a 20 nós
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Agora você envia outro trabalho, J2, que usa 10 nós, porque ainda há capacidade no pool que a instância aumenta automaticamente para 20 nós e processa o J2.

### <a name="example-3"></a>Exemplo 3

- Você cria um Pool do Spark chamado SP1; ele tem um tamanho de cluster fixo de 20 nós.
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Outro usuário, U2, envia um trabalho, J3, que usa 10 nós; uma instância do Spark, SI2, é criada para processar o trabalho.
- Então você envia outro trabalho, J2, que usa 10 nós porque ainda há capacidade no pool e a instância; o J2, é processado pela SI1.

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação do Apache Spark](https://spark.apache.org/docs/2.4.4/)
