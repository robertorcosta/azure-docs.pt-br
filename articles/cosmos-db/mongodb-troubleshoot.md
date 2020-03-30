---
title: Solucionando erros comuns na API do Azure Cosmos DB para Mongo DB
description: Este doc discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para o MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941839"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Soluciona problemas comuns na API do Azure Cosmos DB para MongoDB

O Azure Cosmos DB implementa os protocolos de fio de bancos de dados NoSQL comuns, incluindo o MongoDB. Devido à implementação do protocolo de fio, você pode interagir de forma transparente com o Azure Cosmos DB usando os SDKs, drivers e ferramentas existentes que funcionam com bancos de dados NoSQL. O Azure Cosmos DB não usa nenhum código fonte dos bancos de dados para fornecer APIs compatíveis com fio para qualquer um dos bancos de dados NoSQL. Qualquer driver cliente MongoDB que entenda as versões do protocolo de fio pode se conectar ao Azure Cosmos DB.

Embora a API do Azure Cosmos DB para MongoDB seja compatível com a versão 3.2 do protocolo de fio do MongoDB (os operadores de consulta e recursos adicionados na versão 3.4 estão atualmente disponíveis como uma visualização), existem alguns códigos de erro personalizados que correspondem ao Azure Cosmos DB erros específicos. Este artigo explica diferentes erros, códigos de erro e as etapas para resolver esses erros.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere escalar o throughput atribuído a um contêiner ou a um conjunto de contêineres do portal Azure ou você pode tentar novamente a operação. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| O caminho de índice correspondente ao item de ordem especificado é excluído / A ordem por consulta não tem um índice composto correspondente do que pode ser servido. | 2 | A consulta solicita uma espécie em um campo que não é indexado. | Crie um índice de correspondência (ou índice composto) para a consulta de classificação que está sendo tentada. |
| Problemas de versão de fios do MongoDB | - | As versões mais antigas dos drivers MongoDB não conseguem detectar o nome da conta Do Azure Cosmos nas strings de conexão. | Anexar *appName=@**contaNome** @ * no final da API do Seu Cosmos DB para seqüência de conexão MongoDB, onde ***accountName*** é o nome da sua conta Cosmos DB. |


## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

