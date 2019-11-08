---
title: Analisar dados do dispositivo em seu aplicativo do Azure IoT Central | Microsoft Docs
description: Este artigo descreve como analisar os dados do dispositivo no aplicativo IoT Central do Azure usando consultas e visualizações.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827971"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar análise para analisar os dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

A Central de IoT do Azure fornece recursos avançados de análise para compreender grandes quantidades de dados de seus dispositivos. Para começar, visite **análise** no painel esquerdo.

## <a name="querying-your-data"></a>Consultando seus dados

Você precisará escolher um **conjunto de dispositivos**, adicionar um **filtro** (opcional) e selecionar um **período de tempo** para começar. Depois de terminar, selecione **Mostrar resultados** para começar a visualizar seus dados.

* **Conjuntos de dispositivos:** um [conjunto de dispositivo](howto-use-device-sets.md) é um grupo de dispositivos definido pelo usuário. Por exemplo, todos os refrigeradores em Oakland ou todas as turbinas eólicas rev 2.0.

* **Filtros:** opcionalmente, você pode adicionar filtros à pesquisa para apurar ainda mais os dados. Você pode adicionar até 10 filtros por vez. Por exemplo, em todos os refrigeradores em Oakland, encontre aqueles que apresentaram temperatura acima de 60 graus.
* **Período de tempo:** por padrão, recuperaremos dados dos últimos 10 minutos. Você pode alterar esse valor para um dos intervalos de tempo predefinidos ou selecionar um período de tempo personalizado.

  ![Consulta de análise](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizando seus dados

Depois de consultar seus dados, você poderá começar a visualizá-los. Você pode mostrar/ocultar medidas, alterar a maneira como os dados são agregados e dividir os dados ainda mais pelas diferentes propriedades do dispositivo.  

* **Dividir por:** dividir os dados pelas propriedades do dispositivo permite que você faça uma busca mais detalhada nos dados. Por exemplo, você pode dividir os resultados por ID ou local do dispositivo.

* **Medições:** você pode optar por mostrar/ocultar até 10 itens de telemetria diferentes que estão sendo relatados por seus dispositivos por vez. As medidas são itens como temperatura e umidade.

* **Agregação:** por padrão, podemos agregar dados pela média, mas você pode alterar a agregação de dados para outra coisa para atender às suas necessidades.

   ![Visualização de análise - dividir por](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Você tem várias maneiras de alterar os resultados da consulta para atender às suas necessidades de visualização. Você pode alternar entre uma exibição de gráfico e uma exibição de grade, ampliar e reduzir, atualizar o conjunto de dados e alterar o modo como as linhas são mostradas.

* **Mostrar grade:** Os resultados estão disponíveis em um formato de tabela, permitindo que você exiba o valor específico para cada ponto de dados. Essa exibição também atende aos padrões de acessibilidade.
* **Mostrar gráfico:** Os resultados são exibidos em um formato de linha para ajudá-lo a identificar tendências e anomalias ascendentes ou descendentes.

  ![Mostrando a exibição em grade da sua análise](media/howto-create-analytics/analytics-showgrid.png)

O zoom permite que você se baseie em seus dados. Se você encontrar um período de tempo em que gostaria de se concentrar no conjunto de resultados, use o cursor para capturar a área que deseja ampliar e use os controles disponíveis para executar uma das seguintes ações:

* **Ampliar:** Depois de selecionar um período de tempo, o zoom é habilitado e permite que você amplie seus dados.
* **Reduzir:** esse controle permite que você reduza um nível em relação ao último zoom. Por exemplo, se você ampliar seus dados três vezes, reduzir o levará de volta uma etapa por vez.
* **Redefinir zoom:** depois de experimentar vários níveis de zoom, você pode usar o controle de redefinição de zoom para retornar ao conjunto de resultados original.

  ![Executando zoom em seus dados](media/howto-create-analytics/analytics-zoom.png)

Você pode alterar o estilo da linha para atender às suas necessidades. Você tem quatro opções:

* **Linha:** Uma linha simples entre cada um dos pontos de dados.
* **Smooth:** Uma linha curva entre cada ponto.
* **Etapa:** A linha entre cada ponto no gráfico é uma etapa.
* **Dispersão:** Todos os pontos são plotados no gráfico sem linhas que os conectam.

  ![Diferentes tipos de linha disponíveis na análise](media/howto-create-analytics/analytics-linetypes.png)

Por fim, você pode organizar seus dados no eixo Y escolhendo um dos três modos:

* **Empilhado:** um gráfico para cada medida é empilhado e cada um dos gráficos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medidas selecionadas e deseja ter uma exibição distinta dessas medidas.
* **Não empilhado:** um gráfico para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões nessas medidas para o mesmo intervalo de tempo.
* **Eixo Y compartilhado:** todos os gráficos compartilham o mesmo eixo Y e os valores do eixo não são alterados. Os gráficos compartilhados do eixo Y são úteis quando você deseja examinar uma única medida ao fatiar os dados com a opção dividir por.

  ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar análises personalizadas para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo Node.js](howto-connect-nodejs.md)