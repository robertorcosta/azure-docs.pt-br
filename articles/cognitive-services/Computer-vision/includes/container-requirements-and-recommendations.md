---
title: Recomendações e requisitos do contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397174"
---
> [!NOTE]
> Os requisitos e as recomendações são baseados em parâmetros de comparação com uma solicitação por segundo, usando uma imagem de 8 MB de uma carta empresarial digitalizada que contém 29 linhas e 803 caracteres no total.

A tabela a seguir descreverá a alocação mínima e recomendada de recursos para cada contêiner de Leitura.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Leia 2,0-visualização | 1 núcleo, 8 GB de memória |  8 núcleos, 16 GB de memória |
| Leitura 3.0 – versão prévia | 8 núcleos, 16 GB de memória | 8 núcleos, 24 GB de memória |
| Leitura 3.1 – versão prévia | 8 núcleos, 16 GB de memória | 8 núcleos, 24 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
