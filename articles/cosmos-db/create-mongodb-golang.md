---
title: Aplicativo de console usando a API para MongoDB do Azure Cosmos DB e o SDK do Golang
description: Apresenta um exemplo de código do Golang que você pode usar para se conectar à API para MongoDB do Azure Cosmos DB e consultá-la.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 94be0ec16aedc317f1be41998356bc52b66f7e86
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619204"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Início Rápido: Criar um aplicativo de console usando a API para MongoDB do Azure Cosmos DB e o SDK do Golang

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e distribuição global no núcleo do Cosmos DB.

Este início rápido demonstra como usar um aplicativo MongoDB existente escrito em [Golang](https://golang.org/) e conectá-lo ao banco de dados do Cosmos usando a API para MongoDB do Azure Cosmos DB.

Em outras palavras, o aplicativo Golang só sabe que está se conectando usando um cliente do MongoDB. Está claro para o aplicativo que os dados estão armazenados em um banco de dados do Cosmos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) e um conhecimento básico sobre a linguagem [Go](https://golang.org/).
- Um IDE — [GoLand](https://www.jetbrains.com/go/) da Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) da Microsoft ou [Atom](https://atom.io/). Neste tutorial, estou usando GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Clone o aplicativo de exemplo e instale os pacotes necessários.

1. Crie uma pasta chamada CosmosDBSample dentro da pasta GOROOT\src, que é C:\Go\ por padrão.
2. Execute o comando a seguir usando uma janela do terminal git como git bash para clonar o repositório de exemplo na pasta CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Execute o comando a seguir para obter o pacote mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

O driver [mgo](https://labix.org/mgo) é um driver [MongoDB](https://www.mongodb.com/) para a [linguagem Go](https://golang.org/) que implementa uma seleção bem testada e avançada de recursos em uma API muito simples seguindo expressões Go padrão.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. Clique em **Início rápido** no menu de navegação à esquerda e clique em **Outros** para exibir as informações de cadeia de conexão exigidas pelo aplicativo Go.

2. No Goglang, abra o arquivo main.go no diretório GOROOT\CosmosDBSample e atualize as linhas de código a seguir usando as informações de cadeia de conexão do portal do Azure, conforme mostrado na captura de tela a seguir. 

    O Nome do banco de dados é o prefixo do valor **Host** no painel de cadeia de conexão de portal do Azure. Para a conta mostrada na imagem abaixo, o nome do banco de dados é golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Painel Início rápido, guia Outros no portal do Azure mostrando as informações de cadeia de conexão](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Salve o arquivo main.go.

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Executar o aplicativo](#run-the-app). 

Todos os snippets de código a seguir são retirados do arquivo main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Conectando o aplicativo Go ao Cosmos DB

A API do Azure Cosmos DB para MongoDB dá suporte à conexão habilitada para TLS. Para se conectar, você precisa definir a função **DialServer** em [mgo.DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo) e usar a função [tls.*Dial*](https://golang.org/pkg/crypto/tls#Dial) para realizar a conexão.

O snippet de código Golang a seguir se conecta ao aplicativo Go com a API para MongoDB do Azure Cosmos DB. A classe *DialInfo* contém opções para estabelecer uma sessão.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

O método **mgo.Dial()** é usado quando não há nenhuma conexão TLS. Para uma conexão TLS, o método **mgo.DialWithInfo()** é necessário.

Uma instância do objeto **DialWIthInfo{}** é usada para criar o objeto de sessão. Quando a sessão é estabelecida, você pode acessar a coleção usando o snippet de código abaixo:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Criar um documento

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Consultar ou ler um documento

O Cosmos DB dá suporte a consultas avançadas de dados armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em sua coleção.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Atualizar um documento

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Excluir um documento

O Cosmos DB dá suporte à exclusão de documentos.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Executar o aplicativo

1. No Golang, verifique se seu GOPATH (disponível em **Arquivo**, **Configurações**, **Go**, **GOPATH**) inclui a localização em que o gopkg foi instalado, que é PERFILDOUSUÁRIO/go por padrão. 
2. Comente as linhas que excluem o documento, linhas 103 a 107, para que você possa ver o documento depois de executar o aplicativo.
3. No Golang, clique em **Executar** e em **Executar 'Compilar main.go e executar'** .

    O aplicativo termina e exibe a descrição do documento criado em [Criar um documento](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Golang mostrando a saída do aplicativo](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Revisar o documento no Data Explorer

Volte para o portal do Azure a fim de ver o documento no Data Explorer.

1. Clique em **Data Explorer (Versão prévia)** no menu de navegação à esquerda, expanda **golang-coach**, **pacote**e clique em **Documentos**. Na guia **Documentos** , clique na \_id para exibir o documento no painel direito. 

    ![Data Explorer mostrando o documento recém-criado](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Você pode trabalhar com o documento em linha e clicar em **Atualizar** para salvá-lo. Você também pode excluir o documento ou criar novos documentos ou consultas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Cosmos e executar um aplicativo Golang. Agora você pode importar dados adicionais para o banco de dados Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md)
