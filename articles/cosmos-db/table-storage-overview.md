---
title: Visão geral do armazenamento de Tabelas do Azure
description: Aprenda a usar o armazenamento de Tabelas do Azure para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos Web, catálogos de endereços, informações sobre dispositivos ou outros tipos de metadados.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 7727dd2ae69fab75219c5d6d733db3f150061df3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477225"
---
# <a name="azure-table-storage-overview"></a>Visão geral do armazenamento de tabelas

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

O Armazenamento de Tabelas do Azure é um serviço que armazena dados NoSQL estruturados na nuvem, fornecendo um repositório chave/atributo com um design sem esquema. Como o armazenamento de Tabelas não tem um esquema, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. O acesso aos dados do Armazenamento de Tabelas é rápido e econômico para muitos tipos de aplicativos e normalmente tem um custo mais baixo que o SQL tradicional para volumes de dados semelhantes.

Você pode usar o armazenamento de tabelas para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos ou outros tipos de metadados exigidos pelo serviço. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Próximas etapas

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.

* [Introdução à API de Tabela do Azure Cosmos DB e armazenamento de Tabelas do Azure usando SDK do .NET](./tutorial-develop-table-dotnet.md)

* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:

    * [Referência à Biblioteca de Cliente de Armazenamento para .NET](/dotnet/api/overview/azure/storage)

    * [Referência da API REST](/rest/api/storageservices/)