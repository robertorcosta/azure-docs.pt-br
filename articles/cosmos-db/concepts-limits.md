---
title: Cotas de serviços do Azure Cosmos DB
description: Cotas de serviço do Azure Cosmos DB e limites padrão em diferentes tipos de recursos.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415701"
---
# <a name="azure-cosmos-db-service-quotas"></a>Cotas de serviços do Azure Cosmos DB

Este artigo oferece uma visão geral das cotas padrão oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e throughput

Depois de criar uma conta do Azure Cosmos sob sua assinatura, você pode gerenciar dados em sua conta [criando bancos de dados, contêineres e itens](databases-containers-items.md). Você pode provisionar throughput em um nível de contêiner ou em um nível de banco de dados em termos de unidades de [solicitação (RU/s ou RUs)](request-units.md). A tabela a seguir lista os limites para armazenamento e throughput por contêiner/banco de dados.

| Recurso | Limite padrão |
| --- | --- |
| RUs máximo por contêiner[(modo provisionado de throughput dedicado)](databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por padrão. Você pode aumentá-lo [arquivando um bilhete de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| RUs máximo por banco de dados[(modo provisionado de throughput compartilhado)](databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por padrão. Você pode aumentá-lo [arquivando um bilhete de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| RUs máximo por chave de partição (lógica) | 10.000 |
| Armazenamento máximo em todos os itens por chave de partição (lógica)| 20 GB |
| Número máximo de teclas de partição distintas (lógicas) | Ilimitado |
| Armazenamento máximo por contêiner | Ilimitado |
| Armazenamento máximo por banco de dados | Ilimitado |
| Tamanho máximo de anexo por conta (recurso de anexo está sendo depreciado) | 2 GB |
| RUs mínimo necessário por 1 GB | 10 RU/s |

> [!NOTE]
> Para aprender sobre as melhores práticas para gerenciar cargas de trabalho que possuem chaves de partição que requerem limites mais altos para armazenamento ou throughput, consulte [Criar uma chave de partição sintética](synthetic-partition-keys.md).
>

Um contêiner Cosmos (ou banco de dados de throughput compartilhado) deve ter um rendimento mínimo de 400 RUs. À medida que o contêiner cresce, o rendimento mínimo suportado também depende dos seguintes fatores:

* O rendimento mínimo que você pode definir em um contêiner depende do rendimento máximo já provisionado no recipiente. Por exemplo, se o seu throughput foi aumentado para 10000 RUs, então o menor throughput provisionado possível seria 1000 RUs
* O rendimento mínimo de um banco de dados de throughput compartilhado também depende do número total de contêineres que você já criou em um banco de dados de throughput compartilhado, medido em 100 RUs por contêiner. Por exemplo, se você criou cinco contêineres dentro de um banco de dados de throughput compartilhado, então o throughput deve ser de pelo menos 500 RUs

O rendimento atual e mínimo de um contêiner ou de um banco de dados pode ser recuperado do portal Azure ou dos SDKs. Para obter mais informações, consulte [Provision throughput em contêineres e bancos de dados](set-throughput.md). 

> [!NOTE]
> Em alguns casos, você pode ser capaz de reduzir o throughput para menos de 10%. Use a API para obter o mínimo exato de RUs por contêiner.
>

Em resumo, aqui estão os limites mínimos provisionados da RU. 

| Recurso | Limite padrão |
| --- | --- |
| RUs mínimos por contêiner[(modo provisionado de throughput dedicado)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| RUs mínimos por banco de dados[(modo provisionado de throughput compartilhado)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| RUs mínimos por contêiner dentro de um banco de dados de throughput compartilhado | 100 |

Cosmos DB suporta dimensionamento elástico de throughput (RUs) por contêiner ou banco de dados através dos SDKs ou portal. Cada contêiner pode escalar de forma sincronizada e imediata dentro de uma escala de 10 a 100 vezes, entre valores mínimos e máximos. Se o valor de throughput solicitado estiver fora do intervalo, o dimensionamento será realizado de forma assíncrona. O dimensionamento assíncrono pode levar minutos a horas para ser concluído, dependendo do throughput solicitado e do tamanho do armazenamento de dados no recipiente.  

## <a name="control-plane-operations"></a>Controle das operações do avião

Você pode [provisionar e gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md) usando os modelos do portal Azure, Azure PowerShell, Azure CLI e Azure Resource Manager. A tabela a seguir lista os limites por assinatura, conta e número de operações.

| Recurso | Limite padrão |
| --- | --- |
| Contas máximas de banco de dados por assinatura | 50 por padrão. Você pode aumentá-lo [arquivando um bilhete de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Número máximo de failovers regionais | 1/hora por padrão. Você pode aumentá-lo [arquivando um bilhete de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Os failovers regionais só se aplicam a contas de gravações de uma única região. As contas de gravação de várias regiões não exigem ou têm limites para alterar a região de gravação.

O Cosmos DB faz backups de seus dados automaticamente em intervalos regulares. Para obter detalhes sobre intervalos de retenção de backup e janelas, consulte [Backup on-line e restauração de dados sob demanda no Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites por conta

| Recurso | Limite padrão |
| --- | --- |
| Número máximo de bancos de dados | Ilimitado |
| Número máximo de contêineres por banco de dados com throughput compartilhado |25 |
| Número máximo de contêineres por banco de dados ou conta com throughput dedicado  |ilimitado |
| Número máximo de regiões | Sem limite (todas as regiões do Azure) |

## <a name="per-container-limits"></a>Limites por contêiner

Dependendo de qual API você usa, um contêiner Azure Cosmos pode representar uma coleção, uma tabela ou um gráfico. Os contêineres suportam configurações para [restrições de chave exclusivas, procedimentos](unique-keys.md) [armazenados, gatilhos e UDFs](stored-procedures-triggers-udfs.md)e [política de indexação](how-to-manage-indexing-policy.md). A tabela a seguir lista os limites específicos para configurações dentro de um contêiner. 

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo do banco de dados ou nome do contêiner | 255 |
| Máximo de procedimentos armazenados por contêiner | 100<sup>*</sup>|
| UDFs máximos por contêiner | 25<sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100<sup>*</sup>|
| Número máximo de chaves exclusivas por contêiner|10<sup>*</sup>|
| Número máximo de caminhos por restrição de chave única|16<sup>*</sup>|

<sup>*</sup>Você pode aumentar qualquer um desses limites por contêiner entrando em contato com o Suporte Azure.

## <a name="per-item-limits"></a>Limites por item

Dependendo de qual API você usa, um item do Azure Cosmos pode representar um documento em uma coleção, uma linha em uma tabela ou um nó ou borda em um gráfico. A tabela a seguir mostra os limites por item no Cosmos DB. 

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento UTF-8 da representação JSON) |
| Comprimento máximo do valor da chave de partição | Bytes 2048 |
| Comprimento máximo do valor de id | 1023 bytes |
| Número máximo de propriedades por item | Sem limite prático |
| Profundidade máxima de aninhamento | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade string | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 de dupla precisão 64 bits |

Não há restrições sobre as cargas do item, como número de propriedades e profundidade de aninhamento, exceto pelas restrições de comprimento nos valores de chave de partição e id, e pela restrição de tamanho global de 2 MB. Você pode ter que configurar a política de indexação para contêineres com estruturas de itens grandes ou complexos para reduzir o consumo de RU. Consulte [Itens de modelagem no Cosmos DB](how-to-model-partition-example.md) para um exemplo do mundo real e padrões para gerenciar itens grandes.

## <a name="per-request-limits"></a>Limites por solicitação

O Azure Cosmos DB suporta [operações crud e consulta](https://docs.microsoft.com/rest/api/cosmos-db/) contra recursos como contêineres, itens e bancos de dados. Ele também suporta [solicitações de lote transacional](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) contra vários itens com a mesma chave de partição em um contêiner.

| Recurso | Limite padrão |
| --- | --- |
| Tempo máximo de execução para uma única operação (como uma execução de procedimento armazenado ou uma única recuperação de página de consulta)| 5 segundos |
| Tamanho máximo da solicitação (por exemplo, procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo de resposta (por exemplo, consulta paginada) | 4 MB |
| Número máximo de operações em lote transacional | 100 |

Uma vez que uma operação como consulta atinge o limite de tempo limite de execução ou tamanho de resposta, ela retorna uma página de resultados e um token de continuação para o cliente para retomar a execução. Não há limite prático sobre a duração que uma única consulta pode executar em páginas/continuações.

Cosmos DB usa HMAC para autorização. Você pode usar uma chave mestra ou um [token de recurso para](secure-access-to-data.md) controle de acesso de grãos finos a recursos como contêineres, chaves de partição ou itens. A tabela a seguir lista limites para tokens de autorização no Cosmos DB.

| Recurso | Limite padrão |
| --- | --- |
| Tempo máximo de expiração do token mestre | 15 min  |
| Tempo mínimo de expiração do token de recurso | 10 min  |
| Tempo máximo de expiração do token de recurso | 24 horas por padrão. Você pode aumentá-lo [arquivando um bilhete de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Inclinação máxima do relógio para autorização de token| 15 min |

Cosmos DB suporta a execução de gatilhos durante a escrita. O serviço suporta um máximo de um pré-gatilho e um pós-gatilho por operação de gravação. 

## <a name="autopilot-mode-limits"></a>Limites do modo piloto automático

Consulte o artigo [do Piloto Automático](provision-throughput-autopilot.md#autopilot-limits) para obter os limites de throughput e armazenamento no modo piloto automático.

## <a name="sql-query-limits"></a>Limites de consulta SQL

Cosmos DB suporta itens de consulta usando [SQL](how-to-sql-query.md). A tabela a seguir descreve restrições nas instruções de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo da consulta SQL| 256 KB |
| Máximo de JOINs por consulta| 5<sup>*</sup>|
| UDFs máximos por consulta| 10<sup>*</sup>|
| Pontos máximos por polígono| 4096 |
| Caminhos máximos incluídos por contêiner| 500 |
| Caminhos excluídos máximos por contêiner| 500 |
| Propriedades máximas em um índice composto| 8 |

<sup>*</sup>Você pode aumentar esses limites de consulta SQL entrando em contato com o Suporte azure.

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API do MongoDB

Cosmos DB suporta o protocolo de fio MongoDB para aplicativos escritos contra o MongoDB. Você pode encontrar os comandos e versões de protocolo suportados nos [recursos e sintaxe do MongoDB suportado.](mongodb-feature-support.md)

A tabela a seguir lista os limites específicos do suporte ao recurso MongoDB. Outros limites de serviço mencionados para a API SQL (núcleo) também se aplicam à API Do MongoDB.

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo da memória de consulta mongoDB (essa limitação é apenas para a versão do servidor 3.2) | 40 MB |
| Tempo máximo de execução para operações do MongoDB| 30 s |
| Tempo de tempo de conexão ocioso para o encerramento da conexão lateral do servidor* | 30 minutos |

\*Recomendamos que os aplicativos clientedefinam o tempo de intervalo de conexão ociosa nas configurações do driver para 2-3 minutos porque o [tempo de intervalo padrão para o Azure LoadBalancer é de 4 minutos](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout).  Este tempo out garantirá que as conexões ociosas não sejam fechadas por um balanceador de carga intermediário entre a máquina cliente e o Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Experimente os limites livres do Cosmos DB

A tabela a seguir lista os limites para o [try Azure Cosmos DB para teste gratuito.](https://azure.microsoft.com/try/cosmosdb/)

| Recurso | Limite padrão |
| --- | --- |
| Duração do ensaio | 30 dias (pode ser renovado em qualquer número de vezes) |
| Recipientes máximos por assinatura (SQL, Gremlin, Tabela API) | 1 |
| Recipientes máximos por assinatura (API Do MongoDB) | 3 |
| Throughput máximo por contêiner | 5.000 |
| Throughput máximo por banco de dados de throughput compartilhado | 20000 |
| Armazenamento total máximo por conta | 10 GB |

O Try Cosmos DB suporta a distribuição global apenas nas regiões central dos EUA, Norte da Europa e Sudeste Asiático. Os bilhetes de suporte do Azure não podem ser criados para as contas Try Azure Cosmos DB. No entanto, o suporte é fornecido para assinantes com planos de suporte existentes.

## <a name="free-tier-account-limits"></a>Limites de conta de nível gratuito
A tabela a seguir lista os limites para [contas de nível livre do Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de nível grátis por assinatura do Azure | 1 |
| Duração do desconto de nível livre | Vida inteira da conta. Deve optar por entrar durante a criação da conta. |
| Máximo ru/s grátis | 400 RU/s |
| Armazenamento máximo gratuitamente | 5 GB |
| Número máximo de bancos de dados de throughput compartilhados | 5 |
| Número máximo de contêineres em um banco de dados de throughput compartilhado | 25 <br>Em contas de nível livre, o mínimo de RU/s para um banco de dados de throughput compartilhado com até 25 contêineres é de 400 RU/s. |

  Além do acima, os [limites per-account](#per-account-limits) também se aplicam a contas de nível gratuito.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os conceitos principais do Cosmos DB [de distribuição global](distribute-data-globally.md) e [particionamento](partitioning-overview.md) e [throughput provisionado](request-units.md).

Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

* [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
