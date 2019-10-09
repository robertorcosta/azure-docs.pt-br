---
title: Visualizar dados no gerenciador da versão prévia do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve os vários recursos e opções disponíveis no aplicativo Web do Time Series Insights Explorer.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: a0d8f7cdace8d3e810dfcda6e54df5823840307c
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034338"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizar os dados na atualização do gerenciador

Este documento descreve a interface do usuário e os recursos de experiência do usuário e a interface do [aplicativo Web de demonstração](https://insights.timeseries.azure.com/preview/demo)do Azure Time Series insights Preview. Especificamente, ele aborda o layout do exemplo hospedado, opções de personalização de interface e navegação por meio da demonstração fornecida.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Time Series Insights Explorer Preview, você deve:

* Criar um ambiente do Time Series Insights. Para saber mais sobre como provisionar uma instância, experimente o tutorial de [visualização Azure Time Series insights](./time-series-insights-update-create-environment.md) .
* [Forneça acesso a dados](./time-series-insights-data-access.md) para o ambiente de time Series insights que você criou para a conta. Você pode fornecer acesso a outras pessoas, bem como para si mesmo.
* Adicione uma origem de evento ao ambiente de Time Series Insights para enviar dados por push para o ambiente:
  * Saiba [como se conectar a um hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Saiba [como se conectar a um hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Saiba mais sobre o Gerenciador de visualização

O Gerenciador da Versão prévia do Azure Time Series Insights consiste dos seguintes elementos:

[exibição do Gerenciador de ![a](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Painel de ambiente</a>: Exibe seus ambientes de Azure Time Series Insights.
- <a href="#navigation-menu">Menu de navegação</a>: Use-a para alternar entre as páginas **analisar** e **modelo** .
- <a href="#hierarchy-tree">Árvore hierárquica</a>: Use-o para selecionar um modelo específico e elementos de dados a serem gráficos.
- <a href="#preview-well">Série temporal</a>: Exibe os elementos de dados selecionados no momento no formato de tabela com codificação por cores.
- <a href="#preview-chart">Painel gráfico</a>: Exibe o gráfico de trabalho atual.
- <a href="#time-editor-panel">Linha do tempo</a>: Use-o para modificar seu período de tempo de trabalho.
- <a href="#navigation-panel">Barra de aplicativo</a>: Contém as opções de gerenciamento de usuário, como o locatário atual. Você pode usá-lo para alterar as configurações de tema e idioma.

## <a name="environment-drop-down-list"></a>Lista suspensa ambiente

A lista suspensa ambiente exibe todos os Time Series Insights ambientes aos quais você tem acesso. A lista inclui ambientes pagos conforme o uso, como a visualização de Time Series Insights. A lista também inclui ambientes S1/S2, que estão geralmente disponíveis.

1. Selecione a seta suspensa ao lado do seu ambiente exibido.

   [painel de controle do ![a](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Em seguida, selecione o ambiente desejado.

## <a name="navigation-menu"></a>Menu de navegação

  [menu de navegação do ![a](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Use o menu de navegação para selecionar entre duas exibições:

* **Analisar**: Use-o para criar um gráfico e executar análises avançadas em seus dados de série temporal modelados ou não modelados.
* **Modelo**: Use-o para enviar por push novos Time Series Insights tipos de visualização, hierarquias e instâncias para seu modelo de Time Series Insights.

## <a name="hierarchy-tree"></a>Árvore hierárquica

A árvore hierárquica exibe os elementos de dados selecionados que incluem modelos, dispositivos específicos e sensores em seus dispositivos.

### <a name="model-search-panel"></a>Painel de pesquisa de modelo

Você pode usar o painel de pesquisa de modelo para pesquisar e navegar facilmente na hierarquia de modelo de série temporal para localizar as instâncias de série temporal específicas que você deseja exibir no gráfico. Depois de selecionar suas instâncias, elas são adicionadas ao gráfico atual e bem aos dados.

  [painel de pesquisa de modelo ![a](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Criação de modelos

A visualização de Azure Time Series Insights dá suporte a operações CRUD (criação, leitura, atualização e exclusão) completas no seu modelo de série temporal.

* **Tipo de Modelo do Time Series**: Você pode usar tipos de Time Series Insights para definir variáveis ou fórmulas para fazer cálculos. Eles estão associados a uma determinada instância de Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquias do modelo do Time Series**: Hierarquias são organizações sistemáticas de seus dados. Hierarquias descrevem as relações entre entidades diferentes em seus dados de Azure Time Series Insights.
* **Instâncias do modelo do Time Series**: As instâncias são a série temporal em si. Na maioria dos casos, eles são **DeviceID** ou **AssetID**, que é o identificador exclusivo do ativo no ambiente.

Para saber mais sobre o Modelo do Time Series, consulte [Times Series Models](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Bem-visualização

O bem exibe os campos de instância e outros metadados associados às instâncias de Time Series Insights selecionadas. Ao marcar as caixas de seleção no lado direito, você pode ocultar ou exibir instâncias específicas do gráfico atual. Você também pode remover elementos de dados específicos de seus dados atuais bem selecionando o controle vermelho **delete** (lixeira) no lado esquerdo do elemento.

  [Bem ![a visualização](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Para reconfigurar o layout da página **analisar** gráfico, selecione o ícone de reticências no canto superior direito:

  [opções de layout de @no__t 1Telemetry](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se você vir a mensagem a seguir, a instância não terá nenhum dado durante o período de tempo selecionado. Para resolver o problema, aumente o período de tempo ou confirme se a instância está enviando dados por push.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Visualizar gráfico

Com o gráfico, você pode exibir Time Series Insights instâncias como linhas. Você pode recolher o painel de ambiente, o modelo de dados e o painel de controle de intervalo de tempo selecionando os controles da Web para aumentar o tamanho do gráfico.

  [Visão geral do gráfico de ![Preview](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Intervalo de datas selecionado**: Controla quais elementos de dados estão disponíveis para visualização.

- **Ferramenta de controle deslizante de intervalo de datas internas**: Use os dois controles de ponto de extremidade arrastando-os sobre o período de tempo desejado.

- **Controle de recolhimento do período de tempo**: Recolhe e expande o editor de alcance do painel de tempo.

- **Controle de formato do eixo y**: Percorre as opções de exibição do eixo y disponíveis:

    * `Default`: Cada linha tem um eixo y individual.
    * `Stacked`: Use-o para empilhar várias linhas no mesmo eixo y, com os dados do eixo y mudando com base na linha selecionada.
    * `Shared`: Todos os dados do eixo y exibidos juntos.

- **Elemento de dados atual**: O elemento de dados selecionado no momento e seus detalhes associados.

Para analisar mais detalhadamente uma fatia de dados específica, clique em um ponto de dados no grafo atual e arraste a área selecionada para o ponto de extremidade de sua escolha. Clique com o botão direito do mouse na área cinza selecionada e selecione **zoom**, conforme mostrado nesta imagem a seguir:

  [1Preview-zoom do gráfico de @no__t](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Depois de executar a ação de **zoom** , você verá o conjunto de dados selecionado. Selecione o controle de formato do eixo y para percorrer as três representações do eixo y de seus dados de Time Series Insights.

  [1Preview-eixo y do gráfico de @no__t](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Aqui, você pode ver um exemplo dos eixos Y compartilhados:

  [eixo Y compartilhado de @no__t 1Preview](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Painel de editor de tempo

Quando você trabalha com a versão prévia do Time Series Insights, você primeiro selecione um período de tempo. O intervalo de tempo selecionado controla o conjunto de dados que está disponível para manipulação com os widgets de Time Series Insights Preview. Os seguintes controles da Web estão disponíveis na visualização Time Series Insights para selecionar seu período de tempo de trabalho:

  [![Painel de seleção de tempo](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Ferramenta de controle deslizante de intervalo de datas internas**: Use os dois controles de ponto de extremidade arrastando-os sobre o período de tempo desejado. Esse intervalo de datas interna é restrito pelo controle deslizante de intervalo de datas externa.

1. **Aumentar e diminuir os botões de intervalo de data**: Aumente ou diminua o período de tempo, selecionando um dos botões para o intervalo desejado.

1. **Controle de recolhimento do período de tempo**: Esse controle da Web permite ocultar todos os controles, exceto a ferramenta de controle deslizante de intervalo de datas interna.

1. **Controle deslizante de intervalo de datas externas**: Use os controles de ponto de extremidade para selecionar o intervalo de datas externas, que estará disponível para seu controle de intervalo de datas interna.

1. **Lista suspensa de intervalos de datas rápidos**: Use-a para alternar rapidamente entre as seleções de intervalo de tempo predefinido, como os últimos **30 minutos**, as **últimas 12 horas**ou um **intervalo personalizado**. A alteração desse valor também muda os intervalos de intervalo disponíveis discutidos na ferramenta de controle deslizante de tamanho do intervalo.

1. **Ferramenta de controle deslizante de tamanho do intervalo**: Use-o para ampliar e reduzir os intervalos no mesmo período de tempo. Essa ação fornece controle mais preciso dos movimentos entre fatias de tempo grandes. Ele exibe tendências suaves abaixo de fatias como um milissegundo. Você pode usá-lo para ver os cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição mais ideal dos dados na seleção, que balanceia a resolução, velocidade de consulta e granularidade.

1. **Intervalo de datas para-e-do controle da Web**: Com esse controle da Web, você pode selecionar facilmente os intervalos de data e hora desejados. Você também pode usar o controle para alternar entre fusos horários diferentes. Depois de fazer as alterações a serem aplicadas ao seu espaço de trabalho atual, selecione **salvar**.

   [![To e do painel de seleção](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Painel de navegação

O painel de navegação Time Series Insights visualização aparece na parte superior do seu aplicativo Time Series Insights. Ele fornece as seguintes funcionalidades.

### <a name="current-session-share-link-control"></a>Controle de link de compartilhamento de sessão atual

  [ícone de @no__t 1Share](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selecione o ícone novo **compartilhamento** para compartilhar um link de URL com sua equipe.

  [![Share sua URL de instância](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Seção de Locatário

  [seleção de @no__t 1Tenant](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Exibe as informações atuais da sua conta de entrada de Time Series Insights.
* Use-o para alternar entre os temas de Time Series Insights disponíveis.
* Use-o para exibir o [aplicativo Web de demonstração](https://insights.timeseries.azure.com/preview/demo)de visualização.

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, selecione o ícone de perfil localizado no canto superior direito. Em seguida, selecione **alterar tema**.

  [seleção de @no__t 1Theme](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> A seleção de idioma também está disponível selecionando o ícone de perfil.

O Azure Time Series Insights Preview suporta dois temas:

* **Tema claro**: O tema padrão mostrado ao longo deste documento.
* **Tema escuro**: Renderiza o explorer conforme mostrado aqui:

  [tema escuro @no__t 1Selected](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controles de ambiente S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de visualização

Esta seção se aplica somente a ambientes existentes do S1/S2 que tentam usar o gerenciador na interface do usuário atualizada. Talvez você queira usar o produto disponível e a visualização em combinação. Adicionamos algumas funcionalidades de interface do usuário existente para ao gerenciador atualizado, mas você pode obter a experiência de interface do usuário completa para o ambiente S1/S2 no gerenciador do Time Series Insights existente. 

Em vez da hierarquia, você verá o painel Time Series Insights termos, em que você define consultas em seu ambiente. Use-o para filtrar seus dados com base em um predicado.

  [painel de consulta ![Where](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

O painel de editor de termos de visualização do Azure Time Series Insights leva em consideração os seguintes parâmetros:

**Onde**: Use a cláusula WHERE para filtrar rapidamente seus eventos usando o conjunto de operandos listados na tabela a seguir. Caso você realize uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

| Operação | Tipos com suporte   | Observações |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL. |
| `HAS` | Cadeia de caracteres | Somente literais de cadeia de caracteres constantes são permitidos no lado direito. Cadeia de caracteres vazia e NULL não são permitidos. |

Para saber mais sobre os tipos de dados e operações de consulta com suporte, confira [TSX (expressão de série temporal)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemplos de cláusulas WHERE

  [exemplos de cláusula ![Where](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Medida**: Uma lista suspensa que exibe todas as colunas numéricas (**duplos**) que você pode usar como elementos para seu gráfico atual.

**Dividir por**: Essa lista suspensa exibe todas as colunas categóricas disponíveis (cadeias de caracteres) em seu modelo no qual você pode agrupar seus dados. Você pode adicionar até cinco termos para exibir no mesmo eixo x. Insira os parâmetros desejados e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  [![Queried e exibição filtrada um](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Você pode mostrar e ocultar elementos no painel de gráfico selecionando o ícone visível, conforme mostrado na imagem a seguir. Para remover completamente as consultas, selecione o **X**vermelho.

  [![Queried e exibição filtrada dois](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na visualização de Azure Time Series insights.
- Leia o documento de visualização de Time Series Insights na [modelagem de dados](./time-series-insights-update-tsm.md).
- Saiba [como diagnosticar e solucionar problemas](./time-series-insights-update-how-to-troubleshoot.md) de sua instância de time Series insights.
