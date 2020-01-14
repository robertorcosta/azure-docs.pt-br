---
title: Criar um aplicativo Web do Python Flask usando a API para MongoDB do Azure Cosmos DB
description: Apresenta um exemplo de código Python do Flask que você pode usar para se conectar à API para MongoDB do Azure Cosmos DB e consultá-la.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 8e58d0bdaaa5e4fb4564a68b46de7887ec28336d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445486"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início Rápido: Criar um aplicativo do Python usando a API para MongoDB do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e distribuição global no núcleo do Cosmos DB.

Este guia de início rápido, usa o seguinte [exemplo do Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) e demonstra como criar um aplicativo Flask simples de tarefas pendentes usando o [Emulador do Azure Cosmos DB](local-emulator.md) e a API para MongoDB do Azure Cosmos DB.

## <a name="prerequisites"></a>Prerequisites

- Baixe o [Emulador do Azure Cosmos DB](local-emulator.md). No momento, o emulador é compatível apenas no Windows. O exemplo mostra como usar o exemplo com uma chave de produção do Azure, o que pode ser feito em qualquer plataforma.

- Se você ainda não tiver o Visual Studio Code instalado, instale rapidamente o [VS Code](https://code.visualstudio.com/Download) para sua plataforma (Windows, Mac, Linux).

- Lembre-se de adicionar o suporte à linguagem Python ao instalar uma das extensões do Python populares.
  1. Selecione uma extensão.
  2. Instale a extensão digitando `ext install` na paleta de comandos `Ctrl+Shift+P`.

     Os exemplos neste documento usam a popular e completa [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) do Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo Flask-MongoDB do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática.

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Execute o seguinte comando para instalar os módulos do Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Abra a pasta no Visual Studio Code.

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Executar o aplicativo Web](#run-the-web-app). 

Os snippets de código a seguir são obtidos do arquivo app.py e usam a cadeia de conexão para o Emulador do Azure Cosmos DB. A senha deve ser dividida da maneira mostrada abaixo para acomodar as barras "/" que não podem ser analisadas de nenhuma outra maneira.

* Inicialize o cliente MongoDB, recupere o banco de dados e autentique-se.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Recupere a coleção ou crie-a se ela ainda não existir.

    ```python
    todos = db.todo #Select the collection
    ```

* Criar o aplicativo

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. Verifique se o emulador do Azure Cosmos DB está em execução.

2. Abra uma janela do terminal e emita `cd` para o diretório no qual o aplicativo está salvo.

3. Em seguida, defina a variável de ambiente para o aplicativo Flask com `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` para editores do PowerShell ou `export FLASK_APP=app.py` se você estiver usando um Mac. 

4. Execute o aplicativo com `flask run` e navegue até [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Se você quiser testar o código em relação a uma conta ativa do Cosmos, acesse o portal do Azure para criar uma conta e obter as informações da cadeia de conexão. Em seguida, copie-a no aplicativo.

1. No [portal do Azure](https://portal.azure.com/), na sua conta do Cosmos, no painel de navegação esquerdo, clique em **Cadeia de Conexão** e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar o Nome de usuário, Senha e Host para o arquivo Dal.cs na próxima etapa.

2. Abra o arquivo **app.py** no diretório raiz.

3. Copie o valor de **nome de usuário** do portal (usando o botão de cópia) e cole-o como o valor de **nome** no arquivo **app.py**.

4. Em seguida, copie o valor de **cadeia de conexão** do portal e cole-o como o valor de MongoClient no arquivo **app.py**.

5. Finalmente copie o valor de **senha** do portal e cole-o como o valor de **senha** no arquivo **app.py**.

Agora, você atualizou o aplicativo com todas as informações necessárias para comunicar-se com o Cosmos DB. É possível executá-lo da mesma forma que antes.

## <a name="deploy-to-azure"></a>Implantar no Azure

Para implantar esse aplicativo, você pode criar um novo aplicativo Web no Azure e habilitar a implantação contínua com um fork deste repositório do GitHub. Siga este [tutorial](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) para configurar a implantação contínua com o GitHub no Azure.

Ao implantar no Azure, você deve remover as chaves do aplicativo e verificar se a seção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, você precisa adicionar as suas informações de MONGOURL, MONGO_PASSWORD e MONGO_USERNAME nas configurações do aplicativo. Você pode seguir este [tutorial](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) para saber mais sobre as configurações de aplicativo em aplicativos Web do Azure.

Se você não quiser criar um fork deste repositório, clique no botão Implantar no Azure abaixo. Em seguida, entre no Azure e defina as configurações do aplicativo com as informações da sua conta do Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se você planeja armazenar o código no GitHub ou em outras opções de controle do código-fonte, lembre-se de remover as cadeias de conexão do código. Nesse caso, elas podem ser definidas com as configurações do aplicativo Web.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Cosmos e executar um aplicativo Flask. Agora você pode importar dados adicionais para o banco de dados Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md)
