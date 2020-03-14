---
title: Recursos avançados do Azure Metrics Explorer
description: Saiba mais sobre os recursos avançados do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: c754f33ab5f4346413b6603ca2cd404acac5443f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248782"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Recursos avançados do Azure Metrics Explorer

> [!NOTE]
> Este artigo pressupõe que você esteja familiarizado com os recursos básicos do Metrics Explorer. Se você for um novo usuário e quiser saber como criar seu primeiro gráfico de métrica, consulte [introdução ao Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métricas no Azure

[Métricas no Azure Monitor](data-platform-metrics.md) são a série de valores medidos e as contagens coletadas e armazenadas ao longo do tempo. Há métricas padrão (ou da “plataforma”) e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de uso e integridade dos recursos do Azure. Enquanto as métricas personalizadas são enviadas ao Azure por seus aplicativos usando a [api Application insights para métricas e eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [extensão WAD (Windows diagnóstico do Azure)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)ou por [Azure monitor API REST](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Criar exibições com várias métricas e gráficos

Você pode criar gráficos que plotam várias linhas de métricas ou mostrar vários gráficos de métrica de uma só vez. Essa funcionalidade permite que você:

- Correlacione as métricas relacionadas no mesmo grafo para ver como um valor está relacionado a outro
- Exibir métricas com diferentes unidades de medida no próximo proximidade
- Agregue visualmente e compare métricas de vários recursos

Por exemplo, se você tiver 5 contas de armazenamento e quiser saber quanto espaço total é consumido entre elas, você pode criar um gráfico de área (empilhado) que mostra a pessoa e a soma de todos os valores em determinados pontos no tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Várias métricas no mesmo gráfico

Primeiro, [crie um novo gráfico](metrics-getting-started.md#create-your-first-metric-chart). Clique em **Adicionar métrica** e repita as etapas para adicionar outra métrica no mesmo gráfico.

   > [!NOTE]
   > Normalmente, você não deseja ter métricas com diferentes unidades de medida (ou seja, “milissegundos” e “quilobytes”) ou com uma escala consideravelmente diferente em um gráfico. Em vez disso, considere o uso de vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no Metrics Explorer.

### <a name="multiple-charts"></a>Vários gráficos

Clique em **Adicionar gráfico** e crie outro gráfico com uma métrica diferente.

### <a name="order-or-delete-multiple-charts"></a>Ordenar ou excluir vários gráficos

Para ordenar ou excluir vários gráficos, clique no símbolo de reticências ( **...** ) para abrir o menu do gráfico e escolha o item de menu apropriado de **mover para cima**, **mover para baixo**ou **excluir**.

## <a name="apply-filters-to-charts"></a>Aplicar filtros a gráficos

Aplique filtros aos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica “Contagem de transações” tiver uma dimensão, “Tipo de resposta”, que indica se a resposta das transações foi bem-sucedida ou falhou, a filtragem nessa dimensão plotará uma linha de gráfico apenas para as transações bem-sucedidas (ou apenas para aquelas com falha). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. Selecione **Adicionar filtro** acima do gráfico

2. Selecione qual dimensão (propriedade) você deseja filtrar

   ![imagem de métrica](./media/metrics-charts/00006.png)

3. Selecione quais valores de dimensão você deseja incluir ao plotar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![imagem de métrica](./media/metrics-charts/00007.png)

4. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![imagem de métrica](./media/metrics-charts/00008.png)

5. Repita as etapas 1 a 4 para aplicar vários filtros aos mesmos gráficos.



## <a name="apply-splitting-to-a-chart"></a>Aplicar divisão a um gráfico

Divida uma métrica pela dimensão para visualizar uma comparação de diferentes segmentos da métrica em relação um ao outro e identificar os segmentos distantes de uma dimensão.

### <a name="apply-splitting"></a>Aplicar a separação

1. Clique em **Aplica-se a divisão** acima do gráfico.
 
   > [!NOTE]
   > A divisão não pode ser usada com gráficos com várias métricas. Além disso, você pode ter vários filtros, mas apenas uma dimensão de divisão aplicada a um único gráfico.

2. Escolha uma dimensão na qual você deseja segmentar o gráfico:

   ![imagem de métrica](./media/metrics-charts/00010.png)

   Agora o gráfico mostra várias linhas, uma para cada segmento de dimensão:

   ![imagem de métrica](./media/metrics-charts/00012.png)

3. Clique fora do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Use a Filtragem e a Divisão na mesma dimensão para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura dos gráficos.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do eixo y do gráfico

Bloquear o intervalo do eixo y se torna importante quando o gráfico mostra flutuações menores de valores maiores. 

Por exemplo, quando o volume de solicitações bem-sucedidas cai de 99,99% para 99,5%, isso pode representar uma redução significativa na qualidade de serviço. No entanto, observar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível com as configurações de gráfico padrão. Nesse caso, você pode bloquear o limite mais baixo do gráfico para 99%, o que tornaria essa pequena queda mais aparente. 

Outro exemplo é uma flutuação na memória disponível, em que o valor tecnicamente nunca alcançará 0. Fixar o intervalo para um valor maior pode facilitar a detecção de quedas na memória disponível. 

Para controlar o intervalo do eixo y, use o menu do gráfico "…" e selecione **Editar gráfico** para acessar configurações do gráfico avançadas. Modifique os valores na seção Intervalo do Eixo U ou use o botão **Automático** para reverter para os padrões.

![imagem de métrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Bloquear os limites do eixo y para que os gráficos que acompanham várias contagens ou somas por um período (e, portanto, usam agregações máximas, mínimas, soma e contagem) geralmente requer especificar uma granularidade de tempo fixo, em vez de contar com os padrões automáticos. Isso é necessário porque os valores em gráficos mudam quando a granularidade de tempo é modificada automaticamente pelo usuário redimensionando a janela do navegador ou passando de uma resolução de tela para outra. A alteração de granularidade de tempo resultante afeta o visual do gráfico, invalidando a seleção atual do intervalo do eixo y.

## <a name="pin-charts-to-dashboards"></a>Fixar gráficos em painéis

Depois de configurar os gráficos, talvez você deseje adicioná-los aos painéis, de modo que você possa exibi-los novamente, possivelmente, no contexto de outra telemetria de monitoramento ou compartilhá-los com sua equipe.

Para fixar um gráfico configurado em um painel:

Depois de configurar o gráfico, clique no menu **Ações do Gráfico** no canto superior direito do gráfico e clique em **Fixar no painel**.

![imagem de métrica](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Criar regras de alerta

Você pode usar os critérios definidos para visualizar suas métricas como fundamento para uma regra de alerta com base na métrica. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. É possível alterar essas configurações posteriormente no painel de criação de regras de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique em **Nova regra de alerta**

![Botão de nova regra de alerta realçado em vermelho](./media/metrics-charts/015.png)

Você será levado para o painel de criação de regra de alerta com as dimensões de métrica subjacentes do gráfico preenchido previamente para facilitar ainda mais a gerar regras de alerta personalizadas.

![Criar regra de alerta](./media/metrics-charts/016.png)

Verifique este [artigo](alerts-metric.md) para saber mais sobre como configurar alertas de métrica.

## <a name="troubleshooting"></a>solução de problemas

*Não vejo dados no gráfico.*

* Os filtros se aplicam a todos os gráficos do painel. Verifique se, ao se concentrar em um gráfico, não definiu um filtro que excluía todos os dados em outro.

* Se quiser definir filtros diferentes em gráficos diferentes, crie-os em folhas diferentes e os salve como favoritos separados. Se desejar, você poderá fixá-los ao painel para que eles sejam exibidos lado a lado.

* Se você segmentar um gráfico por uma propriedade que não esteja definida na métrica, o gráfico ficará vazio. Tente limpar a segmentação (divisão) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Próximas etapas

  Leia [Criar painéis personalizados de KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para a criação de painéis acionáveis com métricas.

