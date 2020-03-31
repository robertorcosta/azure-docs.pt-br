---
title: Tutorial de design do Projeto Acústico do Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design do Projeto Acústico no Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854273"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Tutorial de design do Projeto Acústico do Unity
Este tutorial descreve as ferramentas de design e o fluxo de trabalho do Projeto Acústico no Unity.

Pré-requisitos:
* Unity 2018.2+ para Windows
* Uma cena do Unity com um ativo de bake da acústica

Para este tutorial, é possível obter uma cena do Unity com um ativo de bake da acústica de duas maneiras:
* [Adicionar o Projeto Acústico ao projeto do Unity](unity-integration.md), em seguida, [obter uma conta do Lote do Azure](create-azure-account.md) e [fazer o bake da cena do Unity](unity-baking.md)
* Ou, usar o [conteúdo de exemplo do Projeto Acústico](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Revise os conceitos do processo de design
O Projeto Acústico usa métodos comuns de DSP (processamento de sinal digital) de áudio para processar as fontes e fornece controle sobre as propriedades acústicas conhecidas, incluindo oclusão, combinação úmido/seco e comprimento da cauda de reverberação (RT60). Mas o principal [conceito do processo de design do Projeto Acústico](design-process.md) é que, em vez de configurar essas propriedades diretamente, você controla como os resultados da simulação são utilizados para direcionar essas propriedades. As configurações padrão para cada controle representam acústica materialmente precisa.

## <a name="design-acoustics-for-each-source"></a>Design acústico para cada fonte
O Projeto Acústico fornece vários controles de design acústico específicos da fonte. Isso permite controlar a composição em uma cena, enfatizando algumas fontes e deixando de enfatizar outras.

### <a name="adjust-distance-based-attenuation"></a>Ajustar a atenuação baseada em distância
O DSP de áudio fornecido pelo plug-in spatializer do Unity do **Projeto Acústico** respeita a atenuação baseada em distância por origem incorporada no Editor do Unity. Controles de atenuação de distância estão na **Audio Source** componente encontrado na **Inspetor** fontes de painel de som, em **configurações de som 3D**:

![Captura de tela do painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

A acústica executa o cálculo em uma caixa de “região da simulação” centralizada em torno do local do player. Se uma fonte de som estiver distante do player, localizada fora dessa região de simulação, somente a geometria dentro da caixa afetará a propagação do som (como causar oclusão), o que funciona razoavelmente bem quando os oclusores estão nas proximidades do player. No entanto, nos casos em que o player está em espaço aberto, mas os oclusores estão perto da fonte de som distante, o som pode passar por uma desoclusão irreal. A solução sugerida é garantir, em tais casos, que a atenuação do som caia para 0 a cerca de 45 m, a distância horizontal padrão do player em relação à borda da caixa.

![Captura de tela do painel de opções SpeakerMode do Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Ajustar oclusão e transmissão
Anexar o script **AcousticsAdjust** a uma fonte ativa os parâmetros de ajuste para essa origem. Para anexar o script, clique em **Add Component** na parte inferior do painel **Inspector** e navegue até **Scripts > Acoustics Adjust**. O script tem seis controles:

![Captura de tela do script AcousticsAdjust do Unity](media/acoustics-adjust.png)

* **Ativar Acústica** - Controla se a acústica é aplicada a essa fonte. Quando desmarcada, a origem será espacializada com HRTFs ou movimento panorâmico, mas não haverá nenhuma acústica. Isso significa que não há obstrução, oclusão ou parâmetros de reverberação dinâmica, como nível e tempo de decaimento. A reverberação ainda é aplicada com um nível fixo e tempo de decaimento.
* **Occlusion** – aplique um multiplicador ao nível de dB de oclusão calculado pelo sistema acústico. Se esse multiplicador for maior que 1, a oclusão será exagerada, enquanto valores menores que 1 tornam o efeito de oclusão mais sutil e um valor 0 desabilita a oclusão.
* **Transmission (dB)** – defina a atenuação (em dB) causada pela transmissão por meio de geometria. Defina esse controle deslizante para o nível mais baixo para desabilitar a transmissão. A acústica espacializa o áudio seco inicial ao chegar em torno da geometria da cena (portaling). A transmissão fornece uma chegada seca adicional que é espacializada na direção da linha de visão. A curva de atenuação de distância da fonte também é aplicada.

### <a name="adjust-reverberation"></a>Ajustar reverberação
* **Wetness (dB)** – ajusta a potência de reverberação, em dB, de acordo com a distância da fonte. Valores positivos emita um som mais reverberant, enquanto valores negativos emita um som mais dry. Clique no controle de curva (linha verde) para abrir o editor de curvas. Para modificar a curva, clique com o botão esquerdo do mouse para adicionar pontos e arraste-os para formar a função desejada. O eixo x é a distância da fonte e o eixo y é o ajuste de reverberação em dB. Para obter mais informações sobre como editar curvas, confira este [Manual do Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Para redefinir a curva ao padrão, clique com o botão direito do mouse em **Wetness** e selecione **Reset**.
* **Escala de tempo de decay** -ajusta um multiplicador para a hora de declínio. Por exemplo, se o resultado do coeficiente especificar um tempo de decaimento de 750 milissegundos, mas esse valor estiver definido como 1,5, o tempo de decaimento aplicado à fonte será de 1.125 milissegundos.
* **Outdoorness** – um ajuste complementar à estimativa do sistema acústico sobre o modo como a reverberação em uma fonte soa “ao ar livre”. Definir esse valor para 1 fará com que uma fonte sempre soe como ao ar livre, enquanto a definição para -1 fará com que a fonte soe completamente como interna.

Anexe o script **AcousticsAdjustExperimental** a uma fonte ativa parâmetros adicionais de ajuste experimental para essa fonte. Para anexar o script, clique em **Add Component** na parte inferior do painel **Inspector** e navegue até **Scripts > Acoustics Adjust Experimental**. Atualmente, há um controle experimental:

![Captura de tela do script AcousticsAdjustExperimental do Unity](media/acoustics-adjust-experimental.png)

* **Perceptual Distance Warp** – aplique uma distorção exponencial à distância usada para calcular a relação seco-úmido. O sistema acústico calcula os níveis de umidade em todo o espaço, que variam levemente com a distância e fornecem pistas de distância perceptiva. Valores de distorção maiores que 1 exageram esse efeito, aumentando os níveis de reverberação relacionada à distância e tornando o som "distante". Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil, tornando o som mais "presente".

## <a name="design-acoustics-for-all-sources"></a>Design acústico para todas as fontes
Para ajustar os parâmetros de todas as fontes, clique na faixa do canal no **Mixer de Áudio** do Unity e ajuste os parâmetros no efeito **Mixer do Projeto Acústico**.

![Captura de tela do painel de personalização Mixer do Unity do Projeto Acústico](media/mixer-parameters.png)

* **Ajuste de Umidade** – ajusta o poder de reverberação, em dB, em todas as origens na cena com base na distância entre o ouvinte e a origem. Valores positivos emita um som mais reverberant, enquanto valores negativos emita um som mais dry.
* **Escala de RT60** – escalar multiplicativo para tempo de reverberação.
* **Usar o Balanço** – controla se o áudio será gerado como binaural (0) ou balanço multicanal (1). Qualquer valor além de 1 indica binaural. A saída binaural é espacializada com HRTFs para uso com fones de ouvido, enquanto a saída multicanal é espacializada com VBAP para uso com sistemas de alto-falantes surround multicanais. Se estiver usando o panner multicanal, não deixe de selecionar o modo de alto-falante que corresponde às configurações do dispositivo, encontradas em **Configurações do Projeto** > **Áudio**.

## <a name="check-proper-sound-source-placement"></a>Verifique o posicionamento correto da fonte de som
Fontes de som colocadas dentro de voxels ocupados não receberão tratamento acústico. Como os voxels se estendem além da geometria de cena visível, será possível colocar uma fonte dentro de um voxel enquanto não tiver sido fechado pela geometria visual. É possível exibir os voxels do Projeto Acústico, ativando a caixa de seleção de grade de voxel no menu **Gizmos**, no canto superior direito da exibição da **Cena**.

![Captura de tela do menu Gizmos do Unity](media/gizmos-menu.png)  

A exibição do voxel também pode ajudar a determinar se os componentes visuais do jogo têm uma transformação aplicada a eles. Em caso afirmativo, aplique a mesma transformação ao GameObject que hospeda o **Acoustics Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Tempo de bake vs. voxels de tempo de execução
É possível exibir voxels na janela do editor no momento do design do jogo e na janela do jogo em runtime. O tamanho dos voxels é diferente nessas exibições. Isso ocorre porque a interpolação de runtime acústica usa uma grade de voxel mais fina para resultados de interpolação mais suaves. O posicionamento da fonte de som deve ser verificado usando os voxels de runtime.

Voxels de tempo de design:

![Captura de tela dos voxels do Projeto Acústico durante o tempo de design](media/voxels-design-time.png)

Voxels de runtime:

![Captura de tela dos voxels do Projeto Acústico durante o tempo de execução](media/voxels-runtime.png)

## <a name="next-steps"></a>Próximas etapas
* Explore estudos de caso destacando os conceitos relacionados ao[processo de design](design-process.md)

