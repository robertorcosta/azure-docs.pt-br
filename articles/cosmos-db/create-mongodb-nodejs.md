---
title: 'Início Rápido: Conectar um aplicativo MongoDB do Node.js ao Azure Cosmos DB'
description: Este início rápido demonstra como conectar um aplicativo MongoDB existente escrito em Node.js ao Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: b9cadbf35680953058bd38d7db26a6e6c684836b
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327286"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Início Rápido: Migre um aplicativo Web Node.js do MongoDB existente para o Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Este início rápido demonstra como usar um aplicativo MongoDB existente escrito em Node.js e conectá-lo ao banco de dados do Azure Cosmos, que é compatível com o cliente do MongoDB. Em outras palavras, é transparente para o aplicativo que os dados são armazenados em um banco de dados Cosmos.

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e distribuição global no núcleo do Cosmos DB.

Ao terminar, você terá um aplicativo MEAN (MongoDB, Express, Angular e Node.js) em execução no [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Pré-requisitos 
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Além da CLI do Azure, você precisa do [Node.js](https://nodejs.org/) e do [Git](https://www.git-scm.com/downloads) instalados localmente para executar os comandos `npm` e `git`.

Você deve ter conhecimento prático de Node.js. Este início rápido não se destina a ajudá-lo a desenvolver aplicativos Node.js em geral.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Execute os comandos a seguir para clonar o repositório de exemplo. Esse repositório de exemplo contém o aplicativo [MEAN.js](https://meanjs.org/) padrão.

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
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Executar o aplicativo

Instale os pacotes necessários e inicie o aplicativo.

```bash
cd mean
npm install
npm start
```
O aplicativo tentará se conectar a uma fonte do MongoDB e falhará; prossiga e saia do aplicativo quando a saída retornar "[MongoError: conectar ECONNREFUSED 127.0.0.1:27017]".

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Se você estiver usando uma CLI do Azure instalada, faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/reference-index#az-login) e siga as instruções na tela. Você poderá ignorar esta etapa se estiver usando o Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Adicione o módulo do BD Cosmos do Azure

Se você estiver usando uma CLI do Azure instalada, verifique se o componente `cosmosdb` já está instalado ao executar o comando `az`. Se `cosmosdb` estiver na lista de comandos básicos, vá para o próximo comando. Você poderá ignorar esta etapa se estiver usando o Azure Cloud Shell.

Se `cosmosdb` não estiver na lista de comandos base, reinstale a [CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure, como os aplicativos Web, bancos de dados e contas de armazenamento, são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

Se você estiver usando o Azure Cloud Shell, selecione **Experimente**, siga as instruções na tela para fazer logon e copie o comando para o prompt de comando.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Cosmos com o comando [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

No comando a seguir, substitua os espaços reservados `<cosmosdb-name>` pelo seu próprio nome exclusivo da conta do Cosmos. Esse nome exclusivo será usado como parte do ponto de extremidade do Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), portanto, o nome precisa ser exclusivo entre todas as contas do Cosmos no Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` habilita conexões do cliente MongoDB.

Quando a conta do BD Cosmos do Azure é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. 

> [!NOTE]
> Este exemplo usa JSON como o formato de saída da CLI do Azure, que é o padrão. Para usar outro formato de saída, consulte [Formatos de saída para comandos da CLI do Azure](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Conectar o aplicativo Node.js ao banco de dados

Nesta etapa, você conectará o aplicativo de exemplo MEAN.js ao banco de dados Cosmos que acabou de criar. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de conexão em seu aplicativo Node.js

No seu repositório MEAN.js abra `config/env/local-development.js`.

Substitua o conteúdo deste arquivo pelo código a seguir. Substitua também os dois espaços reservados `<cosmosdb-name>` pelo nome da sua conta do Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Recuperar a chave

Para conectar-se a um banco de dados Cosmos, você precisa da chave de banco de dados. Use o comando [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) para recuperar a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

A CLI do Azure emite informações semelhantes ao seguinte exemplo. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Copie o valor de `primaryMasterKey`. Cole isso no `<primary_master_key>` em `local-development.js`.

Salve suas alterações.

### <a name="run-the-application-again"></a>Execute o aplicativo novamente.

Execute `npm start` novamente. 

```bash
npm start
```

Agora, uma mensagem de console deve informar que o ambiente de desenvolvimento está em execução. 

Vá para `http://localhost:3000` em um navegador. Selecione **Inscrever-se** no menu superior e tente criar dois usuários fictícios. 

O aplicativo de exemplo MEAN.js armazena dados do usuário no banco de dados. Se você tiver êxito e o MEAN.js entrar automaticamente no usuário criado, então a conexão do BD Cosmos do Azure estará funcionando. 

![O MEAN.js conecta-se com êxito ao MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Exibir dados no Data Explorer

Os dados armazenados em um banco de dados Cosmos ficam disponíveis para exibição e consulta no portal do Azure.

Para exibir, consultar e trabalhar com os dados de usuário criados na etapa anterior, faça logon no [portal do Azure](https://portal.azure.com) no seu navegador da Web.

Na caixa Pesquisa superior, insira **Azure Cosmos DB**. Quando a folha da conta do Cosmos abrir, selecione sua conta do Cosmos. No painel de navegação esquerdo, clique em **Data Explorer**. Expanda a coleção no painel Coleções e, em seguida, será possível exibir os documentos na coleção, consultar os dados e até mesmo criar e executar gatilhos, UDFs e procedimentos armazenados. 

![Data Explorer no Portal do Azure](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Implantar o aplicativo Node.js no Azure

Nesta etapa, você implantará o aplicativo Node.js no Cosmos DB.

Você talvez tenha notado que o arquivo de configuração alterado anteriormente é voltado para o ambiente de desenvolvimento (`/config/env/local-development.js`). Quando você implanta seu aplicativo no Serviço de Aplicativo, ele será executado no ambiente de produção por padrão. Agora, você precisa fazer a mesma alteração no arquivo de configuração respectivo.

No seu repositório MEAN.js abra `config/env/production.js`.

No objeto `db`, substitua o valor de `uri` conforme mostrado no exemplo a seguir. Certifique-se de substituir os espaços reservados como antes.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> A opção `ssl=true` é importante porque o [BD Cosmos requer SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

No terminal, confirme todas as alterações no Git. É possível copiar ambos os comandos para os executar juntos.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Cosmos, criar uma coleção e executar um aplicativo de console. Agora você pode importar dados adicionais para o banco de dados Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md)
