---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 9c6ce482a4f6464008f4be0ecd94455f0239ede2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275374"
---
1. Vá para o [portal do Azure](https://portal.azure.com/) para criar uma conta do Azure Cosmos DB. Pesquise pelo **Azure Cosmos DB** e selecione-o.

   ![O painel Bancos de Dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Selecione **Adicionar**.
1. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações básicas da nova conta do Azure Cosmos. 

    |Configuração|Valor|Descrição |
    |---|---|---|
    |Subscription|Nome da assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos. |
    |Grupo de recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo**, então insira um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Insira um nome para identificar a conta do Azure Cosmos. Já que *documents.Azure.com* é acrescentado ao nome que você fornece para criar o URI, use um nome exclusivo.<br><br>O nome pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ela deve ter entre 3 e 31 caracteres.|
    |API|O tipo de conta a ser criada|Selecione **Núcleo (SQL)** para criar uma consulta e um banco de dados de documento usando a sintaxe SQL. <br><br>A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Núcleo (SQL) e MongoDB para dados de documento, Gremlin para dados de grafo, Tabela do Azure e Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API do SQL](../articles/cosmos-db/documentdb-introduction.md).|
    |Aplicar desconto por nível gratuito|Aplicar ou não aplicar|Com o nível gratuito do Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento gratuitamente em uma conta. Saiba mais sobre o [nível gratuito](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|A região mais próxima dos usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.|
    |Tipo de Conta|Produção ou não produção|Selecione **Produção** se a conta for usada para uma carga de trabalho de produção. Selecione **Não produção** se a conta for usada para não produção, por exemplo, desenvolvimento, teste, garantia de qualidade ou de preparo. Essa é uma configuração de marca de recurso do Azure que ajusta a experiência do Portal, mas não afeta a conta do Azure Cosmos DB subjacente. Você pode alterar esse valor a qualquer momento.|

    > [!NOTE]
    > Você pode ter no máximo uma conta do nível gratuito do Azure Cosmos DB por assinatura do Azure e deve aceitar ao criar a conta. Se você não vir a opção de aplicar o desconto por nível gratuito, significa que outra conta da assinatura já foi habilitada com o nível gratuito.
   
   ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Selecione **Examinar + criar**. Você pode ignorar as seções **Rede** e **Marcas**.

1. Examine as configurações da conta e selecione **Criar**. São necessários alguns minutos para criar a conta. Aguarde até que a página do portal exiba **Sua implantação está concluída**. 

    ![O painel Notificações do portal do Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecione **Ir para recurso** para ir para a página da conta do Azure Cosmos DB. 

    ![A página da conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
