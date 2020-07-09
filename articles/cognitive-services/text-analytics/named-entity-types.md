---
title: Categorias com suporte para reconhecimento de entidade nomeada
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as categorias de entidade com suporte no API de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: e36a69be19844a75562f87d3c195494e3ef148a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108474"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorias de entidade com suporte no API de Análise de Texto v3

Use este artigo para localizar as categorias de entidade que podem ser retornadas pelo [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md) (Ner). Uma visualização do NER v 3.1 também está disponível, o que inclui a capacidade de detectar informações pessoais ( `PII` ) e de integridade ( `PHI` ). Além disso, clique na guia **integridade** para ver uma lista de categorias com suporte no análise de texto para fins de integridade.

## <a name="entity-categories"></a>Categorias de entidade

#### <a name="general"></a>[Geral](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Pessoal](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Saúde](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Próximas etapas

* [Como usar o reconhecimento de entidade nomeada no Análise de Texto](how-tos/text-analytics-how-to-entity-linking.md)
