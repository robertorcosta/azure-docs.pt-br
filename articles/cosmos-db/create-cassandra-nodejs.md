---
title: 'Início Rápido: API do Cassandra com .Node.js – Azure Cosmos DB'
description: Este guia de início rápido mostra como usar a API Cassandra do Azure Cosmos DB para criar um aplicativo de perfil com Node.js
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210329"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Início Rápido: compilar um aplicativo do Cassandra com o SDK do Node.js e o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Neste início rápido, você cria uma conta de API do Cassandra do Azure Cosmos DB e usa um aplicativo Cassandra Node.js clonado no GitHub para criar um banco de dados e contêiner Cassandra. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Como alternativa, você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso.

Além disso, você precisa de:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) versão v0.10.29 ou superior
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos clonar um aplicativo da API do Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando. Crie uma pasta chamada `git-samples`. Em seguida, feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do Git, como git bash. Use o comando `cd` para mudar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o código cria os recursos de banco de dados,examine os snippets a seguir. Os snippets são todos obtidos do arquivo `uprofile.js` na pasta `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

* Os valores de nome de usuário e senha são definidos usando a página de cadeia de conexão no portal do Microsoft Azure. O `path\to\cert` fornece um caminho para um X509 certificado. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* O `client` é inicializado com informações do contactPoint. O contactPoint é recuperado do Portal do Azure.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* O `client` se conecta à API Cassandra do Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* Um novo keyspace é criado.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Uma nova tabela é criada.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Entidades de chave/valor são inseridas.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Consulta para obter todos os pares chave-valor.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Consulta para obter uma chave-valor.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. A cadeia de conexão permite que seu aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

    Use o ![botão Copiar](./media/create-cassandra-nodejs/copy.png) no lado direito da tela para copiar o valor superior, o PONTO DE CONTATO.

    ![Exibir e copiar o PONTO DE CONTATO, o NOME DE USUÁRIO e a SENHA da página de cadeia de conexão do Portal do Azure](./media/create-cassandra-nodejs/keys.png)

2. Abra o arquivo `config.js` . 

3. Cole o valor PONTO DE CONTATO do portal sobre `<FillMEIN>` na linha 4.

    Agora a linha 4 deve ser semelhante a 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Copie o valor NOME DE USUÁRIO do portal e cole-o sobre `<FillMEIN>` na linha 2.

    Agora a linha 2 deve ser semelhante a 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copie o valor SENHA do portal e cole-o sobre `<FillMEIN>` na linha 3.

    Agora a linha 3 deve ser semelhante a

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Salve o arquivo `config.js`.
    
## <a name="use-the-x509-certificate"></a>Use o certificado X509

1. Baixe o certificado de Baltimore CyberTrust Root localmente do [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Renomeie o arquivo usando a extensão de arquivo `.cer`.

   O certificado tem o número de série `02:00:00:b9` e a impressão digital SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Abra `uprofile.js` e altere o `path\to\cert` para apontar para o novo certificado.

3. Salvar `uprofile.js`.

> [!NOTE]
> Caso você receba um erro relacionado ao certificado nas etapas posteriores e esteja usando um computador Windows, verifique se seguiu o processo para converter corretamente um arquivo .crt no formato .cer da Microsoft abaixo.
> 
> Clique duas vezes no arquivo .crt para abri-lo na exibição do certificado. 
>
> ![Exibir e verificar a saída](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Clique em Avançar no Assistente de Certificado. Selecione X.509 de codificação de Base 64 (.CER) e, em seguida, Avançar.
>
> ![Exibir e verificar a saída](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Selecione Procurar (para localizar um destino) e digite um nome de arquivo.
> Selecione Avançar e, em seguida, Concluir.
>
> Agora você deverá ter um arquivo .cer formatado corretamente. Verifique se o caminho em `uprofile.js` aponta para esse arquivo.

## <a name="run-the-nodejs-app"></a>Executar o aplicativo do Node.js

1. Na janela do terminal do Git, verifique se você está no diretório de exemplo clonado anteriormente:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Execute `npm install` para instalar os módulos npm necessários.

3. Execute `node uprofile.js` para iniciar seu aplicativo de nó.

4. Verifique se os resultados estão conforme o esperado na linha de comando.

    ![Exibir e verificar a saída](./media/create-cassandra-nodejs/output.png)

    Pressione CTRL+C para interromper a execução do programa e feche a janela do console. 

5. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados. 

    ![Exibir os dados no Data Explorer](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB com a API do Cassandra e executar um aplicativo Cassandra Node.js que cria um banco de dados e contêiner Cassandra. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)