---
title: Tutorial – Associar um Azure Cosmos DB ao aplicativo Azure Spring Cloud
description: Neste tutorial, saiba como associar um Azure Cosmos DB ao aplicativo Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 7e796c6f8b2ae17ba267a19da1d909087163d99c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708840"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Tutorial: Associar um Azure Cosmos DB ao aplicativo Azure Spring Cloud

O Azure Spring Cloud permite que você associe serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente seu aplicativo Spring Boot. Este artigo demonstra como associar o aplicativo a um Azure Cosmos DB.

Pré-requisitos:
* Uma instância do Azure Spring Cloud implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar a usar.
* Uma conta do Azure Cosmos DB com um nível mínimo de permissões de colaborador.

## <a name="bind-azure-cosmos-db"></a>Associar o Azure Cosmos DB

Azure Cosmos DB tem cinco tipos de API diferentes que oferecem suporte a associação:

1. Crie um banco de dados do Azure Cosmos DB. [Veja este artigo](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) para obter ajuda com a criação do banco de dados. Anote o nome do banco de dados. O nosso se chama `testdb`.

1. Adicione uma das seguintes dependências no `pom.xml` do seu aplicativo Spring Cloud de acordo com o tipo de API.
    
    #### <a name="api-type-core-sql"></a>Tipo de API: Núcleo (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Tipo de API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Tipo de API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Tipo de API: Gremlin (grafo)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Tipo de API: tabela do Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma implantação para essa alteração usando `az spring-cloud app deployment create`.  Esses comandos atualizarão ou criarão o aplicativo com a nova dependência.

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure. Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Localize o **Painel do Aplicativo** e selecione o aplicativo a ser associado ao Cosmos DB. Em seguida, selecione `Service binding` e o botão `Create service binding`. Preencha o formulário, selecionando o **Tipo de associação** `Azure Cosmos DB`, o tipo de API, o nome do banco de dados e a conta do Azure Cosmos DB.

    > [!NOTE]
    > Use um espaço de chave para o nome do banco de dados se você estiver usando o Cassandra.

1. Reinicie o aplicativo selecionando o botão **Reiniciar** na página do aplicativo.

1. Para assegurar que o serviço esteja associado corretamente, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ter esta aparência:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a associar seu aplicativo do Azure Spring Cloud a um banco de dados CosmosDB.  Para saber como associar seu aplicativo a um Cache do Azure para Redis, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Associar um aplicativo Azure Spring Cloud a um Cache do Azure para Redis](spring-cloud-tutorial-bind-redis.md).
