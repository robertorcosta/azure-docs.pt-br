---
title: Solucionar erros comuns na API do Azure Cosmos DB para o Mongo DB
description: Este documento discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: fa33e2ccc5c6cca94ab4e2294a4865745145c1ce
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096318"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O artigo a seguir descreve os erros e soluções comuns para bancos de dados usando a API de Azure Cosmos DB para MongoDB.

>[!Note]
> Azure Cosmos DB não hospeda o mecanismo do MongoDB. Ele fornece uma implementação do protocolo de [transmissão do MongoDB versão 3,6](mongodb-feature-support-36.md) e suporte herdado para o [protocolo de conexão versão 3,2](mongodb-feature-support.md), portanto, alguns desses erros só são encontrados na API do Azure Cosmos DB para MongoDB. 

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | A solicitação excedeu o tempo limite de 60 segundos de execução. | Pode haver muitas causas para esse erro. Uma das causas é quando a capacidade das unidades de solicitação alocadas atualmente não é suficiente para concluir a solicitação. Isso pode ser resolvido aumentando as unidades de solicitação da coleção ou do banco de dados. Em outros casos, esse erro pode ser solucionado por meio da divisão de uma solicitação grande em partes menores. |
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere a possibilidade de dimensionar a taxa de transferência atribuída a um contêiner ou um conjunto de contêineres do portal do Azure ou tentar a operação novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| O caminho de índice correspondente ao item order-by especificado é excluído/A consulta order-by não tem um índice composto correspondente do qual ele pode ser atendido. | 2 | A consulta solicita uma classificação em um campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de classificação que está sendo tentada. |
| Problemas de versão de fios do MongoDB | - | As versões mais antigas dos drivers do MongoDB não conseguem detectar o nome da conta do Azure Cosmos nas cadeias de conexão. | Acrescente *AppName = @ **accountName** @* no final da API da sua Cosmos DB para a cadeia de conexão do MongoDB, em que ***accountName*** é o nome da sua conta de Cosmos DB. |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

