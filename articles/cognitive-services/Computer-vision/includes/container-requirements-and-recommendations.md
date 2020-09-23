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
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982065"
---
> [!NOTE]
> Os requisitos e as recomendações baseiam-se em benchmarks com uma única solicitação por segundo, usando uma imagem de 8 MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de leitura.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Leia 3,0-visualização | 8 núcleos, 16 GB de memória | 8 núcleos, 24 GB de memória |
| Leia 3,1-visualização | 8 núcleos, 16 GB de memória | 8 núcleos, 24 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
