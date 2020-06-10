---
title: Conectar-se ao Link do Azure Synapse (versão prévia) para o Azure Cosmos DB
description: Como conectar um Azure Cosmos DB a um workspace do Synapse com o Link do Azure Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 32087b1b2340508f7f868455e2db476046eaa4b5
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235263"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Conectar-se ao Link do Azure Synapse (versão prévia) para o Azure Cosmos DB

Este artigo descreve como acessar um banco de dados Azure Cosmos DB pelo Azure Synapse Analytics Studio com o Link do Azure Synapse.

## <a name="prerequisites"></a>Pré-requisitos

Antes de conectar um banco de dados Azure Cosmos DB ao workspace, você precisará do seguinte:

> [!IMPORTANT]
> O Link do Azure Synapse para o Azure Cosmos DB é compatível atualmente com workspaces que não têm uma rede virtual gerenciada habilitada. 

* Banco de dados do Azure Cosmos DB existente ou criar uma nova conta após este [início rápido](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Espaço de trabalho Synapse existente ou criar um novo espaço de trabalho após este [início rápido](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Habilitar o repositório analítico do Azure Cosmos DB

Para executar a análise em larga escala no Azure Cosmos DB sem afetar o desempenho operacional, recomendamos habilitar o Link do Synapse para o Azure Cosmos DB. O Link do Synapse traz o recurso HTAP para um contêiner e suporte interno no Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Navegar até o Synapse Studio

Em seu espaço de trabalho do Synapse, selecione **Inicializar Synapse Studio**. Na home page do Synapse Studio, selecione **Dados, o que o levará para o **Pesquisador de Objetos de Dados**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Conectar um banco de dados Azure Cosmos DB a um espaço de trabalho Synapse

A conexão de um banco de dados Azure Cosmos DB é feita como um serviço vinculado. Um serviço vinculado do Azure Cosmos DB permite que os usuários procurem e explorem dados, leiam e gravem do Apache Spark para Azure Synapse Analytics ou SQL no Azure Cosmos DB.

No Pesquisador de Objetos de Dados, você pode se conectar diretamente a um banco de dados Azure Cosmos DB executando as seguintes etapas:

1. Selecione o ícone ***+*** ao lado de Dados.
2. Selecione **Conectar a dados externos**
3. Selecione a API à qual deseja se conectar: API do SQL ou API para MongoDB
4. Selecione ***Continuar***
5. Nomeie o serviço vinculado. O nome será exibido no Pesquisador de Objetos e usado pelos tempos de execução do Synapse para conectar ao banco de dados e aos contêineres. Recomendamos usar um nome amigável.
6. Selecione o **nome da conta do Azure Cosmos DB** e o **nome do banco de dados**.
7. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução do Synapse serão roteadas para a região mais próxima na qual o repositório analítico estiver habilitado. No entanto, você pode definir manualmente em qual região deseja que os usuários acessem o repositório analítico do Azure Cosmos DB. Selecione **Propriedades de conexão adicionais** e, em seguida, **Novo**. Em **Nome da Propriedade**, grave as ***Regiões Preferidas*** e defina o **Valor** para a região desejada (exemplo: WestUS2, não há espaço entre palavras e números).
8. Escolha ***Criar***

Os bancos de dados Azure Cosmos DB estão na guia **Vinculado**, na seção Azure Cosmos DB. Com o Azure Cosmos DB, você pode diferenciar um contêiner habilitado para HTAP de um contêiner somente OLTP pelos seguintes ícones:

**Contêiner somente OLTP**:

![Contêiner OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Contêiner habilitado para HTAP**:

![Contêiner HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagir rapidamente com ações geradas por código

Ao clicar com o botão direito em um contêiner, você terá uma lista de gestos que dispararão um tempo de execução do Spark ou do SQL. A gravação em um contêiner ocorrerá por meio do repositório transacional do Azure Cosmos DB e consumirá Unidades de Solicitação.  

## <a name="next-steps"></a>Próximas etapas

* [Saiba o que é compatível entre o Synapse e o Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar o repositório analítico com o Spark](./how-to-query-analytical-store-spark.md)