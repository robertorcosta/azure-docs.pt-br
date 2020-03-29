---
title: Projeto Acústica Perguntas Frequentes
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas a perguntas frequentes sobre o Project Acoustics, incluindo instruções de download e processo de cozedura.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770196"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projeto Acústica Perguntas Frequentes

## <a name="what-is-project-acoustics"></a>O que é Projeto Acústico?

O project acoustics suite of plugins é um sistema acústico que calcula o comportamento de ondas sonoras antes do tempo de execução, semelhante à iluminação estática. A nuvem faz o trabalho pesado dos cálculos de física de onda, portanto, o custo da CPU de runtime é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso baixar o plug-in?

Você pode baixar o [plugin Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346) ou o [plugin Project Acoustics Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>O Project Acoustics suporta &lt;x&gt; plataforma?

O suporte à plataforma Project Acoustics evolui com base nas necessidades do cliente. Entre em contato conosco no fórum de edição do [Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues) para saber sobre o suporte para plataformas adicionais.

## <a name="is-azure-used-at-runtime"></a>O Azure é usado em runtime?

Não, a integração na nuvem é usada apenas durante o estágio de pré-computação que faz parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada da simulação é sua cena 3D, ambiente virtual ou nível do jogo. O Project Acoustics realiza simulações de ondas volumétricas em 3D que modelam a física do som de perto, incluindo a oclusão e o espalhamento suaves.
 
## <a name="what-is-the-runtime-cost"></a>O que é o custo de runtime?

Acústica leva aproximadamente 0,01% da CPU por origem por quadro. Uso de RAM depende do tamanho da cena e pode variar de 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Preciso simplificar a geometria do nível? Contagem do triângulo de controle? Verifique as malhas watertight?

Não. O sistema ingerirá geometria de nível detalhada diretamente. Será voxelizado para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que há na tabela de consulta de runtime?

O arquivo ACE inclui uma tabela de parâmetros acústicos entre numerosos pares de localização de origem e ouvinte, bem como geometria de cena voxelizada usada para interpolação de parâmetros.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>O Project Acoustics pode lidar com fontes móveis?

Sim, o Project Acoustics consulta a tabela de pesquisa e atualiza o DSP de áudio em cada tique-taque, para que ele possa lidar com fontes móveis e ouvinte.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>O Project Acoustics pode lidar com geometria dinâmica? Fechando portas? Paredes surpreso?

Não. Os parâmetros acústicos são pré-computados com base no estado estático de um nível de jogo. Sugerimos deixar a geometria da porta fora da acústica, e depois aplicar oclusão adicional com base no estado de objetos de jogo destrutíveis e móveis usando técnicas estabelecidas.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>O Project Acoustics usa materiais acústicos?

Sim. Os materiais são escolhidos a partir dos nomes dos materiais físicos em seu nível, impulsionando a absorção.
 
## <a name="what-do-the-probes-represent"></a>O que representam os "testes"?

As sondas são uma amostra dos possíveis locais dos jogadores. Cada sonda representa uma simulação de onda separada da cena originada no local da sonda. Em runtime, os parâmetros acústicos para o local do ouvinte são interpolados a partir dos locais de sonda próximos.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar muito computação na nuvem? O que isso me compra?

O Project Acoustics fornece parâmetros acústicos precisos e confiáveis, mesmo para ambientes virtuais ultra-complexos, levando em consideração todos os aspectos arquitetônicos. Fornece oclusão suave e obstrução e variação dinâmica de reverberação sem o trabalho manual de volumes de desenho. Tudo isso enquanto o restante de luz na CPU durante o runtime.

## <a name="what-exactly-happens-during-baking"></a>Exatamente, o que acontece durante "trazendo"?

Um cozimento consiste em simulações de ondas acústicas de regiões de simulação cubóide centradas em cada sonda ouvinte.

## <a name="is-my-source-content-secure"></a>Meu conteúdo de origem é seguro?

O Project Acoustics não envia a geometria da cena de origem para a nuvem. Em vez disso, a simulação opera em uma voxelização da sua cena, que é combinada com dados de localização da sonda e armazenada em um formato proprietário.     

## <a name="next-steps"></a>Próximas etapas
* Experimente o [conteúdo amostral do Project Acoustics Unity](unity-quickstart.md) ou [o conteúdo de amostra irreal](unreal-quickstart.md)

