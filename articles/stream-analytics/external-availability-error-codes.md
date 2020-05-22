---
title: Códigos de erro de disponibilidade externa - Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erro de disponibilidade externa.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 092b548e73330fe80ef10b2e25d9ab7883b256ac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595195"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Códigos de erro de disponibilidade externa do Azure Stream Analytics

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de disponibilidade externa. Os erros de disponibilidade externa ocorrem quando um serviço dependente está indisponível.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Causa**: Um serviço está temporariamente indisponível.
* **Recomendação**: O Stream Analytics continuará tentando acessar o serviço. 

## <a name="kafkaservernotavailable"></a>KafkaServerNotAvailable

* **Causa**: O servidor Kafka não está disponível.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa**: O Stream Analytics encontrou um erro ao se comunicar com o EventHub. 


## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
