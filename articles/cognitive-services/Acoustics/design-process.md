---
title: Conceitos de design com simulação de acústica
titlesuffix: Azure Cognitive Services
description: Esta visão conceitual explica como o Project Acoustics incorpora a simulação acústica ao processo de design de som.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854301"
---
# <a name="project-acoustics-design-process-concepts"></a>Conceitos de processo de design de acústica do projeto

Esta visão conceitual explica como o Project Acoustics incorpora a simulação acústica física no processo de design de som.

## <a name="sound-design-with-audio-dsp-parameters"></a>Design de som com parâmetros de DSP de áudio

Títulos interativos 3D alcançam seu som particular usando blocos de processamento de sinal digital de áudio (DSP) hospedados em um mecanismo de áudio. Esses blocos variam em complexidade desde a simples mistura, até a reverberação, eco, atraso, equalização, compressão e limitação, entre outros efeitos. Selecionar, organizar e definir parâmetros sobre esses efeitos é responsabilidade do designer de som, que constrói um gráfico de áudio que atinja os objetivos estéticos e de jogabilidade da experiência.

Em um título interativo, à medida que os sons e ouvintes se movem pelo espaço 3D, como esses parâmetros se adaptam às condições de mudança? O designer de som muitas vezes organizará volumes em todo o espaço que são programados para desencadear mudanças de parâmetros para alcançar mudanças nos efeitos de reverberação, por exemplo, ou para pato sons na mistura à medida que o ouvinte se move de uma parte da cena para outra. Sistemas acústicos também estão disponíveis que podem automatizar alguns desses efeitos.

Títulos 3D usam sistemas de iluminação e física cinemática que são motivados pela física, mas ajustados por designers para alcançar uma mistura de imersão e objetivos de jogabilidade. Um designer visual não define valores individuais de pixels, mas ajusta modelos 3D, materiais e sistemas de transporte leve que são todos fisicamente baseados para negociar a estética visual e os custos da CPU. Qual seria o processo equivalente para áudio? O Projeto Acústica é um primeiro passo na exploração dessa questão. Primeiro vamos tocar no que significa transportar energia acústica através de um espaço.

![Captura de tela da cena AltSpace sobreposta com zonas de reverb](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Respostas de impulso: Conexão acústica de dois pontos no espaço

Se você está familiarizado com o design de áudio, você pode estar familiarizado com respostas de impulso acústico. Uma resposta de impulso acústico modela o transporte de um som de uma fonte para um ouvinte. Portanto, uma resposta de impulso pode capturar todos os efeitos interessantes da acústica da sala, como oclusão e reverberação. As respostas de impulso também têm certas propriedades poderosas que permitem que os efeitos de DSP de áudio sejam dimensionados. Adicionar dois sinais de áudio juntos e processar com uma resposta de impulso dá o mesmo resultado que aplicar a resposta de impulso separadamente a cada sinal e adicionar os resultados. A propagação acústica e as respostas por impulso também não dependem do áudio que está sendo processado, apenas da cena que está sendo modelada, e dos locais de origem e ouvinte. Em suma, uma resposta de impulso destila o efeito da cena na propagação do som.

Uma resposta de impulso captura todos os efeitos acústicos interessantes da sala, e podemos aplicá-lo ao áudio eficientemente com um filtro, e podemos obter respostas de impulso a partir de medição ou simulação. Mas e se não quisermos que a acústica combine exatamente com a física, mas a molde para combinar com as exigências emocionais de uma cena? Mas, assim como os valores de pixel, uma resposta por impulso é apenas uma lista de milhares de números, como podemos ajustá-lo para atender às necessidades estéticas? E se quisermos ter oclusão/obstrução que varie suavemente ao passar pelas portas ou atrás de obstáculos, quantas respostas de impulso precisamos para obter um efeito suave? E se a fonte se mover rápido? Como interpolamos?

Parece difícil usar respostas de simulação e impulso para alguns aspectos da acústica em títulos interativos. Mas ainda podemos construir um sistema de transporte de áudio que suporta ajustes de designer se pudermos conectar nossas respostas de impulso a partir de simulação com nossos parâmetros familiares de efeito DSP de áudio.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Conectando simulação a DSP de áudio com parâmetros

Uma resposta de impulso contém todos os efeitos acústicos interessantes (e desinteressantes). Os blocos dsp de áudio, quando seus parâmetros são definidos corretamente, podem render um efeito acústico interessante. Usar a simulação acústica para conduzir um bloco DSP de áudio para automatizar o transporte de áudio em uma cena 3D é apenas uma questão de medir os parâmetros de DSP de áudio a partir de uma resposta de impulso. Esta medida é bem compreendida para certos efeitos acústicos comuns e importantes, incluindo oclusão, obstrução, portalling e reverberação.

Mas se a simulação estiver conectada diretamente aos parâmetros do DSP de áudio, onde está o ajuste do designer? O que ganhamos? Bem, nós ganhamos uma quantidade significativa de memória de volta descartando respostas de impulso e retendo alguns parâmetros DSP. E para dar ao designer algum poder sobre o resultado final, só precisamos encontrar uma maneira de inserir o designer entre a simulação e o DSP de áudio.

![Gráfico com resposta de impulso estilizada com parâmetros sobrepostos](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Design de som transformando parâmetros de DSP de áudio a partir de simulação

Considere o efeito que seus óculos de sol têm na sua visão do mundo. Em um dia brilhante, os óculos podem reduzir o brilho a algo mais confortável. Em uma sala escura, você pode não ser capaz de ver nada. Os óculos não definem um certo nível de brilho em todas as situações; eles só fazem tudo mais escuro.

Se usarmos a simulação para conduzir nosso DSP de áudio usando parâmetros de oclusão e reverberação, podemos adicionar um filtro após o simulador para ajustar os parâmetros que o DSP 'vê'. O filtro não imporia um certo nível de oclusão ou reverb comprimento da cauda, assim como os óculos de sol não fazem de todos os cômodos o mesmo brilho. O filtro pode fazer com que cada ocupante oclua menos. Ou ocluir mais. Adicionando e ajustando um filtro de parâmetro de oclusão 'escurecido', salas grandes e abertas ainda teriam pouco ou nenhum efeito de oclusão, enquanto as portas aumentariam de um efeito médio para um forte efeito de oclusão, mantendo a suavidade nas transições de efeito que a simulação fornece.

Nesse paradigma, a tarefa do designer muda desde a escolha de parâmetros acústicos para cada situação, até a seleção e ajuste de filtros para aplicar aos parâmetros mais importantes de DSP provenientes da simulação. Eleva as atividades do designer das preocupações estreitas de estabelecer transições suaves para as maiores preocupações da intensidade dos efeitos de oclusão e reverberação e da presença de fontes na mistura. Claro que, quando a situação exige, um filtro sempre disponível é simplesmente voltar a escolher os parâmetros de DSP para uma fonte específica em uma situação específica.

## <a name="sound-design-in-project-acoustics"></a>Design de som em Project Acoustics

O pacote Project Acoustics integra cada um dos componentes descritos acima: um simulador, um codificador que extrai parâmetros e constrói o ativo acústico, dsp de áudio e uma seleção de filtros. O design de som com o Project Acoustics implica a escolha de parâmetros para os filtros que ajustam os parâmetros de oclusão e reverberação derivados da simulação e aplicados ao DSP de áudio, com controles dinâmicos expostos dentro do editor do jogo e do motor de áudio.

## <a name="next-steps"></a>Próximas etapas
* Experimente o paradigma de design usando o [Project Acoustics quickstart for Unity](unity-quickstart.md) ou o Project [Acoustics quickstart for Unreal](unreal-quickstart.md)
* Explore os [controles de design do Projeto Acústica para a Unidade](unity-workflow.md) ou os controles de design do Projeto Acústica para a [Unreal](unreal-workflow.md)

