---
title: Gerenciar o uso e os custos do Azure Application Insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: b99c1c9348f8442233eeee8fd4442736c78ee4e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199036"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para Application Insights.  Um artigo relacionado, [monitoramento de uso e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços.

O Application Insights foi projetado para obter tudo o que você precisa para monitorar a disponibilidade, o desempenho e o uso de seus aplicativos Web, estejam eles hospedados no Azure ou no local. O Application Insights dá suporte a linguagens e estruturas populares, como .NET, Java e node. js, e integra-se com processos e ferramentas DevOps como o Azure DevOps, JIRA e PagerDuty. É importante entender o que determina os custos de monitoramento de seus aplicativos. Neste artigo, examinaremos o que impulsiona os custos de monitoramento de seu aplicativo e como você pode monitorar e controlá-los proativamente.

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, você poderá postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Modelo de preços

O preço do [aplicativo Azure insights][start] é um modelo **pago conforme o uso** com base no volume de dados ingerido e, opcionalmente, para a retenção de dados mais longa. Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo. Não há nenhum encargo de volume de dados para usar o [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

Há uma cobrança adicional para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). Testes na Web de várias etapas se referem a testes na Web que executam uma sequência de ações. Não há nenhuma cobrança separada para *testes de ping* de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada da mesma forma que outras telemetrias do seu aplicativo.

A opção Application Insights para [habilitar alertas em dimensões de métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) também pode gerar em custos adicionais, pois isso pode resultar na criação de métricas de pré-autenticação adicionais. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics) sobre as métricas baseadas em log e previamente agregadas em Application insights e sobre os [preços](https://azure.microsoft.com/pricing/details/monitor/) de Azure monitor métricas personalizadas.

### <a name="workspace-based-application-insights"></a>Application Insights com base no espaço de trabalho

Para Application Insights recursos que enviam seus dados para um espaço de trabalho do Log Analytics, chamados de [recursos de Application insights baseados no espaço de trabalho](create-workspace-resource.md), a cobrança de ingestão de dados e retenção é feita pelo espaço de trabalho onde os dados de Application insights estão localizados. Isso permite que os clientes aproveitem todas as opções do [modelo de preços](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#pricing-model) de log Analytics que inclui reservas de capacidade, além do pré-pago. Log Analytics também tem mais opções de retenção de dados, incluindo [a retenção por tipo de dados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#retention-by-data-type). Application Insights tipos de dados no espaço de trabalho recebem 90 dias de retenção sem encargos. O uso de testes da Web e a habilitação de alertas em dimensões de métricas personalizadas ainda são relatados por meio de Application Insights. Saiba como acompanhar a ingestão de dados e os custos de retenção em Log Analytics usando o [uso e os custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs), o [Gerenciamento de custos do Azure + cobrança](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#viewing-log-analytics-usage-on-your-azure-bill) e [consultas de log Analytics](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerenciar seu aplicativo

Se você ainda não estiver usando Application Insights, poderá usar a [calculadora de preços de Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso de Application insights. Comece inserindo "Azure Monitor" na caixa de pesquisa e clicando no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione Application Insights na lista suspensa tipo.  Aqui você pode inserir o número de GB de dados que você espera coletar por mês, portanto, a pergunta é a quantidade de dados que Application Insights coletará o monitoramento do aplicativo.

Há duas abordagens para resolver isso: uso de monitoramento padrão e amostragem adaptável, que está disponível no SDK do ASP.NET ou estimar a possível ingestão de dados com base no que outros clientes semelhantes viram.

### <a name="data-collection-when-using-sampling"></a>Coleta de dados ao usar amostragem

Com a [amostragem adaptável](sampling.md#adaptive-sampling)do SDK do ASP.net, o volume de dados é ajustado automaticamente para manter-se dentro de uma taxa máxima de tráfego especificada para o monitoramento de Application insights padrão. Se o aplicativo produzir uma quantidade menor de telemetria, como durante a depuração ou devido ao baixo uso, os itens não serão descartados pelo processador de amostragem, desde que o volume esteja abaixo do nível de eventos configurados por segundo. Para um aplicativo de alto volume, com o limite padrão de cinco eventos por segundo, a amostragem adaptável limitará o número de eventos diários a 432.000. Usando um tamanho de evento médio típico de 1 KB, isso corresponde a 13,4 GB de telemetria por mês de 31 dias por nó que hospeda seu aplicativo (já que a amostragem é feita de local para cada nó). 

Para SDKs que não dão suporte à amostragem adaptável, você pode empregar a [amostragem de ingestão](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), que obtém exemplos de quando os dados são recebidos por Application insights com base em uma porcentagem de dados a serem retidos, ou [amostragem de taxa fixa para sites de ASP.net, ASP.NET Core e Java](sampling.md#fixed-rate-sampling) para reduzir o tráfego enviado do seu servidor Web e navegadores da Web

### <a name="learn-from-what-similar-customers-collect"></a>Aprenda com o que os clientes semelhantes coletam

Na calculadora de preços do monitoramento do Azure para Application Insights, se você habilitar a funcionalidade "estimar volume de dados com base na atividade do aplicativo", poderá fornecer entradas sobre seu aplicativo (solicitações por mês e exibições de página por mês, caso você colete a telemetria do lado do cliente) e, em seguida, a calculadora informará a quantidade de dados mediana e 90 º do percentual coleta Esses aplicativos abrangem o intervalo de configuração de Application Insights (por exemplo, alguns têm [amostragem](../../azure-monitor/app/sampling.md)padrão, alguns não têm amostragem, etc.), portanto, você ainda tem o controle para reduzir o volume de dados que está ingerindo muito abaixo do nível mediano usando a amostragem. Mas esse é um ponto de partida para entender o que outros clientes semelhantes estão vendo.

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e calcule os custos

O Application Insights facilita a compreensão de quais são seus custos com base nos padrões de uso recentes. Para começar, no portal do Azure, do recurso Application Insights, acesse a página **Uso e custos estimados**:

![Escolher preços](./media/pricing/pricing-001.png)

a. Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) de seu servidor e aplicativos cliente e dos testes de disponibilidade.  
B. Uma cobrança separada é feita pelos [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).  
C. Visualize as tendências do volume de dados do último mês.  
D. Habilite a [amostragem](../../azure-monitor/app/sampling.md) de ingestão de dados.
E. Configure o limite de volume de dados diários.  

(Observe que todos os preços exibidos nas capturas de tela neste artigo são apenas para fins de exemplo. Para obter os preços atuais em sua moeda e região, consulte [preços de Application insights][pricing].)

Para investigar mais profundamente o uso do Application Insights, abra a página **Métricas**, adicione a métrica nomeada "Volume do ponto de dados" e selecione a opção *Aplicar divisão* para dividir os dados por "Tipo de item de telemetria".

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua fatura do Azure na seção **Gerenciamento de custos + cobrança** do portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions).  [Consulte abaixo](https://docs.microsoft.com/azure/azure-monitor/app/pricing#viewing-application-insights-usage-on-your-azure-bill) para obter detalhes sobre como usar isso para Application insights. 

![No menu à esquerda, selecione Cobrança](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Usando métricas de volume de dados
<a id="understanding-ingested-data-volume"></a>

Para saber mais sobre seus volumes de dados, selecionando **métricas** para o recurso Application insights, adicione um novo gráfico. Para a métrica do gráfico, em **métricas baseadas em log**, selecione **volume de ponto de dados**. Clique em **aplicar divisão**e selecione Agrupar por ** `Telemetryitem` tipo**.

![Usar métricas para examinar o volume de dados](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Consultas para entender os detalhes do volume de dados

Há duas abordagens para investigar os volumes de dados para Application Insights. O primeiro usa informações agregadas na `systemEvents` tabela e a segunda usa a `_BilledSize` propriedade, que está disponível em cada evento ingerido. `systemEvents`Não terá informações de tamanho de dados para [Application-Revisions com base no espaço de trabalho](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Usando informações de volume de dados agregados

Por exemplo, você pode usar a `systemEvents` tabela para ver o volume de dados ingerido nas últimas 24 horas com a consulta:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ou para ver um gráfico de volume de dados (em bytes) por tipo de dados dos últimos 30 dias, você pode usar:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Observe que essa consulta pode ser usada em um [alerta de log do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) para configurar o alerta em volumes de dados.  

Para saber mais sobre as alterações de dados de telemetria, podemos obter a contagem de eventos por tipo usando a consulta:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Usando informações de tamanho de dados por evento

Para saber mais detalhes sobre a origem dos volumes de dados, você pode usar a `_BilledSize` propriedade que está presente em cada evento ingerido.

Por exemplo, para examinar quais operações geram o volume mais de dados nos últimos 30 dias, podemos somar `_BilledSize` todos os eventos de dependência:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Volume de dados para recursos de Application Insights baseados em espaço de trabalho

Para examinar as tendências de volume de dados de todos os [recursos de Application insights baseados no espaço de trabalho](create-workspace-resource.md) em um espaço de trabalho da última semana, vá para o espaço de trabalho log Analytics e execute a consulta:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Para consultar as tendências do volume de dados por tipo para um recurso de Application Insights baseado no espaço de trabalho específico, no espaço de trabalho Log Analytics, use:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Exibindo o uso de Application Insights em sua fatura do Azure

O Azure fornece uma grande funcionalidade útil no gerenciamento de [custos do Azure +](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub de cobrança. Por exemplo, a funcionalidade de "análise de custo" permite que você exiba seus gastos para os recursos do Azure. A adição de um filtro por tipo de recurso (para Microsoft. insights/componentes para Application Insights) permitirá que você acompanhe seus gastos. Em seguida, para "Agrupar por", selecione "categoria do medidor" ou "medidor".  Para Application Insights recursos nos planos de preços atuais, a maior parte do uso será exibida como Log Analytics para a categoria de medidor, já que há um único back-end de logs para todos os componentes de Azure Monitor. 

Mais informações sobre seu uso podem ser obtidas [baixando seu uso do portal do Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
Na planilha baixada, você pode ver o uso por recurso do Azure por dia. Nesta planilha do Excel, o uso de seus recursos de Application Insights pode ser encontrado primeiro filtrando a coluna "categoria do medidor" para mostrar "Application Insights" e "Log Analytics" e, em seguida, adicionar um filtro na coluna "ID da instância", que é "contém Microsoft. insights/Components".  A maior parte Application Insights uso é relatada em metros com a categoria de medidor de Log Analytics, já que há um único back-end de logs para todos os componentes de Azure Monitor.  Somente Application Insights recursos em tipos de preço herdados e testes na Web de várias etapas são relatados com uma categoria de medidor de Application Insights.  O uso é mostrado na coluna "quantidade consumida" e a unidade de cada entrada é mostrada na coluna "unidade de medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua fatura de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="managing-your-data-volume"></a>Gerenciando o volume de dados

O volume de dados que você envia pode ser gerenciado usando as seguintes técnicas:

* **Amostragem:** você pode usar a amostragem para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Amostragem é a ferramenta principal que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre [recursos de amostragem](../../azure-monitor/app/sampling.md).

* **Limitar chamadas AJAX**: você pode [limitar o número de chamadas AJAX que podem ser relatadas](../../azure-monitor/app/javascript.md#configuration) em cada exibição de página ou desativar relatórios Ajax.

* **Desabilitar módulos desnecessários**: [edite ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar os módulos de coleta que você não precisa. Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.

* **Métricas de agregação prévia**: se você colocar chamadas para TrackMetric em seu aplicativo, poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite diário:** quando você cria um recurso do Application Insights no Portal do Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso do Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. O propósito dele é que o usuário irá gerar o limite diário antes de colocar o aplicativo em produção. 

    O limite máximo é 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de alto tráfego.
    
    Emails de aviso sobre o limite diário são enviados para a conta que são membros dessas funções para seu Application Insights recurso: "administrador", "AccountAdmin", "coadministrador", "proprietário".

    Tome cuidado ao definir o limite diário. A intenção deve ser *nunca atingir o limite diário*. Se atingir o limite diário, você perderá os dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção **Limite de volume diário**. Você pode acessar essa opção no painel **Uso e custos estimados** (isso está descrito em mais detalhes mais adiante neste artigo).
    
    Removemos a restrição de alguns tipos de assinatura com crédito que não pôde ser usado no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, a caixa de diálogo de limite diário teria instruções sobre como remover esse limite e permitir que ele fosse aumentado para mais de 32,3 MB/dia.
    
* **Limitação:** esta opção limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação. O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta enviá-los novamente. Ele espalha um surto por vários minutos. Se o seu aplicativo enviar dados acima da taxa de limitação constantemente, alguns dados serão descartados. (Os SDKs de ASP.NET, Java e JavaScript tentam reenviar os dados dessa maneira; outros SDKs podem simplesmente descartar dados restritos.) Se a limitação ocorrer, um aviso de notificação alertará que isso ocorreu.

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar seu volume máximo de dados diário

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo no restante do dia. *Não é aconselhável* deixar o aplicativo atingir o limite diário. Não será possível rastrear a integridade e o desempenho do seu aplicativo após ele atingir o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", no caso de seu aplicativo inesperadamente começar a enviar volumes muito mais altos de telemetria.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Examine Application Insights uso e custos estimados para entender a tendência de ingestão de dados e qual é o limite de volume diário a ser definido. Isso deve ser considerado com cuidado, pois você não poderá monitorar seus recursos depois que o limite for atingido.

### <a name="set-the-daily-cap"></a>Definir o limite diário

Para alterar o limite diário, na seção **Configurar** do recurso de Application insights, na página **uso e custos estimados** , selecione **limite diário**.

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

Para [alterar o limite diário por meio de Azure Resource Manager](../../azure-monitor/app/powershell.md), a propriedade a ser alterada é `dailyQuota` .  Por meio de Azure Resource Manager você também pode definir o `dailyQuotaResetTime` e o limite diário `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Criar alertas para o limite diário

O limite diário de Application Insights cria um evento no log de atividades do Azure quando os volumes de dados ingeridos atingem o nível de aviso ou o nível de limite diário.  Você pode [criar um alerta com base nesses eventos do log de atividades](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal). Os nomes de sinal para esses eventos são:

* Limite de aviso de limite diário do componente de Application Insights atingido

* Application Insights limite diário do componente atingido

## <a name="sampling"></a>amostragem
[Amostragem](../../azure-monitor/app/sampling.md) é um método de redução da taxa em que a telemetria é enviada para o seu aplicativo, enquanto mantém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Você também mantém contagens de eventos corretas.

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem mantém itens de telemetria relacionados, para que, por exemplo, quando Pesquisar for utilizado, você possa encontrar a solicitação relacionada a uma exceção específica. O algoritmo também mantém contagens corretas, para que você veja os valores corretos no Metrics Explorer referentes a taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [Amostragem adaptável](../../azure-monitor/app/sampling.md) é o padrão para o SDK do ASP.NET. A amostragem adaptável se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera automaticamente no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. 
* *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. A amostragem de ingestão não afeta o volume de telemetria enviado do seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a cota usada pela telemetria de navegadores e de outros SDKs.

Para definir a amostragem de ingestão, vá para o painel **Preços**:

![No painel Cota e preços, clique no bloco Amostras e selecione uma fração de amostragem](./media/pricing/pricing-004.png)

> [!WARNING]
> O painel **Amostragem de dados** controla somente o valor de amostragem de ingestão. Ele não reflete a taxa de amostragem aplicada pelo SDK do Application Insights no seu aplicativo. Se a telemetria de entrada já tiver sido obtida como amostra no SDK, a amostragem de ingestão não será aplicada.
>

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](analytics.md). A consulta tem esta aparência:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registro mantido, `itemCount` indica o número de registros originais que ele representa. É igual a 1 + o número de registros descartados anteriormente.

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

A retenção padrão para recursos de Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso de Application Insights. O conjunto completo de períodos de retenção disponíveis é 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços para a retenção de dados mais longa. 

Para alterar a retenção, de seu Application Insights recurso, vá para a página **uso e custos estimados** e selecione a opção de **retenção de dados** :

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-005.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes que os dados mais antigos sejam removidos.

A retenção também pode ser [definida programaticamente usando o PowerShell](powershell.md#set-the-data-retention) usando o `retentionInDays` parâmetro. Se você definir a retenção de dados para 30 dias, poderá disparar uma limpeza imediata de dados mais antigos usando o `immediatePurgeDataOn30Days` parâmetro, o que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade de limpeza só é exposta por meio de Azure Resource Manager e deve ser usada com extrema atenção. A hora de redefinição diária para o limite do volume de dados pode ser configurada usando Azure Resource Manager para definir o `dailyQuotaResetTime` parâmetro.

## <a name="data-transfer-charges-using-application-insights"></a>Cobranças de transferência de dados usando Application Insights

Enviar dados para Application Insights pode incorrer em encargos de largura de banda de dados. Conforme descrito na [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/), a transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída na taxa normal. A transferência de dados de entrada é gratuita. No entanto, esse encargo é muito pequeno (alguns%) em comparação com os custos de ingestão de dados de log Application Insights. Consequentemente, controlar os custos de Log Analytics precisa se concentrar no volume de dados ingerido e temos diretrizes para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar os emails de limite diário

Para desabilitar os emails de limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**. Há configurações para enviar email quando o limite é alcançado, bem como quando um nível de aviso ajustável é atingido. Se você quiser desabilitar todos os emails relacionados ao volume de limite diário, desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Tipo de preço corporativo herdado (por nó)

Para os pioneiros do Aplicativo Azure insights, ainda existem dois tipos de preço possíveis: básico e corporativo. O tipo de preço básico é o mesmo descrito acima e é a camada padrão. Ele inclui todos os recursos da camada Enterprise, sem custo adicional. A camada básica cobra principalmente do volume de dados que é ingerido.

> [!NOTE]
> Esses tipos de preço herdados foram renomeados. O tipo de preço Enterprise agora é chamado **por nó** e o tipo de preço básico agora é chamado **por GB**. Esses novos nomes são usados abaixo e na portal do Azure.  

A camada por nó (anteriormente Enterprise) tem um encargo por nó e cada nó recebe uma concessão de dados diária. No tipo de preço por nó, você será cobrado pelos dados ingeridos acima da bonificação incluída. Se você estiver usando o Operations Management Suite, deverá escolher a camada por nó.

Para ver os preços atuais em sua moeda e região, consulte [Preços do Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como [avaliar o impacto da mudança para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) com base em seus padrões de uso e [como optar pelo novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Direitos por camada de nó e de assinatura do Operations Management Suite

Os clientes que comprarem o Operations Management Suite E1 e E2 poderão obter Application Insights por nó como um componente adicional sem custo adicional como [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade do Operations Management Suite E1 e E2 inclui um direito a um nó do Application Insights por camada de nó. Cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado de ingestão de dados do Log Analytics), com a retenção de dados de 90 dias sem nenhum custo adicional. A camada é descrita mais detalhadamente mais adiante neste artigo.

Como essa camada é aplicável somente a clientes com uma assinatura do Operations Management Suite, os clientes que não têm uma assinatura do Operations Management Suite não veem uma opção para selecionar essa camada.

> [!NOTE]
> Para garantir que você obtenha esse direito, seus recursos de Application Insights devem estar no tipo de preço por nó. Esse direito se aplica apenas como nós. Application Insights recursos na camada por GB não percebem nenhum benefício. Esse direito não será visível nos custos estimados mostrados no painel **Uso e custo estimado**. Além disso, se você mover uma assinatura para o novo modelo de preços de monitoramento do Azure em abril de 2018, a camada por GB será a única camada disponível. Migrar uma assinatura para o novo modelo de preços de monitoramento do Azure não será recomendado se você tiver uma assinatura do Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Como a camada por nó funciona

* Você paga por cada nó que envia telemetria para todos os aplicativos na camada por nó.
  * Um *nó* é um computador de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda seu aplicativo.
  * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não contam como nós.
  * Se o aplicativo tiver vários componentes que enviam telemetria, como um serviço Web e um trabalhado de back-end, os componentes serão contados separadamente.
  * Os dados de [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contatos para fins de preços. Em uma assinatura, seus encargos são por nó, não por aplicativo. Se você tiver cinco nós que enviam telemetria para 12 aplicativos, o encargo será de cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. O encargo por hora é a cobrança mensal cotada dividida por 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizada não é transportada de um dia para o outro.
  * Se você escolher o tipo de preço por nó, cada assinatura receberá uma concessão diária de dados com base no número de nós que enviam telemetria para os recursos de Application Insights nessa assinatura. Portanto, se você tiver cinco nós que enviam dados todos os dias, você terá uma permissão em pool de 1 GB aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós enviam mais dados que outros nós, porque os dados incluídos são compartilhados entre todos os nós. Se estiver em um determinado dia, os Application Insights recursos receberão mais dados do que o que está incluído na alocação de dados diária para essa assinatura, os encargos excedentes por GB de dados serão aplicados. 
  * A permissão de dados diária é calculada como o número de horas por dia (usando o UTC) que cada nó envia telemetria dividido por 24, multiplicado por 200 MB. Portanto, se você tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia serão ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
  * A bonificação diária por nível de nó não é compartilhada com aplicativos para os quais você escolheu a camada por GB. A permissão não utilizada não é herdada do dia a dia.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nós distinta

| Cenário                               | Contagem de nós diária total |
|:---------------------------------------|:----------------:|
| 1 aplicativo usando 3 instâncias do Serviço de Aplicativo do Azure e 1 servidor virtual | 4 |
| 3 aplicativos em execução em 2 VMs; os recursos de Application Insights para esses aplicativos estão na mesma assinatura e na camada por nó | 2 | 
| 4 aplicativos cujos recursos do Applications Insights estão na mesma assinatura; cada aplicativo executando 2 instâncias durante 16 horas fora de pico e 4 instâncias durante 8 horas de pico | 13.33 |
| Serviços de nuvem com uma função de trabalho e uma função web, cada uma executando duas instâncias | 4 | 
| Um cluster do Azure Service Fabric de 5 nós que executa 50 microsserviços; cada microsserviço executando 3 instâncias | 5|

* A contagem de nós exata depende de qual SDK do Application Insights seu aplicativo está usando. 
  * No SDK versões 2.2 e posterior, o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) do Application Insights e o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatam cada host de aplicativo como um nó. Exemplos são o nome do computador para servidores físicos e hosts de VM ou o nome da instância para serviços de nuvem.  A única exceção é um aplicativo que usa apenas o [.NET Core](https://dotnet.github.io/) e o Application Insights o SDK Core. Nesse caso, apenas um nó é relatado para todos os hosts porque o nome do host não está disponível.
  * Para versões anteriores do SDK, o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta como as versões do SDK mais recentes, mas o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) relata apenas um nó, independentemente do número de hosts do aplicativo.
  * Se o aplicativo usar o SDK para definir **roleInstance** como um valor personalizado, por padrão, esse mesmo valor será usado para determinar a contagem de nós.
  * Se você estiver usando uma nova versão do SDK com um aplicativo que é executado de computadores cliente ou dispositivos móveis, a contagem de nós pode retornar um número grande (devido ao grande número de computadores cliente ou dispositivos móveis).

## <a name="automation"></a>Automação

Você pode escrever um script para definir o tipo de preço usando o gerenciamento de recursos do Azure. [Saiba como](powershell.md#price).

## <a name="next-steps"></a>Próximas etapas

* [amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/