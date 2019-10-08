---
title: Visão geral do Projeto Acústico
titlesuffix: Azure Cognitive Services
description: O Projeto Acústico é um mecanismo acústico para experiências interativas 3D, integrando simulação de ondas preparadas com controles de design interativo.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351139"
---
# <a name="what-is-project-acoustics"></a>O que é Projeto Acústico?
Projeto Acústico é um mecanismo de acústica de onda para experiências interativas 3D. Ele modela efeitos de onda como oclusão, obstrução, portaling e efeitos de reverberação em cenas complexas sem a necessidade de marcação de zona ou ray tracing com uso intensivo de CPU. Também inclui o mecanismo de jogo e a integração de áudio de middleware. A filosofia do Projeto Acústico é semelhante à da iluminação estática: fazer o bake da física detalhada offline para fornecer uma linha de base física e usar um runtime leve com controles de design expressivos para atender às suas metas artísticas com relação à acústica de seu mundo virtual.

![Captura de tela de Gears of War 4 mostrando voxels acústicos](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Usando física de onda para acústica interativa
Métodos com base em raio acústico podem verificar se houver oclusão usando uma conversão de raio fonte-ouvinte ou reverberação de unidade estimando o volume da cena local com alguns raios. Mas, essas técnicas podem não ser confiáveis porque uma pedra oclui tanto quanto um pedregulho. Os raios não contam pela forma como o som se inclina em torno dos objetos, um fenômeno conhecido como difração. A simulação do Projeto Acústico captura esses efeitos usando uma simulação baseada em onda. A acústica é mais previsível, precisa e contínua.

A inovação principal do Projeto Acústico é unir simulação acústica baseada em ondas sonoras reais com conceitos de design de som tradicionais. Ele converte os resultados da simulação em parâmetros DSP de áudio tradicional para oclusão, portaling e reverberação. O designer usa controles sobre esse processo de translação. Para obter mais detalhes sobre as principais tecnologias por trás do Projeto Acústico, visite a [página de pesquisa do projeto](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animação mostrando uma fatia horizontal 2D de propagação de onda por meio de uma cena](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Apresentação em vídeo do GDC 2019 (cerca de 30 min)
[![Vídeo do Projeto Acústico](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Clique para reproduzir o vídeo")

## <a name="setup"></a>Configuração
A [Integração do Unity do Projeto Acústico](unity-integration.md) é do tipo arrastar e soltar e inclui um plugin de mecanismo de áudio do Unity. Aumente os controles de fonte de áudio do Unity, anexando um componente dos controles C# do Projeto Acústico a cada objeto de áudio.

A [Integração do Unreal do Projeto Acústico](unreal-integration.md) inclui plugins de editor e de jogo para o Unreal e um plugin de mixer Wwise. Um componente de áudio personalizado estende funcionalidade familiar do Wwise dentro do Unreal com controles de design de acústica em tempo real. Os controles de design também são expostos no Wwise no plugin do mixer.

## <a name="workflow"></a>Fluxo de trabalho
* **Pré-bake:** Comece configurando o bake selecionando qual geometria responde à acústica, por exemplo, ignorando os faróis de luz. Em seguida, edite as atribuições de material automáticas e selecione as áreas de navegação para guiar a amostragem do ouvinte. Não há nenhuma marcação manual para zonas de reverberação/portal/sala.
* **Bake:** Uma etapa de análise é executada localmente, o que faz voxelização e outras análises geométricas na cena com base nas seleções acima. Os resultados são visualizados no editor para verificar a configuração da cena. No envio de bake, os dados de voxel são enviados para o Azure e você obtém de volta no ativo do jogo de acústica.
* **Tempo de execução:** Carregue o ativo em seu nível e você estará pronto para ouvir a acústica em seu nível. Projete a acústica em tempo real no editor usando os controles do código-fonte granulares. Os controles também podem ser obtidos a partir do nível de script.

## <a name="runtime-platforms"></a>Plataformas de tempo de execução
Os plug-ins do tempo de execução do Projeto Acústico podem atualmente ser implantados nas seguintes plataformas:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Plataformas do editor
O plug-in do editor de Projeto Acústico está disponível para as seguintes plataformas:
* Windows
* MacOS (somente Unity)

## <a name="download"></a>Baixar
* [Plug-in e amostras do Unity do Projeto Acústico](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Plug-ins e amostras do Unreal e do Wwise do Projeto Acústico](https://www.microsoft.com/download/details.aspx?id=58090)
  * Para binários do Xbox e outro suporte, contate-nos pelo [fórum](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Fale conosco
* [Discussão do Projeto Acústico e emissão de relatórios](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Próximas etapas
* Tente um [Início Rápido do Projeto Acústico para o Unit](unity-quickstart.md) ou para o [Unreal](unreal-quickstart.md)
* Explore a [filosofia de design de som do Projeto Acústico](design-process.md)

