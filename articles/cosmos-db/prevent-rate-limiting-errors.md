---
title: Evitar erros de limitação de taxa para as operações Azure Cosmos DB API para MongoDB.
description: Saiba como impedir que seu Azure Cosmos DB API para operações do MongoDB atinjam erros de limitação de taxa com o recurso SSR (repetição do servidor).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540480"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Evitar erros de limitação de taxa para as operações Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

As operações Azure Cosmos DB API para MongoDB podem falhar com erros de limitação de taxa (16500/429) se excederem o limite de taxa de transferência (RUs) da coleção. 

Você pode habilitar o recurso de repetição do lado do servidor (SSR) e permitir que o servidor Repita essas operações automaticamente. As solicitações são repetidas após um pequeno atraso para todas as coleções em sua conta. Esse recurso é uma alternativa conveniente ao tratamento de erros de limitação de taxa no aplicativo cliente.


## <a name="use-the-azure-portal"></a>Usar o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta da API do Azure Cosmos DB para MongoDB.

1. Vá para o painel **recursos** abaixo da seção **configurações** .

1. Selecione **nova tentativa do servidor**.

1. Clique em **habilitar** para habilitar esse recurso para todas as coleções em sua conta.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Captura de tela do recurso de repetição do lado do servidor para Azure Cosmos DB API para MongoDB":::


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como solucionar erros comuns, consulte este artigo:

* [Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB](mongodb-troubleshoot.md)
