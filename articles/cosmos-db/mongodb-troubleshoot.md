---
title: Solucionar erros comuns na API do Azure Cosmos DB para o Mongo DB
description: Este documento discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75941839"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB

Azure Cosmos DB implementa os protocolos de conexão de bancos de dados NoSQL comuns, incluindo o MongoDB. Devido à implementação do protocolo de conexão, você pode interagir de forma transparente com Azure Cosmos DB usando os SDKs, os drivers e as ferramentas do cliente existentes que funcionam com bancos de dados NoSQL. Azure Cosmos DB não usa nenhum código-fonte dos bancos de dados para fornecer APIs compatíveis com cabo para qualquer um dos bancos de dados NoSQL. Qualquer driver de cliente do MongoDB que entenda as versões de protocolo de conexão pode se conectar ao Azure Cosmos DB.

Embora a API do Azure Cosmos DB para MongoDB seja compatível com a versão 3,2 do protocolo de conexão do MongoDB (os operadores de consulta e os recursos adicionados na versão 3,4 estão atualmente disponíveis como uma visualização), há alguns códigos de erro personalizados que correspondem a Azure Cosmos DB erros específicos. Este artigo explica erros diferentes, códigos de erro e as etapas para resolver esses erros.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere dimensionar a taxa de transferência atribuída a um contêiner ou a um conjunto de contêineres do portal do Azure ou você pode repetir a operação. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| O caminho de índice correspondente ao item de ordem por especificado é excluído/a consulta order by não tem um índice composto correspondente do qual ele pode ser atendido. | 2 | A consulta solicita uma classificação em um campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de classificação que está sendo tentada. |
| Problemas de versão de fios do MongoDB | - | As versões mais antigas dos drivers do MongoDB não conseguem detectar o nome da conta do Azure Cosmos nas cadeias de conexão. | Acrescente *AppName = @**accountName** @ * no final da API da sua Cosmos DB para a cadeia de conexão do MongoDB, em que ***accountName*** é o nome da sua conta de Cosmos DB. |


## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

