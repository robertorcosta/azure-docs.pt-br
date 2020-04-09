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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877960"
---
> [!NOTE]
> Os requisitos e recomendações são baseados em benchmarks com uma única solicitação por segundo, usando uma imagem de 8 MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner Read.

| Contêiner | Mínimo | Recomendadas |Tps<br>(Mínimo, Máximo)|
|-----------|---------|-------------|--|
| Ler | 1 núcleos, memória de 8 GB, 0,24 TPS | 8 núcleos, memória de 16 GB, 1,17 TPS | 0.24, 1.17 |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
