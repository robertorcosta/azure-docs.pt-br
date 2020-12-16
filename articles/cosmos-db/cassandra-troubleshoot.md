---
title: Solucionar erros comuns no Azure Cosmos DB API do Cassandra
description: Este documento discute as maneiras de solucionar problemas comuns encontrados no Azure Cosmos DB API do Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: f5f2cb5ac8c354df38310cdcb47b98e1da5b6cfa
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521809"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Solucionar problemas comuns no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB é uma camada de compatibilidade, que fornece [suporte de protocolo de transmissão](cassandra-support.md) para o popular banco de dados Apache Cassandra de software livre e é alimentado por [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). Como um serviço nativo de nuvem totalmente gerenciado, a Azure Cosmos DB fornece [garantias sobre disponibilidade, taxa de transferência e consistência](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) para API do Cassandra. Essas garantias não são possíveis em implementações herdadas do Apache Cassandra. API do Cassandra também facilita operações de plataforma sem manutenção e patches sem tempo de inatividade. Assim, muitas das operações de back-end são diferentes do Apache Cassandra, portanto, recomendamos configurações e abordagens específicas para evitar erros comuns. 

Este artigo descreve os erros comuns e as soluções para aplicativos que consomem Azure Cosmos DB API do Cassandra.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               |  Descrição             | Solução  |
|---------------------|--------------------------|-----------|
| Com sobrecarregamentoexception (Java) | O número total de unidades de solicitação consumidas é maior do que as unidades de solicitação provisionadas na tabela ou no espaço de keyspace. Portanto, as solicitações são limitadas. | Considere dimensionar a taxa de transferência atribuída a um keyspace ou uma tabela do portal do Azure (consulte [aqui](manage-scale-cassandra.md) para operações de dimensionamento no API do Cassandra) ou você pode implementar uma política de repetição. Para Java, consulte amostras de repetição para [Driver v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [Driver v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consulte também [extensões do Cassandra do Azure Cosmos para Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| Sobreloadedexception (Java) mesmo com taxa de transferência suficiente | O sistema parece estar limitando as solicitações, apesar da taxa de transferência suficiente ser provisionada para o volume de solicitação e/ou o custo da unidade de solicitação consumida  | API do Cassandra implementa um orçamento de taxa de transferência do sistema para operações de nível de esquema (CREATE TABLE, ALTER TABLE, DROP TABLE). Esse orçamento deve ser suficiente para operações de esquema em um sistema de produção. No entanto, se você tiver um grande número de operações de nível de esquema, é possível que você esteja excedendo esse limite. Como esse orçamento não é controlado pelo usuário, você precisará considerar reduzir o número de operações de esquema que estão sendo executadas. Se a execução dessa ação não resolver o problema, ou não for viável para sua carga de trabalho, [crie uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Após um período de tempo ocioso após as conexões bem-sucedidas, o aplicativo não consegue se conectar| Esse erro pode ser devido ao tempo limite de ociosidade dos balanceadores do Azure, que é de 4 minutos. Defina a configuração Keep Alive no driver (veja abaixo) e aumente as configurações Keep-Alive no sistema operacional ou [ajuste o tempo limite de ociosidade no Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Outros erros de conectividade intermitente (Java) | A conexão cai ou expira inesperadamente | Os drivers do Apache Cassandra para Java fornecem duas políticas de reconexão nativas: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . O padrão é `ExponentialReconnectionPolicy`. No entanto, para Azure Cosmos DB API do Cassandra, é recomendável ter `ConstantReconnectionPolicy` um atraso de 2 segundos. Consulte a [documentação do driver](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)  para o driver Java v4. x e [Veja aqui](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/) as diretrizes do Java 3. x (consulte também os exemplos abaixo).|

Se o seu erro não estiver listado acima, e você estiver recebendo um erro ao executar uma [operação com suporte no API do Cassandra](cassandra-support.md), em que o erro *não está presente ao usar o Apache Cassandra nativo*, [crie uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Configurando o ReconnectionPolicy para driver Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3. x do driver do Java, configure a política de reconexão ao criar um objeto de cluster:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versão 4. x

Para a versão 4. x do driver do Java, configure a política de reconexão substituindo as configurações no `reference.conf` arquivo:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Habilitar Keep-Alive para o driver Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3. x do driver do Java, defina Keep-Alive ao criar um objeto de cluster e verifique se Keep-Alive está [habilitado no sistema operacional](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Versão 4. x

Para a versão 4. x do driver do Java, defina Keep-Alive substituindo as configurações no `reference.conf` e verifique se Keep-Alive está [habilitado no sistema operacional](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [recursos com suporte](cassandra-support.md) no API do Cassandra Azure Cosmos DB.
- Saiba como [migrar do Apache Cassandra nativo para Azure Cosmos DB API do Cassandra](cassandra-migrate-cosmos-db-databricks.md)

