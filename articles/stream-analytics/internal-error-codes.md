---
title: Solucionar problemas com códigos de erro do Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erro interno.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 2e101747322baac860cbde02aca341171aa0cc1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658354"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Códigos de erro interno do Azure Stream Analytics

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro interno. Os erros internos são genéricos, sendo gerados na plataforma Stream Analytics quando Stream Analytics não pode distinguir se o erro é um erro de Disponibilidade Interno ou um bug no sistema.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Causa**: O tamanho do lote usado para gravar no Cosmos DB é muito grande. 
* **Recomendação**: Tente novamente com um tamanho de lote menor.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)