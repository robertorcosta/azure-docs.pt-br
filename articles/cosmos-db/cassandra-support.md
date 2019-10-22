---
title: Recursos do Apache Cassandra e comandos compatíveis com a API do Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte a recursos do Apache Cassandra na API do Cassandra do Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 66a972e66c35cdd5b8dedceefbe3dbd008380da9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327150"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Recursos do Apache Cassandra compatíveis com a API do Cassandra do Azure Cosmos DB 

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API do Cassandra do Azure Cosmos DB por meio de [drivers](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) de cliente do Cassandra de software livre em conformidade com o [protocolo de transmissão](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) da CQL (linguagem de consulta do Cassandra) v4. 

Ao usar a API do Cassandra do Azure Cosmos DB, você pode aproveitar os benefícios das APIs do Apache Cassandra, bem como as funcionalidades empresariais que o Azure Cosmos DB fornece. As funcionalidades empresariais incluem [distribuição global](distribute-data-globally.md), [particionamento com expansão automática](partition-data.md), garantias de disponibilidade e latência, criptografia em repouso, backups e muito mais.

## <a name="cassandra-protocol"></a>Protocolo do Cassandra 

A API do Cassandra do Azure Cosmos DB é compatível com a versão do CQL **v4**. Os comandos, ferramentas, limitações e exceções do CQL compatíveis estão listados abaixo. Qualquer driver de cliente que compreenda esses protocolos deve ser capaz de conectar-se à API do Cassandra do Azure Cosmos DB.

## <a name="cassandra-driver"></a>Driver do Cassandra

As seguintes versões de drivers Cassandra são compatíveis com a API do Cassandra do Azure Cosmos DB:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Tipos de dados CQL 

A API do Cassandra do Azure Cosmos DB é compatível com os seguintes tipos de dados do CQL:

* ascii  
* bigint  
* blob  
* booleano  
* contador  
* date  
* decimal  
* double  
* flutuante  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Funções do CQL

A API do Cassandra do Azure Cosmos DB é compatível com as seguintes funções do CQL:

* A criptografia do token  
* Funções de agregação
  * mín., máx., média, contagem
* Funções de conversão de blob 
  * typeAsBlob(value)  
  * blobAsType(value)
* Funções UUID e timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Limites de API do Cassandra

A API do Cassandra do Azure Cosmos DB não tem limites para o tamanho dos dados armazenados em uma tabela. Centenas de terabytes ou petabytes de dados podem ser armazenados, com a garantia de que os limites de chave de partição sejam respeitados. Da mesma forma, cada entidade ou linha equivalente não tem nenhum limite para o número de colunas. No entanto, o tamanho total da entidade não deve exceder 2 MB. Os dados por chave de partição não podem exceder 10 GB, assim como em todas as outras APIs.

## <a name="tools"></a>Ferramentas 

A API do Cassandra do Azure Cosmos DB é uma plataforma de serviço gerenciado. Ela não requer nenhuma sobrecarga de gerenciamento nem utilitários como o Coletor de Lixo, a JVM (Máquina Virtual Java) e o nodetool para gerenciar o cluster. Ela dá suporte a ferramentas como o cqlsh, que utiliza a compatibilidade binária CQLv4. 

* O Data Explorer do portal do Azure, as Métricas, os Logs de Diagnóstico, o PowerShell e a CLI são outros mecanismos compatíveis para gerenciar a conta.

## <a name="cql-shell"></a>Shell do CQL  

O utilitário de linha de comando CQLSH vem com o Apache Cassandra 3.1.1 e é pronto para uso, funcionando imediatamente e com as seguintes variáveis de ambiente habilitadas:

Antes de executar os comandos a seguir, [adicione um certificado raiz Baltimore ao repositório cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**UNIX/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Comandos da CQL

O Azure Cosmos DB dá suporte aos seguintes comandos de banco de dados em contas da API do Cassandra.

* CREATE KEYSPACE (as configurações de replicação para este comando são ignoradas)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECIONAR 
* UPDATE 
* BATCH – Somente comandos não registrados são compatíveis 
* EXCLUIR

Todas as operações CRUD, quando executadas por meio de um SDK compatível com CQLV4, retornarão informações adicionais sobre erros, unidades de solicitação consumidas. Os comandos delete e update precisam ser tratados considerando-se a governança de recursos, para evitar o uso da taxa de transferência provisionada. 
* Observação O valor de gc_grace_seconds deverá ser zero se for especificado.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API do Cassandra do Azure Cosmos DB fornece a opção de consistência para operações de leitura.  O mapeamento de consistência está detalhado [aqui](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Gerenciamento de funções e de permissões

O Azure Cosmos DB dá suporte ao controle de acesso baseado em função (RBAC) para provisionamento, revezamento de chaves, visualização de métricas e senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com). O Azure Cosmos DB não dá suporte a funções para atividades CRUD.

## <a name="keyspace-and-table-options"></a>Opções de tabela e de keyspace

As opções para nome da região, classe, replication_factor e datacenter no comando "CREATE KEYSPACE" são ignoradas atualmente. O sistema usa o método de replicação de [distribuição global](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) subjacente do Azure Cosmos DB para adicionar as regiões. Se você precisar da presença de dados entre regiões, poderá habilitá-la no nível da conta com o PowerShell, a CLI ou o portal. Para saber mais, confira o artigo [como adicionar regiões](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Durable_writes não podem ser desabilitadas porque o Azure Cosmos DB garante que cada gravação seja durável. Em todas as regiões, o Azure Cosmos DB replica os dados no conjunto de réplicas composto por quatro réplicas e essa [configuração](global-dist-under-the-hood.md) de conjunto de réplicas não pode ser modificada.
 
Todas as opções são ignoradas ao criar a tabela com a exceção de gc_grace_seconds, que deve ser definida como zero.
O keyspace e a tabela têm uma opção extra chamada "cosmosdb_provisioned_throughput", com um valor mínimo de 400 RU/s. A taxa de transferência do keyspace permite o compartilhamento de taxa de transferência em várias tabelas e é útil para cenários em que todas as tabelas não estão utilizando a taxa de transferência provisionada. O comando ALTER TABLE permite alterar a taxa de transferência provisionada entre as regiões. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Uso da política de nova tentativa de conexão do Cassandra

O Azure Cosmos DB é um sistema controlado por recursos. Isso significa que você pode fazer um determinado número de operações em um determinado segundo com base nas unidades de solicitação consumidas pelas operações. Se um aplicativo exceder esse limite em um determinado segundo, as solicitações serão limitadas por taxa e exceções serão geradas. A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra. Para garantir que seu aplicativo possa interceptar e repetir solicitações na limitação da taxa de caso, as extensões [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) são fornecidas. Se você usar outros SDKs para acessar API do Cassandra no Azure Cosmos DB, crie uma política de conexão para tentar novamente ao encontrar essas exceções.

## <a name="next-steps"></a>Próximas etapas

- Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) pelo uso de um aplicativo Java

