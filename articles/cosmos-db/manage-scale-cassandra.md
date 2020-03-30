---
title: Escala elástica com Cassandra API em Azure Cosmos DB
description: Conheça as opções disponíveis para dimensionar uma conta API Azure Cosmos DB Cassandra e suas vantagens/desvantagens
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474672"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Escala selaticamente uma conta API Azure Cosmos DB Cassandra

Há uma variedade de opções para explorar a natureza elástica da API Azure Cosmos DB para Cassandra. Para entender como dimensionar efetivamente no Azure Cosmos DB, é importante entender como prover a quantidade certa de unidades de solicitação (RU/s) para explicar as demandas de desempenho em seu sistema. Para saber mais sobre as unidades de solicitação, consulte o artigo das [unidades de solicitação.](request-units.md) 

Para a API Cassandra, você pode recuperar a taxa Unidade de solicitação para consultas individuais usando os [SDKs .NET e Java](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Isso é útil para determinar a quantidade de RU/s que você precisará prover no serviço.

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Limitação da taxa de manuseio (429 erros)

O Azure Cosmos DB retornará os erros de taxa limitada (429) se os clientes consumirem mais recursos (RU/s) do que o valor que você provisionou. A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra. 

Se o sistema não for sensível à latência, pode ser suficiente para lidar com a limitação da taxa de throughput usando repetições. Consulte a [amostra de código Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) para lidar com a limitação da taxa de forma transparente usando a [extensão Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) para a política de tentativa de [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) em Java. Você também pode usar a [extensão Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) para lidar com a limitação de taxa.

## <a name="manage-scaling"></a>Gerenciar o dimensionamento

Se você precisar minimizar a latência, há um espectro de opções para gerenciar escala e provisionamento de throughput (RUs) na API Cassandra:

* [Usando manualmente o portal Azure](#use-azure-portal)
* [Programáticamente usando as características do plano de controle](#use-control-plane)
* [Programáticamente usando comandos CQL com um SDK específico](#use-cql-queries)
* [Dinamicamente usando piloto automático](#use-autopilot)

As seções a seguir explicam as vantagens e desvantagens de cada abordagem. Você pode então decidir sobre a melhor estratégia para equilibrar as necessidades de dimensionamento do seu sistema, o custo global e as necessidades de eficiência para sua solução.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Use o portal Azure

Você pode dimensionar os recursos na conta acs azure Cosmos DB Cassandra API usando o portal Azure. Para saber mais, consulte o artigo sobre [provision throughput em contêineres e bancos de dados](set-throughput.md). Este artigo explica os benefícios relativos da definição de throughput em banco de [dados](set-throughput.md#set-throughput-on-a-database) ou nível [de contêiner](set-throughput.md#set-throughput-on-a-container) no portal Azure. Os termos "banco de dados" e "container" mencionados nestes artigos mapeiam para "keyspace" e "table" respectivamente para a API de Cassandra.

A vantagem deste método é que é uma maneira simples de gerenciar a capacidade de throughput no banco de dados. No entanto, a desvantagem é que, em muitos casos, sua abordagem para o dimensionamento pode exigir que certos níveis de automação sejam econômicos e de alto desempenho. As próximas seções explicam os cenários e métodos relevantes.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Use o plano de controle

A API da Azure Cosmos DB para Cassandra fornece a capacidade de ajustar o throughput programáticamente usando nossos vários recursos de plano de controle. Consulte os artigos [do Azure Resource Manager,](manage-cassandra-with-resource-manager.md) [Powershell](powershell-samples-cassandra.md)e [Azure CLI](cli-samples-cassandra.md) para obter orientações e amostras.

A vantagem deste método é que você pode automatizar o dimensionamento para cima ou para baixo de recursos com base em um temporizador para contabilizar a atividade de pico, ou períodos de baixa atividade. Dê uma olhada em nossa amostra [aqui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) para como realizar isso usando Funções Azure e Powershell.

Uma desvantagem com essa abordagem pode ser que você não pode responder a necessidades de escala em mudanças imprevisíveis em tempo real. Em vez disso, você pode precisar aproveitar o contexto do aplicativo em seu sistema, no nível cliente/SDK ou usando [piloto automático](provision-throughput-autopilot.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Use consultas CQL com um SDK específico

Você pode dimensionar o sistema dinamicamente com código executando os [comandos CQL ALTER](cassandra-support.md#keyspace-and-table-options) para o banco de dados ou contêiner dado.

A vantagem dessa abordagem é que ela permite que você responda às necessidades de escala dinamicamente e de forma personalizada que se adapte à sua aplicação. Com essa abordagem, você ainda pode aproveitar as taxas e taxas padrão de RU/s. Se as necessidades de escala do seu sistema forem previsíveis (cerca de 70% ou mais), usar O SDK com CQL pode ser um método mais econômico de dimensionamento automático do que usar o Piloto Automático. A desvantagem dessa abordagem é que pode ser bastante complexo para implementar repetições, enquanto a limitação da taxa pode aumentar a latência.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Use piloto automático

Além da maneira manual ou programática de provisionamento de throughput, você também pode configurar contêineres Azure cosmos no modo Piloto Automático. O modo piloto automático será dimensionado automaticamente e instantaneamente para suas necessidades de consumo dentro das faixas DE RU especificadas sem comprometer os SLAs. Para saber mais, consulte os [contêineres e bancos de dados Create Azure Cosmos no artigo do modo piloto automático.](provision-throughput-autopilot.md)

A vantagem dessa abordagem é que é a maneira mais fácil de gerenciar as necessidades de dimensionamento em seu sistema. Garante não aplicar limitação de taxa **dentro das faixas de RU configuradas.** A desvantagem é que, se as necessidades de dimensionamento em seu sistema são previsíveis, o Piloto Automático pode ser uma maneira menos econômica de lidar com suas necessidades de escala do que usar o plano de controle medida ou abordagens de nível SDK mencionadas acima.

## <a name="next-steps"></a>Próximas etapas

- Comece com a [criação de uma conta Cassandra API, banco de dados e uma tabela](create-cassandra-api-account-java.md) usando um aplicativo Java
