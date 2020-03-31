---
title: Visão geral das Pastas de Trabalho do Azure Monitor
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658227"
---
# <a name="azure-monitor-workbooks"></a>Pastas de trabalho do Azure Monitor

Os livros de trabalho fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos dentro do portal Azure. Eles permitem que você toque em várias fontes de dados de todo o Azure e as combine em experiências interativas unificadas. 

## <a name="data-sources"></a>Fontes de dados

As atos de trabalho podem consultar dados de várias fontes dentro do Azure. Os autores de livros de trabalho podem transformar esses dados para fornecer insights sobre a disponibilidade, desempenho, uso e saúde geral dos componentes subjacentes. Por exemplo, analisar registros de desempenho de máquinas virtuais para identificar instâncias de alta CPU ou memória baixa e exibir os resultados como uma grade em um relatório interativo.
  
Mas o verdadeiro poder das livros de trabalho é a capacidade de combinar dados de fontes diferentes em um único relatório. Isso permite a criação de visualizações de recursos compostos ou se une a recursos que permitem dados e insights mais ricos que de outra forma seriam impossíveis.

Atualmente, as cadernetas são compatíveis com as seguintes fontes de dados:

* [Logs](workbooks-data-sources.md#logs)
* [Métricas](workbooks-data-sources.md#metrics)
* [Gráfico de recursos do Azure](workbooks-data-sources.md#azure-resource-graph)
* [Alertas (Visualização)](workbooks-data-sources.md#alerts-preview)
* [Saúde da Carga de Trabalho (Visualização)](workbooks-data-sources.md#workload-health-preview)
* [Azure Resource Health (Visualização)](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer (Visualização)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualizações

Os livros de trabalho fornecem um rico conjunto de recursos para visualizar seus dados. Para exemplos detalhados de cada tipo de visualização, você pode consultar os links de exemplo abaixo:

* [Texto](workbooks-visualizations.md#text)
* [Gráficos](workbooks-visualizations.md#charts)
* [Grades](workbooks-visualizations.md#grids)
* [Blocos](workbooks-visualizations.md#tiles)
* [Árvores](workbooks-visualizations.md#trees)
* [Gráficos](workbooks-visualizations.md#graphs)

![Visualizações de livros de trabalho de exemplo](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Introdução

Para explorar a experiência dos livros de trabalho, primeiro navegue até o serviço Azure Monitor. Isso pode ser feito digitando **monitor** na caixa de pesquisa no portal Azure.

Em seguida, **selecione Workbooks (visualização)**.

![Captura de tela do botão de visualização de Workbooks destacado em uma caixa vermelha](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galeria

Isso leva você à galeria de livros de trabalho:

![Captura de tela da visualização da galeria de livros de trabalho do Azure Monitor](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Carteiras de trabalho versus modelos de carteira de trabalho

Você pode ver uma _carteira de trabalho_ em verde e uma série de _modelos_ de carteira de trabalho em roxo. Os modelos servem como relatórios curados que são projetados para reutilização flexível por vários usuários e equipes. A abertura de um modelo cria uma carteira de trabalho transitória preenchida com o conteúdo do modelo. 

Você pode ajustar os parâmetros da carteira de trabalho baseada em modelo e realizar a análise sem medo de quebrar a experiência futura de relatórios para colegas. Se você abrir um modelo, faça alguns ajustes e selecione o ícone de salvamento, você estará salvando o modelo como uma carteira de trabalho que, em seguida, mostraria em verde deixando o modelo original intocado. 

o capô, os modelos também diferem das aferidas às aferidas. Salvar uma pasta de trabalho cria um recurso associado do Azure Resource Manager, enquanto a pasta de trabalho transitória criada ao abrir um modelo não tem nenhum recurso exclusivo associado a ele. Para saber mais sobre como o controle de acesso é gerenciado em livros de trabalho, consulte o artigo de controle de acesso das [abcortes.](workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Explorando um modelo de carteira de trabalho

Selecione **Análise de falha** de aplicativo para ver um dos modelos de caderneta de trabalho padrão do aplicativo.

![Captura de tela do modelo de análise de falha de aplicativo](./media/workbooks-overview/failure-analysis.png)

Como dito anteriormente, a abertura do modelo cria uma carteira de trabalho temporária para que você possa interagir. Por padrão, a caderneta de trabalho é aberta no modo de leitura que exibe apenas as informações para a experiência de análise pretendida que foi criada pelo autor do modelo original.

No caso desta pasta de trabalho em particular, a experiência é interativa. Você pode ajustar a assinatura, os aplicativos direcionados e o intervalo de tempo dos dados que deseja exibir. Uma vez que você tenha feito essas seleções, a grade de Solicitações HTTP também é interativa, ao qual a seleção de uma linha individual mudará quais dados são renderizados nos dois gráficos na parte inferior do relatório.

### <a name="editing-mode"></a>Modo de edição

Para entender como este modelo de carteira de trabalho é montado, você precisa trocar para o modo de edição, selecionando **Editar**. 

![Captura de tela do modelo de análise de falha de aplicativo](./media/workbooks-overview/edit.png)

Depois de mudar para o modo de edição, você notará que um número de caixas de **edição** aparecerá à direita correspondente a cada aspecto individual da sua carteira de trabalho.

![Captura de tela do botão Editar](./media/workbooks-overview/edit-mode.png)

Se selecionarmos o botão de edição imediatamente a grade de dados de solicitação, poderemos ver que esta parte da nossa carteira de trabalho consiste em uma consulta kusto contra dados de um recurso do Application Insights.

![Captura de tela da consulta kusto subjacente](./media/workbooks-overview/kusto.png)

Clicar nos outros botões **Editar** à direita revelará uma série de componentes principais que compõem as linhas de [trabalho,](workbooks-visualizations.md#text)como caixas de texto baseadas em marcação, elementos de UI de [seleção](workbooks-parameters.md) de parâmetros e outros [tipos de gráficos/visualização](workbooks-visualizations.md). 

Explorar os modelos pré-construídos no modo de edição e, em seguida, modificá-los para atender às suas necessidades e salvar sua própria pasta de trabalho personalizada é uma excelente maneira de começar a aprender sobre o que é possível com as carteiras de trabalho do Azure Monitor.

## <a name="pinning-visualizations"></a>Visualizações de fixação

As etapas de texto, consulta e métricas em uma caderneta de trabalho podem ser fixadas usando o botão de pino nesses itens enquanto a carteira de trabalho estiver no modo pin, ou se o autor da carteira de trabalho habilitou as configurações para que esse elemento torne o ícone do pino visível. 

Para acessar o modo pin, clique em **Editar** para entrar no modo de edição e selecione o ícone de pino azul na barra superior. Em seguida, um ícone de pino individual aparecerá acima da caixa *Editar* de cada peça de trabalho correspondente no lado direito da tela.

![Experiência de pinos](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> O estado da carteira de trabalho é salvo no momento do pino, e as carteiras de trabalho fixadas em um painel não serão atualizadas se a carteira de trabalho subjacente for modificada. Para atualizar uma peça da caderneta de trabalho fixada, você precisará excluir e refixar essa parte.

## <a name="dashboard-time-ranges"></a>Intervalos de tempo do painel

As partes de consulta da caderneta de trabalho fixadas respeitarão o intervalo de tempo do painel se o item fixado estiver configurado para usar um parâmetro *time range.* O valor do intervalo de tempo do painel será usado como o valor do parâmetro de intervalo de tempo, e qualquer alteração no intervalo de tempo do painel fará com que o item fixado seja atualizado. Se uma peça fixa estiver usando o intervalo de tempo do painel, você verá a legenda da atualização da peça fixada para mostrar o intervalo de tempo do painel sempre que o intervalo de tempo for alterado. 

Além disso, as peças fixadas da carteira de trabalho usando um parâmetro de intervalo de tempo serão atualizadas automaticamente a uma taxa determinada pelo intervalo de tempo do painel. A última vez que a consulta foi exibida aparecerá na legenda da parte fixada.

Se uma etapa fixada tiver um intervalo de tempo explicitamente definido (não usar um parâmetro de intervalo de tempo), esse intervalo de tempo será sempre usado para o painel de controle, independentemente das configurações do painel. A legenda da parte fixada não mostrará o intervalo de tempo do painel e a consulta não será atualizada automaticamente no painel. A legenda mostrará a última vez que a consulta for executada.

> [!NOTE]
> As consultas usando a fonte de dados *de mesclagem* não são suportadas no momento ao fixar em painéis.

## <a name="sharing-workbook-templates"></a>Compartilhando modelos de carteira de trabalho

Uma vez que você começar a criar seus próprios modelos de carteira de trabalho, você pode querer compartilhá-lo com a comunidade em geral. Para saber mais e explorar outros modelos que não fazem parte da visualização padrão da galeria Do Azure Monitor, visite nosso [repositório do GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Para navegar pelos livros de trabalho existentes, visite a [biblioteca da Agenda De trabalho](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) no GitHub.

## <a name="next-step"></a>Próxima etapa

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
