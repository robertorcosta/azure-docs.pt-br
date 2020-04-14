---
title: Gerenciar o uso e os custos do Azure Application Insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ecd0ffd76650efff3a4c9f877522cba6f28d080
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271107"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para insights de aplicativos.  Um artigo relacionado, [o uso do monitoramento e os custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrevem como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços.

O Application Insights foi projetado para obter tudo o que você precisa para monitorar a disponibilidade, o desempenho e o uso de seus aplicativos web, sejam eles hospedados no Azure ou no local. O Application Insights suporta linguagens e frameworks populares, como .NET, Java e Node.js, e se integra com processos e ferramentas de DevOps como Azure DevOps, Jira e PagerDuty. É importante entender o que determina os custos de monitoramento de suas aplicações. Neste artigo, revisamos o que impulsiona os custos de monitoramento do seu aplicativo e como você pode monitorá-los e controlá-los proativamente.

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, você poderá postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Modelo de preços

O preço do [Azure Application Insights][start] é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para maior retenção de dados. Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo. Não há cobrança de volume de dados para o uso do [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

Há uma cobrança adicional para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). Testes na Web de várias etapas se referem a testes na Web que executam uma sequência de ações. Não há nenhuma cobrança separada para *testes de ping* de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada da mesma forma que outras telemetrias do seu aplicativo.

A opção Application Insights para [Permitir o alerta sobre dimensões métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) também pode gerar custos adicionais, pois isso pode resultar na criação de métricas adicionais de pré-agregação. [Saiba mais] sobre métricas baseadas em log e pré-agregados no Application Insights e sobre [preços](https://azure.microsoft.com/pricing/details/monitor/) para métricas personalizadas do Azure Monitor.

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerenciar sua aplicação

Se você ainda não estiver usando o Application Insights, você pode usar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo de usar o Application Insights. Comece digitando "Azure Monitor" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Role a página para o Azure Monitor e selecione Insights de aplicativo na isla de tipo.  Aqui você pode inserir o número de GB de dados que você espera coletar por mês, então a questão é quantos dados o Application Insights coletará monitorando seu aplicativo.

Existem duas abordagens para lidar com isso: o uso de monitoramento padrão e amostragem adaptativa, que está disponível no SDK ASP.NET, ou estimar sua provável ingestão de dados com base no que outros clientes semelhantes viram.

### <a name="data-collection-when-using-sampling"></a>Coleta de dados ao usar amostragem

Com a [amostragem adaptativa](sampling.md#adaptive-sampling)do ASP.NET SDK, o volume de dados é ajustado automaticamente para manter dentro de uma taxa máxima de tráfego especificada para monitoramento padrão do Application Insights. Se o aplicativo produzir uma baixa quantidade de telemetria, como quando depuração ou devido ao baixo uso, os itens não serão descartados pelo processador de amostragem enquanto o volume estiver abaixo dos eventos configurados por segundo nível. Para uma aplicação de alto volume, com o limite padrão de cinco eventos por segundo, a amostragem adaptativa limitará o número de eventos diários a 432.000. Usando um tamanho médio típico de evento de 1 KB, isso corresponde a 13,4 GB de telemetria por 31 dias por mês por nó que hospeda seu aplicativo (uma vez que a amostragem é feita localmente para cada nó.) 

Para SDKs que não suportam amostragem adaptativa, você pode empregar [amostragem de ingesção](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), que mostra quando os dados são recebidos pelo Application Insights com base em uma porcentagem de dados para reter, ou [amostragem de taxa fixa para sites ASP.NET, ASP.NET Core e Java](sampling.md#fixed-rate-sampling) para reduzir o tráfego enviado de seu servidor web e navegadores da Web

### <a name="learn-from-what-similar-customers-collect"></a>Aprenda com o que clientes similares coletam

Na calculadora de preços de monitoramento do Azure para Insights de aplicativos, se você habilitar a funcionalidade "Estimar o volume de dados com base na atividade do aplicativo", você pode fornecer entradas sobre seu aplicativo (solicitações por mês e visualizações de página por mês, no caso de você coletar telemetria do lado do cliente) e, em seguida, a calculadora lhe dirá a quantidade mediana e de 90% de dados coletados por aplicativos semelhantes. Esses aplicativos abrangem a gama de configuração do Application Insights (por exemplo, alguns têm [amostragem](../../azure-monitor/app/sampling.md)padrão, alguns não têm amostragem etc.), então você ainda tem o controle para reduzir o volume de dados que ingere muito abaixo do nível médio usando amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão vendo.

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e estime custos

O Application Insights facilita a compreensão de quais são seus custos com base nos padrões de uso recentes. Para começar, no portal do Azure, do recurso Application Insights, acesse a página **Uso e custos estimados**:

![Escolher preços](./media/pricing/pricing-001.png)

a. Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) de seu servidor e aplicativos cliente e dos testes de disponibilidade.  
B. Uma cobrança separada é feita pelos [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).  
C. Visualize as tendências do volume de dados do último mês.  
D. Habilite a [amostragem](../../azure-monitor/app/sampling.md) de ingestão de dados.
E. Configure o limite de volume de dados diários.  

(Observe que todos os preços exibidos em capturas de tela neste artigo são apenas para fins. Para obter preços atuais em sua moeda e região, consulte [preços do Application Insights][pricing].)

Para investigar mais profundamente o uso do Application Insights, abra a página **Métricas**, adicione a métrica nomeada "Volume do ponto de dados" e selecione a opção *Aplicar divisão* para dividir os dados por "Tipo de item de telemetria".

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure na seção de **Cobrança** do Portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions).

![No menu à esquerda, selecione Cobrança](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Usando métricas de volume de dados
<a id="understanding-ingested-data-volume"></a>

Para saber mais sobre seus volumes de dados, selecionando **Métricas** para o recurso Application Insights, adicione um novo gráfico. Para a métrica do gráfico, em **métricas baseadas em Log,** selecione **Volume de ponto de dados**. Clique **em Aplicar a divisão**e selecione grupo por ** `Telemetryitem` tipo**.

![Use métricas para olhar o volume de dados](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Consultas para entender detalhes do volume de dados

Existem duas abordagens para investigar volumes de dados para insights de aplicativos. O primeiro usa informações `systemEvents` agregadas na tabela, e o segundo usa a `_BilledSize` propriedade, que está disponível em cada evento ingerido.

#### <a name="using-aggregated-data-volume-information"></a>Usando informações agregadas de volume de dados

Por exemplo, você `systemEvents` pode usar a tabela para ver o volume de dados ingerido nas últimas 24 horas com a consulta:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ou para ver um gráfico de volume de dados (em bytes) por tipo de dados nos últimos 30 dias, você pode usar:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Observe que essa consulta pode ser usada em [um Alerta de Log do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) para configurar o alerta sobre volumes de dados.  

Para saber mais sobre as alterações de dados da telemetria, podemos obter a contagem de eventos por tipo usando a consulta:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Usando o tamanho dos dados por informações de evento

Para saber mais detalhes sobre a origem de `_BilledSize` seus volumes de dados, você pode usar a propriedade presente em cada evento ingerido.

Por exemplo, para ver quais operações geram mais volume de dados `_BilledSize` nos últimos 30 dias, podemos somar para todos os eventos de dependência:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Exibindo o uso do Application Insights na sua conta do Azure

O Azure oferece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade "Análise de custos" permite que você visualize seus gastos com recursos do Azure. Adicionar um filtro por tipo de recurso (ao microsoft.insights/componentes para insights de aplicativos) permitirá que você rastreie seus gastos.

Mais compreensão do seu uso pode ser obtida [baixando seu uso do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
Na planilha baixada, você pode ver o uso por recurso do Azure por dia. Nesta planilha do Excel, o uso dos recursos do Application Insights pode ser encontrado primeiro filtrando na coluna "Categoria do medidor" para mostrar "Insights de aplicativos" e "Análise de log", e, em seguida, adicionar um filtro na coluna "Instance ID" que é "contém microsoft.insights/componentes".  A maioria dos usos do Application Insights é relatada em medidores com a categoria medidor de Análise de Log, uma vez que há um backend de logs único para todos os componentes do Azure Monitor.  Apenas os recursos do Application Insights sobre níveis de preços legados e testes web em várias etapas são relatados com uma categoria de medidor de insights de aplicativos.  O uso é mostrado na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua conta do Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)

## <a name="managing-your-data-volume"></a>Gerenciando seu volume de dados

O volume de dados enviados pode ser gerenciado usando as seguintes técnicas:

* **Amostragem:** você pode usar a amostragem para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Amostragem é a ferramenta principal que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre [recursos de amostragem](../../azure-monitor/app/sampling.md).

* **Limite as chamadas do Ajax**: Você pode [limitar o número de chamadas do Ajax que podem ser relatadas](../../azure-monitor/app/javascript.md#configuration) em cada exibição de página, ou desligar a emissão de relatórios do Ajax.

* **Desativar módulos desnecessários**: [Editar ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar módulos de coleta que você não precisa. Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.

* **Métricas pré-agregadas**: Se você colocar chamadas para o TrackMetric em seu aplicativo, você pode reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite diário:** quando você cria um recurso do Application Insights no Portal do Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso do Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. O propósito dele é que o usuário irá gerar o limite diário antes de colocar o aplicativo em produção. 

    O limite máximo é 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de alto tráfego.
    
    Os e-mails de aviso sobre o limite diário são enviados para conta que são membros dessas funções para o seu recurso Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Tome cuidado ao definir o limite diário. A intenção deve ser *nunca atingir o limite diário*. Se atingir o limite diário, você perderá os dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção **Limite de volume diário**. Você pode acessar essa opção no painel **Uso e custos estimados** (isso está descrito em mais detalhes mais adiante neste artigo).
    
    Removemos a restrição de alguns tipos de assinatura com crédito que não pôde ser usado no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, a caixa de diálogo de limite diário teria instruções sobre como remover esse limite e permitir que ele fosse aumentado para mais de 32,3 MB/dia.
    
* **Limitação:** esta opção limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação. O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta enviá-los novamente. Ele espalha um surto por vários minutos. Se o seu aplicativo enviar dados acima da taxa de limitação constantemente, alguns dados serão descartados. (Os SDKs ASP.NET, Java e JavaScript tentam reenviar dados desta forma; outros SDKs podem simplesmente soltar dados estrangulados.) Se ocorrer estrangulamento, um aviso de notificação alerta que isso ocorreu.

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie seu volume máximo de dados diários

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo no restante do dia. *Não é aconselhável* deixar o aplicativo atingir o limite diário. Não será possível rastrear a integridade e o desempenho do seu aplicativo após ele atingir o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", no caso de seu aplicativo inesperadamente começar a enviar volumes muito mais altos de telemetria.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Revisar insights do aplicativo O uso e os custos estimados para entender a tendência de ingestão de dados e qual é o limite de volume diário a definir. Ele deve ser considerado com cuidado, já que você não poderá monitorar seus recursos após o limite ser atingido.

### <a name="set-the-daily-cap"></a>Defina o Daily Cap

Para alterar o limite diário, na seção **Configurar** o recurso 'Crisjustificativa de aplicativos', na página **Deruse e custos estimados,** selecione **'Limite diário**' .

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

Para [alterar o limite diário via Azure Resource Manager,](../../azure-monitor/app/powershell.md)a propriedade a ser trocada é a `dailyQuota`.  Via Azure Resource Manager você `dailyQuotaResetTime` também pode definir `warningThreshold`o e o limite diário .

### <a name="create-alerts-for-the-daily-cap"></a>Crie alertas para o Daily Cap

O Application Insights Daily Cap cria um evento no registro de atividades do Azure quando os volumes de dados ingeridos atingem o nível de aviso ou o nível de limite diário.  Você pode [criar um alerta com base nesses eventos de registro de atividades](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal). Os nomes dos sinais para esses eventos são:

* Limite de aviso de limite diário do componente Application Insights atingido

* A tampa diária do componente Do Aplicativo Insights alcançou

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

A retenção padrão para os recursos do Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso do Application Insights. O conjunto completo de períodos de retenção disponíveis é de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias.

Para alterar a retenção, a partir do recurso Application Insights, vá até a página **Usar e Custos Estimados** e selecione a opção **Retenção de Dados:**

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-005.png)

A retenção também pode ser [definida programaticamente usando o PowerShell](powershell.md#set-the-data-retention) usando o `retentionInDays` parâmetro. Quando a retenção é reduzida, há um período de carência de vários dias antes que os dados mais antigos sejam removidos. Se você definir a retenção de dados para 30 dias, `immediatePurgeDataOn30Days` você pode desencadear uma eliminação imediata de dados mais antigos usando o parâmetro, o que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade de purga só é exposta via Azure Resource Manager e deve ser usada com extremo cuidado. O tempo de reset diário para a tampa do volume de `dailyQuotaResetTime` dados pode ser configurado usando o Azure Resource Manager para definir o parâmetro.

## <a name="data-transfer-charges-using-application-insights"></a>Taxas de transferência de dados usando insights de aplicativos

O envio de dados para o Application Insights pode incorrer em taxas de largura de banda de dados. Conforme descrito na página de preços de largura de banda do [Azure,](https://azure.microsoft.com/pricing/details/bandwidth/)transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, essa carga é muito pequena (poucos %) em comparação com os custos para a ingestão de dados de log do Application Insights. Consequentemente, controlar os custos do Log Analytics precisa se concentrar no seu volume de dados ingerido, e temos orientação para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar os emails de limite diário

Para desabilitar os emails de limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**. Há configurações para enviar email quando o limite é alcançado, bem como quando um nível de aviso ajustável é atingido. Se desejar desativar todos os e-mails relacionados ao volume de tampa diária, descertifique as duas caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Nível de preços do Legacy Enterprise (Por Nó)

Para os primeiros adotantes do Azure Application Insights, ainda existem dois níveis de preços possíveis: Básico e Enterprise. O nível de preços básico é o mesmo descrito acima e é o nível padrão. Ele inclui todos os recursos de nível Enterprise, sem custo adicional. O nível básico diz principalmente sobre o volume de dados que é ingerido.

> [!NOTE]
> Esses níveis de preços legados foram renomeados. O nível de preços da Enterprise agora é chamado **de Por Nó** e o nível de preços básico agora é chamado de Por **GB**. Esses novos nomes são usados abaixo e no portal Azure.  

O nível Por Nó (anteriormente Enterprise) tem uma taxa por nó, e cada nó recebe uma mesada diária de dados. Na camada de preços por nó, você é cobrado por dados ingeridos acima do subsídio incluído. Se você estiver usando o Operations Management Suite, você deve escolher o nível Por Nó.

Para ver os preços atuais em sua moeda e região, consulte [Preços do Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)como [avaliar o impacto da mudança para este modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) com base em seus padrões de uso e [como optar pelo novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Por nível de nó e direitos de assinatura do Operations Management Suite

Os clientes que compram o Operations Management Suite E1 e o E2 podem obter insights de aplicativos por nó como um componente adicional sem custo adicional, conforme [anunciado anteriormente.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) Especificamente, cada unidade do Conjunto de Gerenciamento de Operações E1 e E2 inclui o direito a um nó do nível Application Insights Per Node. Cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado de ingestão de dados do Log Analytics), com a retenção de dados de 90 dias sem nenhum custo adicional. O nível é descrito em mais detalhes mais tarde no artigo.

Como esse nível é aplicável apenas a clientes com uma assinatura do Operations Management Suite, os clientes que não possuem uma assinatura do Operations Management Suite não vêem uma opção para selecionar esse nível.

> [!NOTE]
> Para garantir que você obtenha esse direito, os recursos do Application Insights devem estar no nível de preços por nó. Esse direito se aplica apenas como nós. Os recursos do Application Insights no nível Por GB não realizam nenhum benefício. Esse direito não será visível nos custos estimados mostrados no painel **Uso e custo estimado**. Além disso, se você mover uma assinatura para o novo modelo de preços de monitoramento do Azure em abril de 2018, o nível Por GB é o único nível disponível. Migrar uma assinatura para o novo modelo de preços de monitoramento do Azure não será recomendado se você tiver uma assinatura do Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Como funciona o nível Per Node

* Você paga por cada nó que envia telemetria para quaisquer aplicativos no nível Por Nó.
  * Um *nó* é uma máquina de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda seu aplicativo.
  * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não contam como nós.
  * Se o aplicativo tiver vários componentes que enviam telemetria, como um serviço Web e um trabalhado de back-end, os componentes serão contados separadamente.
  * Os dados de [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contatos para fins de preços. Em uma assinatura, seus encargos são por nó, não por aplicativo. Se você tiver cinco nós que enviam telemetria para 12 aplicativos, o encargo será de cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. O encargo por hora é a cobrança mensal cotada dividida por 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizada não é transportada de um dia para o outro.
  * Se você escolher a camada de preços por nó, cada assinatura recebe uma mesada diária de dados com base no número de nomes que enviam telemetria aos recursos do Application Insights nessa assinatura. Portanto, se você tiver cinco nós que enviam dados todos os dias, você terá uma permissão em pool de 1 GB aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós enviam mais dados que outros nós, porque os dados incluídos são compartilhados entre todos os nós. Se em um determinado dia, os recursos do Application Insights receberem mais dados do que estão incluídos na alocação diária de dados para esta assinatura, as taxas de dados por excesso por GB se aplicam. 
  * A permissão de dados diária é calculada como o número de horas por dia (usando o UTC) que cada nó envia telemetria dividido por 24, multiplicado por 200 MB. Portanto, se você tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia serão ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
  * A subsídio diário por nível de nó não é compartilhada com aplicativos para os quais você escolheu o nível Por GB. A permissão não utilizada não é herdada do dia a dia.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nós distinta

| Cenário                               | Contagem de nós diária total |
|:---------------------------------------|:----------------:|
| 1 aplicativo usando 3 instâncias do Serviço de Aplicativo do Azure e 1 servidor virtual | 4 |
| 3 aplicativos em execução em 2 VMs; os recursos do Application Insights para esses aplicativos estão na mesma assinatura e no nível Por Nó | 2 | 
| 4 aplicativos cujos recursos do Applications Insights estão na mesma assinatura; cada aplicativo executando 2 instâncias durante 16 horas fora de pico e 4 instâncias durante 8 horas de pico | 13.33 |
| Serviços de nuvem com uma função de trabalho e uma função web, cada uma executando duas instâncias | 4 | 
| Um cluster do Azure Service Fabric de 5 nós que executa 50 microsserviços; cada microsserviço executando 3 instâncias | 5|

* A contagem de nós exata depende de qual SDK do Application Insights seu aplicativo está usando. 
  * No SDK versões 2.2 e posterior, o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) do Application Insights e o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatam cada host de aplicativo como um nó. Exemplos são o nome do computador para servidores físicos e hosts de VM ou o nome da instância para serviços de nuvem.  A única exceção é um aplicativo que usa apenas o [.NET Core](https://dotnet.github.io/) e o Application Insights o SDK Core. Nesse caso, apenas um nó é relatado para todos os hosts porque o nome do host não está disponível.
  * Para versões anteriores do SDK, o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta como as versões do SDK mais recentes, mas o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) relata apenas um nó, independentemente do número de hosts do aplicativo.
  * Se o aplicativo usar o SDK para definir **roleInstance** como um valor personalizado, por padrão, esse mesmo valor será usado para determinar a contagem de nós.
  * Se você estiver usando uma nova versão sdk com um aplicativo que funciona a partir de máquinas clientes ou dispositivos móveis, a contagem de nós pode retornar um número que é grande (devido ao grande número de máquinas clientes ou dispositivos móveis).

## <a name="automation"></a>Automação

Você pode escrever um script para definir o nível de preços usando o Azure Resource Management. [Saiba como](powershell.md#price).

## <a name="next-steps"></a>Próximas etapas

* [amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/