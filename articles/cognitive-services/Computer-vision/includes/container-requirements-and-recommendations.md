---
title: Recomendações e requisitos do contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481760"
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
