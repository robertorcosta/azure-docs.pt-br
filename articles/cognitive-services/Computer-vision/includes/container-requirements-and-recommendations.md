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
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877960"
---
> [!NOTE]
> Os requisitos e as recomendações baseiam-se em benchmarks com uma única solicitação por segundo, usando uma imagem de 8 MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de leitura.

| Contêiner | Mínimo | Recomendadas |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
| Ler | 1 núcleos, 8 GB de memória, 0,24 TPS | 8 núcleos, 16 GB de memória, 1,17 TPS | 0,24, 1,17 |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS-transações por segundo.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
