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
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709138"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorias de entidade com suporte no API de Análise de Texto v3

Use este artigo para localizar as categorias de entidade que podem ser retornadas pelo [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md) (Ner). Uma visualização do NER v 3.1 também está disponível, o que inclui a capacidade de detectar informações pessoais ( `PII` ) e de integridade ( `PHI` ). Além disso, clique na guia **integridade** para ver uma lista de categorias com suporte no análise de texto para fins de integridade.

## <a name="entity-categories"></a>Categorias de entidade

#### <a name="general"></a>[Geral](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Saúde](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Próximas etapas

* [Como usar o reconhecimento de entidade nomeada no Análise de Texto](how-tos/text-analytics-how-to-entity-linking.md)
