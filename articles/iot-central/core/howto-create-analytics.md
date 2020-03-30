---
title: Analise os dados do dispositivo em seu aplicativo Azure IoT Central | Microsoft Docs
description: Analise os dados do dispositivo no aplicativo Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158275"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Como usar análises para analisar dados de dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*



O Azure IoT Central oferece recursos de análise ricos para analisar tendências históricas e correlacionar várias telemetrias de seus dispositivos. Para começar, visite **Analytics** no painel esquerdo.

## <a name="understanding-the-analytics-ui"></a>Entendendo a ui analytics
A interface de usuário do Analytics é feita de três componentes principais:
- **Painel de configuração de dados:** No painel de configuração, comece selecionando o grupo do dispositivo para o qual deseja analisar os dados. Em seguida, selecione a telemetria que deseja analisar e selecione o método de agregação para cada telemetria. **Dividir pelo** controle ajuda a agrupar os dados usando as propriedades do dispositivo como dimensões.

- **Controle de tempo:** O controle de tempo é usado para selecionar a duração para a qual você deseja analisar os dados. Você pode arrastar qualquer controle deslizante de extremidade do tempo para selecionar o período de tempo. O controle de tempo também tem um controle deslizante de **tamanho de intervalo** que controla o balde ou o tamanho do intervalo usado para agregar os dados. 

- **Controle de gráficos:** O controle de gráficos visualiza os dados como um gráfico de linha. Você pode alternar a visibilidade de linhas específicas interagindo com a legenda do gráfico. 


  ![Visão geral da ia de análise](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Consultando seus dados

Você precisará começar escolhendo um **grupo de dispositivos**e a telemetria que deseja analisar. Depois de terminar, selecione **Analisar** para começar a visualizar seus dados.

- **Grupo de dispositivos:** Um [grupo de dispositivos](tutorial-use-device-groups.md) é um grupo definido pelo usuário de seus dispositivos. Por exemplo, todas as geladeiras em Oakland, ou todas as turbinas eólicas versão 2.0.

- **Telemetria:** Selecione a telemetria que deseja analisar e explorar. Você pode selecionar várias telemetrias para analisar em conjunto. O método de agregação padrão é definido como Média para numérico e contagem para tipo de dados de seqüência, respectivamente. Os métodos de agregação suportados para tipos de dados numéricos são Médio, Máximo, Mínimo, Contagem e, Soma.  Os métodos de agregação suportados para o tipo de dados de seqüência são contados.

- **Dividido por:** O controle 'Split by' ajuda a agrupar os dados usando as propriedades do dispositivo como dimensões. Os valores do dispositivo e das propriedades da nuvem são unidos junto com a telemetria como e quando é enviada pelo dispositivo. Se a propriedade nuvem ou dispositivo tiver sido atualizada, então você verá a telemetria agrupada por valores diferentes no gráfico.

    > [!TIP]
    > Para exibir dados de cada dispositivo separadamente, selecione ID do dispositivo no controle 'Dividir por'.

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Uma vez que você tenha consultado seus dados, você pode começar a visualizá-los no gráfico de linha. Você pode mostrar/ocultar a telemetria, alterar a duração do tempo, exibir a telemetria em uma grade de dados.

- **Painel do editor de tempo:** Por padrão, vamos recuperar dados do passado um dia. Você pode arrastar qualquer controle deslizante de extremidade do tempo para alterar a duração do tempo. Você também pode usar o controle de calendário para selecionar um dos baldes de tempo predefinidos ou selecionar um intervalo de tempo personalizado. O controle de tempo também tem um controle deslizante de **tamanho de intervalo** que controla o balde ou o tamanho do intervalo usado para agregar os dados.

    ![Editor de tempo](media/howto-create-analytics/timeeditorpanel.png)

    - **Ferramenta de controle deslizante de intervalo de data interna**: Use os dois controles de ponto final arrastando-os ao longo do período de tempo que você deseja. Este intervalo de data sumido interno é limitado pelo controle de controle deslizante de faixa de data externa.
    
   
    - **Controle deslizante de faixa de data externa :** Use os controles de ponto final para selecionar o intervalo de datas externas, que estará disponível para o controle interno do intervalo de datas.

    - **Aumentar e diminuir os botões de intervalo de data**: Aumente ou diminua o tempo selecionando qualquer botão para o intervalo desejado.

    - Controle deslizante de tamanho de intervalo : **Use-o**para aumentar e sair de intervalos ao longo do mesmo período de tempo. Essa ação fornece controle mais preciso dos movimentos entre fatias de tempo grandes. Você pode usá-lo para ver vistas granulares e de alta resolução de seus dados, mesmo que até milissegundos. O ponto de partida padrão do controle deslizante é definido como a visão mais ideal dos dados da sua seleção, que equilibra resolução, velocidade de consulta e granularidade.
    
    - **Seletor de faixas de**data : Com este controle web, você pode selecionar facilmente as datas e intervalos de tempo desejadas. Você também pode usar o controle para alternar entre fusos horários diferentes. Depois de fazer as alterações para aplicar ao seu espaço de trabalho atual, selecione Salvar.

    > [!TIP]
    > O tamanho do intervalo é determinado dinamicamente com base no período de tempo selecionado. Intervalos de tempo menores permitirão agregar os dados em intervalos muito granulares de até alguns segundos.


- **Legenda do gráfico:** A lenda do gráfico mostra a telemetria selecionada no gráfico. Você pode passar o tempo sobre cada item da legenda para trazê-lo em foco no gráfico. Ao usar 'Split By', a telemetria é agrupada pelos respectivos valores da dimensão selecionada. Você pode alternar a visibilidade de cada telemetria específica ou de todo o grupo clicando no nome do grupo.  


- **Controle do formato do eixo Y:** o modo de eixo y é ciclos através das opções de exibição do eixo y disponível. Este controle só está disponível quando diferentes telemetrias estão sendo visualizadas. Você pode definir o eixo y escolhendo entre um dos três modos:

    - **Empilhados:** Um gráfico para cada telemetria é empilhado e cada um dos gráficos tem seu próprio eixo y. Este modo é definido como padrão.
    - **Compartilhado:** Um gráfico para cada telemetria é plotado contra o mesmo eixo y.
    - **Sobreposição:** Use-o para empilhar várias linhas no mesmo eixo y, com a alteração de dados do eixo y com base na linha selecionada.

  ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media/howto-create-analytics/yaxiscontrol.png)

- **Controle de zoom:** O zoom permite que você aprofunde ainda mais seus dados. Se você encontrar um período de tempo em que deseja se concentrar dentro do seu conjunto de resultados, use o ponteiro do mouse para pegar a área e arrastá-la até o ponto final de sua escolha. Em seguida, clique com o botão direito do mouse na área selecionada e clique em Zoom.

  ![Amplie os dados](media/howto-create-analytics/zoom.png)

a elipse, há mais controles de gráficos para interagir com os dados.

- **Grade de exibição:** Seus resultados estão disponíveis em um formato de tabela, permitindo que você visualize o valor específico para cada ponto de dados.

- **Solte um marcador:** O controle 'Drop Marker' fornece uma maneira de ancorar certos pontos de dados no gráfico. É útil quando você está tentando comparar dados para várias linhas em diferentes períodos de tempo.

  ![Mostrando a exibição em grade da sua análise](media/howto-create-analytics/additionalchartcontrols.png)