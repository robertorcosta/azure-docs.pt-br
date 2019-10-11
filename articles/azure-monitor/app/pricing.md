---
title: Gerenciar o uso e os custos do Azure Application Insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 10/03/2019
ms.author: dalek
ms.openlocfilehash: 51caf34d0030fd404cd7f7c1868a0e2945c75b35
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264416"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para Application Insights.  Um artigo relacionado, [monitoramento de uso e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços.

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, você poderá postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Modelo de preços

O preço do [aplicativo Azure insights][start] é um modelo pago conforme o uso, com base no volume de dados ingerido e, opcionalmente, para a retenção de dados mais longa. Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure. 

### <a name="data-volume-details"></a>Detalhes do volume de dados

* O volume de dados é o número de bytes de telemetria recebidos pelo Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo. Para [dados tabulares importados para o Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descompactado de arquivos enviados ao Application Insights.
* Seus encargos de volume de dados do seu aplicativo agora são relatados em um novo medidor de cobrança denominado **Ingestão de Dados** a partir de abril de 2018. Esse novo medidor é compartilhado entre tecnologias de monitoramento, como o Application insights e o Log Analytics e, no momento, está sob o nome do serviço **log Analytics**. 
* Os dados de [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contatos para fins de preços.

> [!NOTE]
> Todos os preços exibidos nas capturas de tela neste artigo são apenas para fins de exemplo. Para preços atuais em sua moeda e região, consulte [Preços do Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Há uma cobrança adicional para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). Testes na Web de várias etapas se referem a testes na Web que executam uma sequência de ações.

Não há nenhuma cobrança separada para *testes de ping* de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada da mesma forma que outras telemetrias do seu aplicativo.

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerenciar seu aplicativo 

Se você ainda não estiver usando Application Insights, poderá usar a [calculadora de preços de Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso de Application insights. Comece inserindo "Azure Monitor" na caixa de pesquisa e clicando no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione Application Insights na lista suspensa tipo.  Aqui você pode inserir o número de GB de dados que você espera coletar por mês, de modo que eles perguntam a quantidade de dados Application Insights coletam o monitoramento do aplicativo. 

Há duas abordagens para resolver isso: uso de monitoramento padrão e amostragem adaptável, que está disponível no SDK do ASP.NET ou estimtate você likley ingestão de dados com base no que outros clientes semelhantes viram. 

### <a name="data-collection-when-using-sampling"></a>Coleta de dados ao usar amostragem

Com a [amostragem adaptável](https://docs.microsoft.com/azure/azure-monitor/app/sampling#adaptive-sampling-in-your-aspnetaspnet-core-web-applications)do SDK do ASP.net, o volume de dados é ajustado automaticamente para manter-se dentro de uma taxa máxima de tráfego especificada para o monitoramento de Application insights padrão. Se o aplicativo produzir uma quantidade menor de telemetria, como durante a depuração ou devido ao baixo uso, os itens não serão descartados pelo processador de amostragem, desde que o volume esteja abaixo do nível de eventos configurados por segundo. Para um aplicativo de alto volume, com o limite padrão de 5 eventos por segundo, a amostragem adaptável limitará o número de eventos diários a 432.000. Usando um tamanho de evento médio típico de 1 KB, isso corresponde a 13,4 GB de telemetria por mês de 31 dias por nó que hospeda seu aplicativo (já que a amostragem é feita de local para cada nó). 

Para SDKs que não dão suporte à amostragem adaptável, você pode empregar a [amostragem de ingestão](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling) , que é amostrada quando os dados são receveddos por Application insights com base em uma porcentagem de dados a serem retidos ou [amostragem de taxa fixa para ASP.net, ASP.NET Core e Java sites](https://docs.microsoft.com/azure/azure-monitor/app/sampling#fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites) para reduzir o tráfego enviado do seu servidor Web e navegadores da Web

### <a name="learn-from-what-similar-customers-collect"></a>Aprenda com o que os clientes semelhantes coletam

Na calculadora de preços do monitoramento do Azure para Application Insights, se você habilitar a funcionalidade "estimar volume de dados com base na atividade do aplicativo", poderá fornecer entradas sobre seu aplicativo (solicitações por mês e exibições de página por mês, caso você possa colete telemetria do lado do cliente) e, em seguida, a calculadora informará a quantidade mediana e 90 º percentil de dados coletados por aplicativos semelhantes. É claro que esses maiores aplicativos abrangem o intervalo de configuração de Application Insights (por exemplo, alguns têm [amostragem](../../azure-monitor/app/sampling.md)padrão, alguns não têm amostragem, etc.), portanto, você ainda tem o controle para reduzir o volume de dados que está ingerindo muito abaixo do nível mediano usando a amostragem. Mas esse é um ponto de partida para entender o que outros clientes semelhantes estão vendo. 

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e calcule os custos

O Application Insights facilita a compreensão de quais são seus custos com base nos padrões de uso recentes. Para começar, no portal do Azure, do recurso Application Insights, acesse a página **Uso e custos estimados**:

![Escolher preços](./media/pricing/pricing-001.png)

A. Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) de seu servidor e aplicativos cliente e dos testes de disponibilidade.  
B. Uma cobrança separada é feita pelos [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).  
C. Visualize as tendências do volume de dados do último mês.  
D. Habilite a [amostragem](../../azure-monitor/app/sampling.md) de ingestão de dados.   
E. Configure o limite de volume de dados diários.  

Para investigar mais profundamente o uso do Application Insights, abra a página **Métricas**, adicione a métrica nomeada "Volume do ponto de dados" e selecione a opção *Aplicar divisão* para dividir os dados por "Tipo de item de telemetria". 

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure na seção de **Cobrança** do Portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu à esquerda, selecione Cobrança](./media/pricing/02-billing.png)

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Exibindo o uso de Application Insights em sua fatura do Azure 

O Azure fornece uma grande funcionalidade útil no gerenciamento de [custos do Azure +](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub de cobrança. Por exemplo, a funcionalidade de "análise de custo" permite que você exiba seus gastos para os recursos do Azure. A adição de um filtro por tipo de recurso (para Microsoft. insights/componentes para Application Insights) permitirá que você veja acompanhar seus gastos.

Mais informações sobre seu uso podem ser obtidas [baixando seu uso do portal do Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure por dia. Nesta planilha do Excel, o uso de seus recursos de Application Insights pode ser encontrado primeiro filtrando a coluna "categoria do medidor" para mostrar "Application Insights" e "Log Analytics" e, em seguida, adicionar um filtro na coluna "ID da instância" que é "contém Microsoft. insights/Components ".  A maior parte Application Insights uso é relatada em metros com a categoria de medidor de Log Analytics, já que há um único back-end de logs para todos os componentes de Azure Monitor.  Somente Application Insights recursos em tipos de preço herdados e testes na Web de várias etapas são relatados com uma categoria de medidor de Application Insights.  O uso é mostrado na coluna "quantidade consumida" e a unidade de cada entrada é mostrada na coluna "unidade de medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua fatura de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="managing-your-data-volume"></a>Gerenciando o volume de dados 

Para entender a quantidade de dados que seu aplicativo está enviando, você pode:

* Vá para o painel **Uso e custos estimados** para ver o gráfico de volume de dados diário. 
* No Metrics Explorer, adicione um novo gráfico. Para a métrica do gráfico, selecione **Volume de ponto de dados**. Ative o **Agrupamento** e agrupe por **Tipo de dados**.
* Use o tipo de dados `systemEvents`. Por exemplo, para ver o volume de dados ingerido no último dia, a consulta seria:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Essa consulta pode ser usada em um [alerta de log do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) para configurar alertas em volumes de dados. 

O volume de dados que você envia pode ser gerenciado de três maneiras:

* **Amostragem**: Você pode usar a amostragem para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Amostragem é a ferramenta principal que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre [recursos de amostragem](../../azure-monitor/app/sampling.md).
 
* **Limite diário**: Quando você cria um recurso do Application Insights no portal do Microsoft Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso do Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. O propósito dele é que o usuário irá gerar o limite diário antes de colocar o aplicativo em produção. 

    O limite máximo é 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de alto tráfego. 
    
    Os emails de aviso sobre o limite diário são enviados para a conta que são membros dessas funções para o recurso de Application Insights: "Administrador", "AccountAdmin", "coadministrador", "proprietário".

    Tome cuidado ao definir o limite diário. A intenção deve ser *nunca atingir o limite diário*. Se atingir o limite diário, você perderá os dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção **Limite de volume diário**. Você pode acessar essa opção no painel **Uso e custos estimados** (isso está descrito em mais detalhes mais adiante neste artigo).
    
    Removemos a restrição de alguns tipos de assinatura com crédito que não pôde ser usado no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, a caixa de diálogo de limite diário teria instruções sobre como remover esse limite e permitir que ele fosse aumentado para mais de 32,3 MB/dia.
    
* **Limitação**: Esta opção limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação. O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta enviá-los novamente. Ele espalha um surto por vários minutos. Se o seu aplicativo enviar dados acima da taxa de limitação constantemente, alguns dados serão descartados. (Os SDKs do ASP.NET, Java e JavaScript tentam reenviar dados dessa maneira; outros SDKs poderão simplesmente descartar dados limitados.) Caso ocorra uma limitação, um aviso de notificação o alertará de que isso ocorreu.

## <a name="reduce-your-data-volume"></a>Reduzir o volume de dados

Veja abaixo o que é possível fazer para reduzir o volume de dados:

* Use a [Amostragem](../../azure-monitor/app/sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer sua métrica. Você não perderá a capacidade de navegar entre os itens relacionados em Pesquisar. Em aplicativos de servidor, a amostragem funciona automaticamente.
* [Limite o número de chamadas do Ajax que podem ser informadas](../../azure-monitor/app/javascript.md#configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* [Edite Applicationinsights](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar os módulos de coleção desnecessários. Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Divida a telemetria em chaves de instrumentação separadas. 
* Métricas de pré-agregação. Se tiver feito chamadas para o TrackMetric no seu aplicativo, você poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar seu volume máximo de dados diário

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo no restante do dia. *Não é aconselhável* deixar o aplicativo atingir o limite diário. Não será possível rastrear a integridade e o desempenho do seu aplicativo após ele atingir o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", no caso de seu aplicativo inesperadamente começar a enviar volumes muito mais altos de telemetria.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Examine Application Insights uso e custos estimados para entender a tendência de ingestão de dados e qual é o limite de volume diário a ser definido. Considere-o com cuidado, pois não será possível monitorar os recursos depois que o limite for alcançado. 

### <a name="set-the-daily-cap"></a>Definir o limite diário

Para alterar o limite diário, na seção **Configurar** do recurso de Application insights, na página **uso e custos estimados** , selecione **limite diário**.

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

Para [alterar o limite diário por meio de Azure Resource Manager](../../azure-monitor/app/powershell.md), a propriedade a ser alterada é a `dailyQuota`.  Por meio de Azure Resource Manager você também pode definir o `dailyQuotaResetTime` e o @no__t do limite diário-1. 

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

A retenção padrão para recursos de Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso de Application Insights. O conjunto completo de períodos de retenção disponíveis é 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. 

Para alterar a retenção, de seu Application Insights recurso, vá para a página **uso e custos estimados** e selecione a opção de **retenção de dados** :

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-005.png)

A retenção também pode ser [definida programaticamente usando o PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/powershell/set-the-data-retention) usando o parâmetro `retentionInDays`. Além disso, se você definir a retenção de dados para 30 dias, poderá disparar uma limpeza imediata de dados mais antigos usando o parâmetro `immediatePurgeDataOn30Days`, que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade de limpeza só é exposta por meio do ARM e deve ser usada com extrema atenção. 

Quando a cobrança começa para uma retenção mais longa no início de dezembro de 2019, os dados mantidos por mais de 90 dias serão cobrados como a mesma taxa que é cobrada atualmente para a retenção de dados do Azure Log Analytics. Saiba mais na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Mantenha-se atualizado sobre o progresso da retenção de variáveis [votando essa sugestão](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="data-transfer-charges-using-application-insights"></a>Cobranças de transferência de dados usando Application Insights

Enviar dados para Application Insights pode incorrer em encargos de largura de banda de dados. Conforme descrito na [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/), a transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída na taxa normal. A transferência de dados de entrada é gratuita. No entanto, esse encargo é muito pequeno (alguns%) em comparação com os custos de ingestão de dados de log Application Insights. Consequentemente, controlar os custos de Log Analytics precisa se concentrar no volume de dados ingerido e temos diretrizes para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).   

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar os emails de limite diário

Para desabilitar os emails de limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**. Há configurações para enviar email quando o limite é alcançado, bem como quando um nível de aviso ajustável é atingido. Se você quiser desabilitar todos os emails relacionados a volume de limite diário desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Tipo de preço corporativo herdado (por nó)

Para os pioneiros do Aplicativo Azure insights, ainda existem dois tipos de preço possíveis: Básico e Enterprise. O tipo de preço básico é o mesmo descrito acima e é a camada padrão. Ele inclui todos os recursos da camada Enterprise, sem custo adicional. A camada básica cobra principalmente do volume de dados que é ingerido. 

> [!NOTE]
> Esses tipos de preço herdados foram renomeados. O tipo de preço Enterprise agora é chamado **por nó** e o tipo de preço básico agora é chamado **por GB**. Esses novos nomes são usados abaixo e na portal do Azure.  

A camada por nó (anteriormente Enterprise) tem um encargo por nó e cada nó recebe uma concessão de dados diária. No tipo de preço por nó, você será cobrado pelos dados ingeridos acima da bonificação incluída. Se você estiver usando o Operations Management Suite, deverá escolher a camada por nó. 

Para preços atuais em sua moeda e região, consulte [Preços do Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como a [avaliar o impacto de migrar para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) com base nos seus padrões de uso e [como aceitar o novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Direitos por camada de nó e de assinatura do Operations Management Suite

Os clientes que comprarem o Operations Management Suite E1 e E2 poderão obter Application Insights por nó como um componente adicional sem custo adicional como [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade do Operations Management Suite E1 e E2 inclui um direito a um nó do Application Insights por camada de nó. Cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado de ingestão de dados do Log Analytics), com a retenção de dados de 90 dias sem nenhum custo adicional. A camada é descrita mais detalhadamente mais adiante neste artigo. 

Como essa camada é aplicável somente a clientes com uma assinatura do Operations Management Suite, os clientes que não têm uma assinatura do Operations Management Suite não veem uma opção para selecionar essa camada.

> [!NOTE]
> Para garantir que você obtenha esse direito, seus recursos de Application Insights devem estar no tipo de preço por nó. Esse direito se aplica apenas como nós. Application Insights recursos na camada por GB não percebem nenhum benefício. Esse direito não será visível nos custos estimados mostrados no painel **Uso e custo estimado**. Além disso, se você mover uma assinatura para o novo modelo de preços de monitoramento do Azure em abril de 2018, a camada por GB será a única camada disponível. Migrar uma assinatura para o novo modelo de preços de monitoramento do Azure não será recomendado se você tiver uma assinatura do Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Como a camada por nó funciona

* Você paga por cada nó que envia telemetria para todos os aplicativos na camada por nó.
  * Um *nó* é um computador de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda o aplicativo.
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
  * Se você estiver usando uma nova versão do SDK com um aplicativo que executa a partir de computadores clientes ou dispositivos móveis, a contagem de nós poderá retornar um número muito grande (devido ao grande número de computadores clientes ou dispositivos móveis). 

## <a name="automation"></a>Automação

Você pode escrever um script para definir o tipo de preço usando o gerenciamento de recursos do Azure. [Saiba como](powershell.md#price).


## <a name="next-steps"></a>Próximas etapas

* [Amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/