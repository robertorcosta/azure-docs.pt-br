---
title: Use o Studio 3T para se conectar à API do Azure Cosmos DB para MongoDB
description: Saiba como se conectar à API do Azure Cosmos DB para MongoDB usando o Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 9b8c3a36dbdd3a14d0bd325c22421033a1765df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063689"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Conecte-se a uma conta do Azure Cosmos usando o Studio 3T

Para conectar-se à API do Azure Cosmos DB para MongoDB usando o Studio 3T, você deve:

* Baixe e instale [o Studio 3T](https://studio3t.com/).
* Tenha as informações da [seqüência](connect-mongodb-account.md) de conexões da sua conta Do Azure Cosmos.

> [!NOTE]
> Atualmente, robo 3T v1.2 e versões inferiores são suportados com a API do Cosmos DB para MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Criar a conexão no Studio 3T

Para adicionar sua conta do Azure Cosmos ao gerenciador de conexões Studio 3T, use as seguintes etapas:

1. Recupere as informações de conexão para a aPI do Seu Azure Cosmos DB para conta MongoDB usando as instruções no [aplicativo Conecte um MongoDB ao artigo do Azure Cosmos DB.](connect-mongodb-account.md)

    ![Captura de tela da página de seqüência de seqüência de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    ![Captura de tela do gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **Nova conexão,** na guia **Servidor,** digite o HOST (FQDN) da conta Do Azure Cosmos e do PORT.

    ![Captura de tela da guia do servidor do gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **Nova Conexão**, na guia **Autenticação**, escolha o Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1) Básico** e insira NOME DE USUÁRIO e SENHA.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia de autenticação do gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **Nova Conexão**, na guia **SSL**, marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados**.

    ![Captura de tela da guia SSL do gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar**.

    ![Captura de tela da janela de conexão de teste do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usar o Studio 3T para criar um banco de dados, coleção e documentos
Para criar um banco de dados, coleta e documentos usando o Studio 3T, execute as seguintes etapas:

1. No **Gerenciador de Conexões**, realce a conexão e clique em **Conectar**.

    ![Captura de tela do gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse no host e escolha **Adicionar Banco de Dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção Studio 3T Add Database](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Captura de tela da opção Coleção de Adicionar ao Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento**.

    ![Captura de tela do item do menu de adicionar documento do Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar Documento, cole o conteúdo a seguir e clique em **Adicionar Documento**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adicione outro documento, dessa vez com o seguinte conteúdo:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o sobrenome 'Andersen' e retorne os campos de estado e pais.

    ![Captura de tela dos resultados da consulta do Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
