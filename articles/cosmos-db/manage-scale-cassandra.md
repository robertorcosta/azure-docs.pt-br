---
title: Escale de forma elástica com API do Cassandra em Azure Cosmos DB
description: Saiba mais sobre as opções disponíveis para dimensionar uma conta de API do Cassandra de Azure Cosmos DB e suas vantagens/desvantagens
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210782"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Dimensionar de forma elástica uma conta de API do Cassandra de Azure Cosmos DB

Há uma variedade de opções para explorar a natureza elástica da API do Azure Cosmos DB para Cassandra. Para entender como dimensionar efetivamente em Azure Cosmos DB, é importante entender como provisionar a quantidade certa de unidades de solicitação (RU/s) para considerar as demandas de desempenho em seu sistema. Para saber mais sobre unidades de solicitação, consulte o artigo [unidades de solicitação](request-units.md) . 

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Limitação da taxa de manipulação (erros 429)

Azure Cosmos DB retornará erros de taxa limitada (429) se os clientes consumirem mais recursos (RU/s) do que o valor que você provisionou. A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra. 

Se o sistema não for sensível à latência, poderá ser suficiente para lidar com a limitação da taxa de transferência usando repetições. Consulte o [exemplo de código Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) para saber como manipular a limitação de taxa de forma transparente usando a [extensão Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) para a [política de repetição Cassandra](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) em Java. Você também pode usar a [extensão do Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) para lidar com a limitação de taxa.

## <a name="manage-scaling"></a>Gerenciar o dimensionamento

Se você precisar minimizar a latência, há um espectro de opções para gerenciar a escala e a taxa de transferência de provisionamento (RUs) no API do Cassandra:

* [Manualmente usando o portal do Azure](#use-azure-portal)
* [Programaticamente, usando os recursos do plano de controle](#use-control-plane)
* [Programaticamente, usando comandos CQL com um SDK específico](#use-cql-queries)
* [Dinamicamente usando o AutoPilot](#use-autopilot)

As seções a seguir explicam as vantagens e desvantagens de cada abordagem. Em seguida, você pode decidir sobre a melhor estratégia para balancear as necessidades de dimensionamento do seu sistema, o custo geral e as necessidades de eficiência para sua solução.

## <a id="use-azure-portal"></a>Usar o portal do Azure

Você pode dimensionar os recursos na conta Azure Cosmos DB API do Cassandra usando portal do Azure. Para saber mais, confira o artigo sobre a [taxa de transferência de provisionamento em contêineres e bancos de dados](set-throughput.md). Este artigo explica os benefícios relativos de definir a taxa de transferência no nível de [banco de dados](set-throughput.md#set-throughput-on-a-database) ou [contêiner](set-throughput.md#set-throughput-on-a-container) no portal do Azure. Os termos "banco de dados" e "contêiner" mencionados nesses artigos são mapeados para "keyspace" e "Table", respectivamente para o API do Cassandra.

A vantagem desse método é que é uma maneira simples e completa de gerenciar a capacidade de taxa de transferência no banco de dados. No entanto, a desvantagem é que, em muitos casos, sua abordagem de dimensionamento pode exigir que determinados níveis de automação sejam econômicos e de alto desempenho. As próximas seções explicam os cenários e os métodos relevantes.

## <a id="use-control-plane"></a>Usar o plano de controle

A API do Azure Cosmos DB para Cassandra fornece a capacidade de ajustar a taxa de transferência programaticamente usando nossos vários recursos de plano de controle. Consulte os artigos [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [PowerShell](powershell-samples-cassandra.md)e [CLI do Azure](cli-samples-cassandra.md) para obter orientações e exemplos.

A vantagem desse método é que você pode automatizar a expansão ou redução dos recursos com base em um temporizador para considerar a atividade de pico ou períodos de baixa atividade. Dê uma olhada em nosso exemplo [aqui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) para saber como fazer isso usando o Azure Functions e o PowerShell.

Uma desvantagem dessa abordagem pode ser que você não pode responder a necessidades de escala dinâmicas imprevisíveis em tempo real. Em vez disso, talvez seja necessário aproveitar o contexto do aplicativo em seu sistema, no nível do cliente/SDK ou usando o [piloto automático](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>Usar consultas CQL com um SDK específico

Você pode dimensionar o sistema dinamicamente com o código executando os [comandos CQL ALTER](cassandra-support.md#keyspace-and-table-options) para o banco de dados ou o contêiner fornecido.

A vantagem dessa abordagem é que ela permite que você responda às necessidades de dimensionamento dinamicamente e de uma maneira personalizada adequada ao seu aplicativo. Com essa abordagem, você ainda pode aproveitar os encargos e as tarifas de RU/s padrão. Se as necessidades de dimensionamento do seu sistema forem principalmente previsíveis (cerca de 70% ou mais), usar o SDK com CQL pode ser um método mais econômico de dimensionamento automático do que usar o AutoPilot. A desvantagem dessa abordagem é que ela pode ser bastante complexa para implementar repetições enquanto a limitação de taxa pode aumentar a latência.

## <a id="use-autopilot"></a>Usar o AutoPilot

Além da maneira manual ou programática de provisionar a taxa de transferência, você também pode configurar contêineres de Cosmos do Azure no modo de piloto automático. O modo AutoPilot será dimensionado de forma automática e instantânea para suas necessidades de consumo dentro dos intervalos de RU especificados sem comprometer os SLAs. Para saber mais, confira o artigo [criar contêineres e bancos de dados do Azure Cosmos no modo AutoPilot](provision-throughput-autopilot.md) .

A vantagem dessa abordagem é que é a maneira mais fácil de gerenciar as necessidades de dimensionamento em seu sistema. Ele garante a não aplicação da limitação de taxa **dentro dos intervalos de ru configurados**. A desvantagem é que, se as necessidades de dimensionamento no seu sistema forem previsíveis, o piloto automático poderá ser uma maneira menos econômica de lidar com suas necessidades de dimensionamento do que usar as abordagens do plano de controle do bespoke ou do nível do SDK mencionadas acima.

## <a name="next-steps"></a>Próximas etapas

- Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) pelo uso de um aplicativo Java
