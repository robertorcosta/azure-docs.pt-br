---
title: Criar um exemplo de aplicativo do SDK do Java de ponta a ponta Azure Cosmos DB usando o feed de alterações
description: Este guia orienta você por meio de um aplicativo simples de API do SQL Java que insere documentos em um Azure Cosmos DB contêiner, mantendo uma exibição materializada do contêiner usando o feed de alterações.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125665"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Como criar um aplicativo Java que usa Azure Cosmos DB API do SQL e o processador do feed de alterações

> [!IMPORTANT]  
> Para obter mais informações sobre Azure Cosmos DB SDK do Java v4, consulte as notas de versão do Azure Cosmos DB SDK do Java v4, o [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), a Azure Cosmos DB [dicas de desempenho](performance-tips-java-sdk-v4-sql.md)do SDK do Java v4 e Azure Cosmos DB guia de [solução de problemas](troubleshoot-java-sdk-v4-sql.md)do SDK do Java v4.
>

Este guia de instruções o orienta através de um aplicativo Java simples que usa a API do SQL Azure Cosmos DB para inserir documentos em um contêiner de Azure Cosmos DB, mantendo uma exibição materializada do contêiner usando o feed de alterações e o processador do feed de alterações. O aplicativo Java se comunica com a API do SQL Azure Cosmos DB usando Azure Cosmos DB SDK do Java v4.

## <a name="prerequisites"></a>Pré-requisitos

* O URI e a chave da sua conta do Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Segundo plano

O feed de alterações Azure Cosmos DB fornece uma interface controlada por evento para disparar ações em resposta à inserção de documentos. Há muitos usos para isso. Por exemplo, em aplicativos que são de leitura e gravação pesadas, o uso do feed de alterações é criar uma **exibição materializada** em tempo real de um contêiner, pois ele está ingerindo documentos. O contêiner de exibição materializada armazenará os mesmos dados, mas particionados para leituras eficientes, tornando o aplicativo de leitura e gravação eficiente.

O trabalho de gerenciar eventos do feed de alterações é amplamente resolvido pela biblioteca do processador do feed de alterações interna no SDK. Essa biblioteca é eficiente o suficiente para distribuir eventos do feed de alterações entre vários trabalhadores, se desejado. Tudo o que você precisa fazer é fornecer à biblioteca de feeds de alterações um retorno de chamada.

Este exemplo simples demonstra a biblioteca do processador do feed de alterações com um único trabalho criando e excluindo documentos de uma exibição materializada.

## <a name="setup"></a>Instalação

Caso ainda não tenha feito isso, clone o repositório de exemplo do aplicativo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Abra um terminal no diretório do repositório. Crie o aplicativo executando

```bash
mvn clean package
```

## <a name="walkthrough"></a>Passo a passo

1. Como uma primeira verificação, você deve ter uma conta do Azure Cosmos DB. Abra o **portal do Azure** no navegador, vá para sua conta do Azure Cosmos DB e, no painel esquerdo, navegue até **Data Explorer**.

    ![Conta do Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Execute o aplicativo no terminal usando o seguinte comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Pressione ENTER quando vir

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    em seguida, retorne ao portal do Azure Data Explorer no navegador. Você verá que um banco de dados **GroceryStoreDatabase** foi adicionado com três contêineres vazios: 

    * **InventoryContainer**: o registro de inventário do nosso supermercado de exemplo, particionado no item ```id```, que é um UUID.
    * **InventoryContainer-pktype**: uma exibição materializada do registro de inventário, otimizada para consultas no item ```type```
    * **InventoryContainer-concessões** -um contêiner de concessões é sempre necessário para o feed de alterações; as concessões acompanham o progresso do aplicativo na leitura do feed de alterações.


    ![Contêineres vazios](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. No terminal, agora você deverá ver um aviso

    ```bash
    Press enter to start creating the materialized view...
    ```

    Pressione ENTER. Agora, o seguinte bloco de código executará e inicializará o processador do feed de alterações em outro thread: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"``` é o nome do trabalho do processador do feed de alterações. ```changeFeedProcessorInstance.start()``` é o que realmente inicia o processador do feed de alterações.

    Retorne ao portal do Azure Data Explorer no navegador. No contêiner **InventoryContainer-concessões**, clique em **items** para ver o conteúdo. Você verá que o processador do feed de alterações preencheu o contêiner de concessão, ou seja, o processador atribuiu ao trabalho ```SampleHost_1``` uma concessão em algumas partições do **InventoryContainer**.

    ![Concessões](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Pressione ENTER novamente no terminal. Isso disparará dez documentos a serem inseridos no **InventoryContainer**. Cada inserção de documento aparece no feed de alterações como JSON; o código de retorno de chamada a seguir manipula esses eventos espelhando os documentos JSON em uma exibição materializada:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. Permita que o código seja executado de 5 a 10 segundos. Em seguida, retorne ao portal do Azure Data Explorer e navegue até **InventoryContainer > itens**. Você deverá ver que os itens estão sendo inseridos no contêiner de inventário; observe a chave de partição (```id```).

    ![Contêiner do feed](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Agora, no Data Explorer, navegue até **InventoryContainer-pktype > items**. Esta é a exibição materializada-os itens neste contêiner espelham **InventoryContainer** porque foram inseridos programaticamente pelo feed de alterações. Anote a chave de partição (```type```). Portanto, essa exibição materializada é otimizada para filtragem de consultas em ```type```, o que será ineficiente em **InventoryContainer**, porque ele é particionado em ```id```.

    ![Exibição materializada](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Vamos excluir um documento de **InventoryContainer** e de **InventoryContainer-pktype** usando apenas uma só chamada ```upsertItem()```. Primeiro, dê uma olhada portal do Azure Data Explorer. Excluiremos o documento para o qual ```/type == "plums"```; ele é circulado em vermelho abaixo

    ![Exibição materializada](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Pressione ENTER novamente para chamar a função ```deleteDocument()``` no código de exemplo. Essa função, mostrada abaixo, faz upsert de uma nova versão do documento com ```/ttl == 5```, que define a TTL (vida útil) do documento como 5 segundos. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    O feed de alterações ```feedPollDelay``` é definido como 100 ms; portanto, o feed de alterações responde a essa atualização quase instantaneamente e chama o ```updateInventoryTypeMaterializedView()``` mostrado acima. Essa última chamada de função fará upsert do novo documento com uma TTL de 5 segundos em **InventoryContainer-pktype**.

    O efeito é que, após cerca de 5 segundos, o documento vai expirar e ser excluído de ambos os contêineres.

    Esse procedimento é necessário porque o feed de alterações só emite eventos na inserção ou atualização de item, não na exclusão do item.

1. Pressione ENTER mais uma vez para fechar o programa e limpar os recursos.
