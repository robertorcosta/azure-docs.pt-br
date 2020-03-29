---
title: Projeto Resolução de Bolos Acústicos
titlesuffix: Azure Cognitive Services
description: Esta visão conceitual descreve a diferença entre resoluções grosseiras e finas durante a cozimento da acústica.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854360"
---
# <a name="project-acoustics-bake-resolution"></a>Projeto Resolução de Bolos Acústicos
Esta visão conceitual descreve a diferença entre resoluções grosseiras e finas durante a cozimento da acústica. Você escolhe esta configuração durante a etapa Sondas do fluxo de trabalho de cozimento.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Resolução grossa vs fina

A única diferença entre as configurações de resolução grossa e fina é a frequência com que a simulação é executada. A fina usa uma frequência duas vezes mais alta que a grossa. Isso tem uma série de implicações na simulação acústica:

* O comprimento de onda para a grossa é duas vezes mais longo que a fina e, portanto, os voxels são duas vezes maiores.
* O tempo de simulação está diretamente relacionado ao tamanho do voxel, fazendo um bake grosso aproximadamente 16 vezes mais rápido que um bake fino.
* Portais (por exemplo, portas ou janelas) menores do que o tamanho do voxel não podem ser simulados. A configuração grosseira pode fazer com que alguns desses portais menores não sejam simulados; portanto, eles não vão passar o som através do tempo de execução. Você pode ver se isso está acontecendo, visualizando os voxels.
* A menor frequência de simulação resulta em menos difração ao redor dos cantos e bordas.
* Fontes sonoras não podem ser localizadas dentro de voxels "preenchidos" (ou seja, voxels que contêm geometria). Isso não resulta em nenhum som. É mais difícil colocar fontes de som para que não estejam dentro dos voxels maiores de grosso do que é ao usar a configuração fina.
* Os voxels maiores se intrometem mais nos portais, como mostrado abaixo. A primeira imagem foi criada usando resolução grossa, enquanto a segunda é a mesma porta usando resolução fina. Como indicado pelas marcas vermelhas, há muito menos intrusão na entrada usando a configuração fina. A linha azul é a porta conforme definida pela geometria, enquanto a linha vermelha é o portal acústico efetivo definido pelo tamanho do voxel. Como essa intrusão ocorre em uma determinada situação depende completamente de como os voxels se alinham com a geometria do portal, que é determinada pelo tamanho e localização de seus objetos na cena.

![Captura de tela de voxels grossos preenchendo uma porta de entrada no Unreal](media/unreal-coarse-bake.png)

![Captura de tela de voxels finos preenchendo uma porta de entrada no Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Próximas etapas

Experimente as configurações de resolução grosseiras e finas você mesmo usando nossos plugins [Unreal](unreal-baking.md) ou [Unity.](unity-baking.md)
