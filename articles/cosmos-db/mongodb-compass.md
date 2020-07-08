---
title: Conectar-se ao Azure Cosmos DB usando o Compass
description: Saiba como usar o MongoDB Compass para armazenar e gerenciar dados no Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 2f461ebbd84a66470eba012660db3aefebc8c5e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260773"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Use o MongoDB Compass para conectar-se à API do Azure Cosmos DB para MongoDB

Este tutorial demonstra como usar o [MongoDB Compass](https://www.mongodb.com/products/compass) ao armazenar o Cosmos DB. Usaremos a API do Azure Cosmos DB para o MongoDB para este passo a passo. Para aqueles não familiarizados, o Compass é uma interface gráfica de usuário para MongoDB. É comumente usado para visualizar dados, executar consultas ad-hoc e gerenciar dados.

O Azure Cosmos DB é um serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e distribuição global no núcleo do Cosmos DB.

## <a name="pre-requisites"></a>Pré-requisitos

Para se conectar à sua conta do Cosmos DB usando o MongoDB Compass, você deve:

* Baixar e instalar [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Obtenha informações da [cadeia de conexão](connect-mongodb-account.md) do Cosmos DB

> [!NOTE]
> Atualmente, a API de Azure Cosmos DB para o servidor MongoDB versão 3,2 tem suporte com a versão mais recente da bússola do MongoDB. A bússola do MongoDB ainda não tem suporte com a versão do servidor 3,6. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Conectar-se à API do Azure Cosmos DB para MongoDB

Para conectar sua conta do Cosmos DB ao Compass, você pode seguir as etapas:

1. Recupere as informações de conexão da sua conta do Cosmos configurada com a API do Azure Cosmos DB para MongoDB usando as instruções que estão [aqui](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Captura de tela da folha de cadeia de conexão":::

2. Clique no botão que diz **Copiar para a Área de Transferência** ao lado de sua **Cadeia de conexão primária/secundária** no Cosmos DB. Clicar nesse botão copiará toda a cadeia de conexão para a área de transferência.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Captura de tela do botão Copiar para Área de Transferência":::

3. Abra o Compass na sua área de trabalho/computador e clique em **Conectar** e em **Conectar a...**.

4. O Compass irá detectar automaticamente uma cadeia de conexão na área de transferência, e irá pedir para perguntar se você deseja usar isso para se conectar. Clique em **Sim** conforme mostrado na captura de tela abaixo.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Captura de tela do prompt do Compass para conectar":::

5. Ao clicar em **Sim** na etapa anterior, os detalhes da cadeia de conexão serão preenchidos automaticamente. Remova o valor preenchido automaticamente a **nome do conjunto de réplica** campo para garantir que é deixado em branco.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Captura de tela do prompt do Compass para conectar":::

6. Na parte inferior da página, clique em **Conectar**. Sua conta e bancos de dados do Cosmos DB agora devem estar visíveis no MongoDB Compass.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
