---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505824"
---
Os enunciados de exemplo seguem um formato específico. 

O campo `text` contém o texto do enunciado de exemplo. O campo `intentName` precisa corresponder ao nome de uma intenção existente no aplicativo do LUIS. O campo `entityLabels` é obrigatório. Se você não quiser rotular as entidades, forneça uma matriz vazia.

Se a matriz entityLabels não estiver vazia, `startCharIndex` e `endCharIndex` precisarão marcar a entidade referenciada no campo `entityName`. O índice é baseado em zero, o que significa que o 6 no exemplo superior se refere ao "S" de Seattle e não ao espaço antes da letra maiúscula S. Se você iniciar ou terminar o rótulo com um espaço no texto, a chamada à API para adicionar os enunciados falhará.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
