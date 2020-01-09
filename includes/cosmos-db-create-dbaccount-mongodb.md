---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 0acc989f558c5de4e8f52ba2c77f2842f8d5d70c
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75752289"
---
1. Em uma nova janela, entre no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **Criar um recurso**, **Bancos de dados** e em **Azure Cosmos DB**, selecione **Criar**.
   
   ![Captura de tela do portal do Azure, realçando Mais Serviços e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|DESCRIÇÃO
    ---|---|---
    Subscription|Sua assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|Criar Novo<br><br>Em seguida, insira o mesmo nome exclusivo fornecido na ID|Selecione **Criar novo**. Insira o novo nome do grupo de recursos para sua conta. Para simplificar, use um nome igual à sua ID. 
    Nome da Conta|Insira um nome exclusivo|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB. Como *documents.Azure.com* é acrescentado à ID que você fornece para criar o URI, use uma ID exclusiva.<br><br>A ID deve conter apenas letras minúsculas, números e o caractere de hífen (-). Ela precisa ter entre 3 e 31 caracteres.
    API|API do Azure Cosmos DB para MongoDB|A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Core (SQL) para bancos de dados de documentos, Gremlin para o bancos de dados de grafos, API para MongoDB do Azure Cosmos DB para bancos de dados de documentos, Tabela do Azure e Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>Selecione **MongoDB**, porque neste guia de início rápido você está criando uma tabela que funciona com o MongoDB.|
    Location|Selecione a região mais próxima de seus usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.

    Selecione **Revisar + Criar**. Você pode ignorar as seções **Rede** e **Marcas**. 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página **Parabéns! Sua conta do Cosmos com a compatibilidade de protocolo de transmissão para o MongoDB está pronta**.

    ![O painel Notificações do portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
