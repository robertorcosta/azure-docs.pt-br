---
title: Controle de versão de modelo
titleSuffix: Azure Cognitive Services
description: Especificar versões de modelo nos pontos de extremidade V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089024"
---
A versão 3 da API de Análise de Texto permite escolher a versão do modelo mais atual para seus dados. Use o parâmetro opcional `model-version` para selecionar a versão do modelo desejado em suas solicitações. Se esse parâmetro não for especificado, a API usará como padrão a versão estável mais recente, `latest`. Embora você possa usar a versão de modelo mais recente em qualquer solicitação, apenas alguns recursos são atualizados em cada versão. A tabela a seguir descreve quais recursos foram atualizados em cada versão do modelo:

| Versão do modelo           | Recursos atualizados         | Última versão de:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Reconhecimento de entidade                      | Reconhecimento de entidade                      |
| `2019-10-01`            | Reconhecimento de entidade, Análise de sentimento  | Detecção de idioma, Extração de frases-chave e Análise de sentimento|


Cada resposta dos pontos de extremidade v3 inclui um campo `model-version` especificando a versão do modelo que foi usada.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Confira [Novidades](../whats-new.md) para obter detalhes sobre as atualizações dessas versões de modelo.
