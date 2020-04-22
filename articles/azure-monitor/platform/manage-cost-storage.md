---
title: Gerencie o uso e os custos dos logs do Monitor do Azure | Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar a política de volume e retenção de dados para o espaço de trabalho do Log Analytics no Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686568"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerencie o uso e os custos com logs do Monitor do Azure

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para o Azure Monitor Logs. Um artigo relacionado, [o uso do monitoramento e os custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrevem como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Todos os preços e custos mostrados neste artigo são, por exemplo, apenas para fins. 

O Azure Monitor Logs foi projetado para dimensionar e suportar a coleta, indexação e armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou implantadono Azure.  Embora isso possa ser um driver primário para a organização, a eficiência de custo é, em última instância, o driver subjacente. Para isso, é importante entender que o custo de um espaço de trabalho do Log Analytics não se baseia apenas no volume de dados coletados, também depende do plano selecionado e de quanto tempo você escolheu armazenar dados gerados a partir de suas fontes conectadas.  

Neste artigo, analisamos como você pode monitorar proativamente o volume de dados ingeridos e o crescimento do armazenamento, e definir limites para controlar esses custos associados. 

## <a name="pricing-model"></a>Modelo de preços

O preço padrão para o Log Analytics é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para maior retenção de dados. O volume de dados é medido como o tamanho dos dados que serão armazenados. Cada espaço de trabalho do Log Analytics é cobrado como um serviço separado e contribui para a conta da sua assinatura do Azure. A quantidade de ingestão de dados pode ser considerável dependendo dos seguintes fatores: 

  - Número de soluções de gerenciamento habilitadas e sua configuração (por exemplo, 
  - Número de VMs monitorados
  - Tipo de dados coletados de cada VM monitorada 
  
Além do modelo Pay-As-You-Go, o Log Analytics possui níveis **de reserva de capacidade** que permitem economizar até 25% em comparação com o preço pay-as-you-go. O preço de reserva de capacidade permite que você compre uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado na taxa Pay-As-You-Go. Os níveis de Reserva de Capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, você pode mudar para um nível de reserva de capacidade de nível mais alto (que reiniciará o período de compromisso de 31 dias), mas você não pode voltar para o Pay-As-You-Go ou para um nível de reserva de capacidade mais baixo até que o período de compromisso seja concluído. O faturamento dos níveis de Reserva de Capacidade é feito diariamente. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços de reserva de pagamento e reserva de capacidade do Log Analytics. 

Em todos os níveis de preços, o volume de dados é calculado a partir de uma representação de seqüência dos dados à medida que ele está preparado para ser armazenado. Várias [propriedades comuns a todos os tipos de](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) dados `_ResourceId` `_ItemId`não `_IsBillable` estão incluídas no cálculo do tamanho do evento, incluindo , e `_BilledSize`.

Além disso, observe que algumas soluções, como [o Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) e o [Azure Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)têm seu próprio modelo de preços. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerenciar seu ambiente 

Se você ainda não estiver usando o Azure Monitor Logs, você pode usar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo de usar o Log Analytics. Comece digitando "Azure Monitor" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Role a página para o Azure Monitor e selecione Log Analytics na isla de tipo.  Aqui você pode inserir o número de VMs e o GB de dados que você espera coletar de cada VM. Normalmente, 1 a 3 GB de dados mês é ingerido a partir de uma VM Azure típica. Se você já está avaliando o Azure Monitor Logs, você pode usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o [volume de dados que seu espaço de trabalho está ingerindo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e estime custos

Se você está usando o Azure Monitor Logs agora, é fácil entender quais são os custos provavelmente baseados em padrões de uso recentes. Para isso, use **o Uso do Log Analytics e os Custos Estimados** para revisar e analisar o uso de dados. Isso mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa de seus custos com base na quantidade de dados ingeridos e qualquer retenção adicional além do valor incluído.

![Uso e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar seus dados com mais detalhes, clique no ícone no canto superior direito de qualquer um dos gráficos na página **Uso e custos estimados**. Agora você pode trabalhar com essa consulta para explorar mais detalhes do seu uso.  

![Exibir logs](media/manage-cost-storage/logs.png)

Da página**uso e custos estimados**, pode-se examinar o seu volume de dados para o mês. Isso inclui todos os dados recebidos e retidos no espaço de trabalho do Log Analytics.  Clique **em Detalhes** de uso no topo da página, para visualizar o painel de uso com informações sobre tendências de volume de dados por fonte, computadores e oferta. Para exibir e definir um limite diário ou modificar o período de retenção, clique em **Gerenciamento de volume de dados**.
 
Os encargos do Log Analytics são adicionadas à sua fatura do Azure. É possível ver os detalhes da fatura do Azure na seção Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Exibindo o uso do Log Analytics na sua conta do Azure 

O Azure oferece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade "Análise de custos" permite que você visualize seus gastos com recursos do Azure. Adicionar um filtro por tipo de recurso (ao microsoft.operationalinsights/workspace for Log Analytics) permitirá que você rastreie seus gastos.

Mais compreensão do seu uso pode ser obtida [baixando seu uso do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada você pode ver o uso por recurso do Azure (por exemplo, espaço de trabalho do Log Analytics) por dia. Nesta planilha do Excel, o uso dos espaços de trabalho do Log Analytics pode ser encontrado primeiro filtrando na coluna "Categoria de medidor" para mostrar "Insights e Analytics" (usado por alguns dos níveis de preços legados) e "Log Analytics", e, em seguida, adicionar um filtro na coluna "Instance ID" que é "contém espaço de trabalho". O uso é mostrado na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua conta do Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Para alterar o nível de preços do Log Analytics do seu espaço de trabalho, 

1. No portal Azure, abra **o uso e os custos estimados** do seu espaço de trabalho, onde você verá uma lista de cada um dos níveis de preços disponíveis para este espaço de trabalho.

2. Revise os custos estimados para cada uma das camadas de preços. Esta estimativa é baseada nos últimos 31 dias de uso, então essa estimativa de custo depende dos últimos 31 dias serem representativos do seu uso típico. No exemplo abaixo, você pode ver como, com base nos padrões de dados dos últimos 31 dias, esse espaço de trabalho custaria menos no nível Pay-As-You-Go (#1) em comparação com o nível de reserva de capacidade de 100 GB/dia (#2).  

    ![Tipos de preço](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Depois de revisar os custos estimados com base nos últimos 31 dias de uso, se você decidir alterar o nível de preços, clique **em Selecionar**.  

Você também pode [definir o nível de preços via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o `sku` parâmetro (no`pricingTier` modelo do Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Tipos de preço legados

As assinaturas que possuíam um recurso de espaço de trabalho log analytics ou de insights de aplicativos nele antes de 2 de abril de 2018, ou estão vinculadas a um Contrato Empresarial que começou antes de 1º de fevereiro de 2019, continuarão a ter acesso ao uso dos níveis de preços legados: **Livre,** **Autônomo (Por GB)** e **Por Nó (OMS).**  Os espaços de trabalho no nível de preços livres terão ingestão diária de dados limitada a 500 MB (exceto para os tipos de dados de segurança coletados pelo Azure Security Center) e a retenção de dados é limitada a 7 dias. A camada de preços livres destina-se apenas para fins de avaliação. Os espaços de trabalho nos níveis de preços Autônomo ou Por Nó têm retenção configurável pelo usuário de 30 a 730 dias.

O nível de preço por nó cobra por VM monitor (nó) em uma hora de granularidade. Para cada nó monitorado, o espaço de trabalho é alocado 500 MB de dados por dia que não são cobrados. Essa alocação é agregada ao nível do espaço de trabalho. Os dados ingeridos acima da alocação diária de dados agregados são cobrados por GB como excesso de dados. Observe que, em sua conta, o serviço será **Insight e Analytics** para uso do Log Analytics se o espaço de trabalho estiver no nível de preços por nó. 

> [!TIP]
> Se o seu espaço de trabalho tiver acesso ao nível de preços **por nó,** mas você está se perguntando se ele custaria menos em um nível Pay-As-You-Go, você pode [usar a consulta abaixo](#evaluating-the-legacy-per-node-pricing-tier) para obter facilmente uma recomendação. 

Os espaços de trabalho criados antes de abril de 2016 também podem acessar os níveis de preços **padrão** e **premium** originais que têm retenção de dados fixos de 30 e 365 dias, respectivamente. Novos espaços de trabalho não podem ser criados nos níveis de preços **Padrão** ou **Premium** e, se um espaço de trabalho for movido para fora desses níveis, ele não poderá ser movido para trás. 

Mais detalhes sobre as limitações do nível de preços estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Para usar os direitos provenientes da aquisição de OMS E1 Suite, OMS E2 Suite OMS ou Complemento do OMS para System Center, escolha o tipo de preço *Por Nó* do Log Analytics.

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

As etapas a seguir descrevem como configurar por quanto tempo os dados de log são mantidos no workspace. A retenção de dados pode ser configurada de 30 a 730 dias (2 anos) para todos os espaços de trabalho, a menos que eles estejam usando o nível de preços livre legado. 

### <a name="default-retention"></a>Retenção padrão

Para definir a retenção padrão do seu espaço de trabalho, 
 
1. No portal Azure, a partir do seu espaço de trabalho, selecione **Uso e custos estimados** do painel esquerdo.
2. Na página **Uso e estimativa de custos**, clique em **Gerenciamento de volume de dados** na parte superior da página.
3. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e, em seguida, clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.

    ![Alterar a configuração de retenção de dados do workspace](media/manage-cost-storage/manage-cost-change-retention-01.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes que os dados mais antigos sejam removidos. 
    
A retenção também pode ser [definida via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o `retentionInDays` parâmetro. Além disso, se você definir a retenção de dados para 30 `immediatePurgeDataOn30Days` dias, você pode desencadear uma eliminação imediata de dados mais antigos usando o parâmetro, o que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade só é exposta via Azure Resource Manager. 

Dois tipos de `Usage` `AzureActivity` dados - e - são retidos por 90 dias por padrão, e não há cobrança para esta retenção de 90 dias. Esses tipos de dados também estão livres de taxas de ingestão de dados. 



### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar diferentes configurações de retenção para tipos de dados individuais de 30 a 730 dias (exceto para espaços de trabalho no nível de preços livre legado). Cada tipo de dados é um sub-recurso do espaço de trabalho. Por exemplo, a tabela SecurityEvent pode ser endereçada no [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Observe que o tipo de dados (tabela) é sensível a maiúsculas e minúsculas.  Para obter as configurações atuais de retenção por tipo de dados de um determinado tipo de dados (neste exemplo SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Para obter as configurações atuais de retenção por tipo de dados para todos os tipos de dados em seu espaço de trabalho, basta omitir o tipo de dados específico, por exemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para definir a retenção de um determinado tipo de dados (neste exemplo SecurityEvent) para 730 dias, faça

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Os valores `retentionInDays` válidos são de 30 a 730.

Os `Usage` `AzureActivity` tipos e dados não podem ser definidos com retenção personalizada. Eles assumirão o máximo da retenção padrão do espaço de trabalho ou 90 dias. 

Uma ótima ferramenta para se conectar diretamente ao Azure Resource Manager para definir a retenção por tipo de dados é a ferramenta [OSS ARMclient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre armclient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Aqui está um exemplo usando armClient, definindo dados do SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> A definição de retenção em tipos de dados individuais pode ser usada para reduzir seus custos de retenção de dados.  Para dados coletados a partir de outubro de 2019 (quando esse recurso foi lançado), reduzir a retenção para alguns tipos de dados pode reduzir seu custo de retenção ao longo do tempo.  Para dados coletados anteriormente, definir uma retenção menor para um tipo individual não afetará seus custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie seu volume máximo de dados diários

É possível configurar um limite diário e limitar a ingestão diária para o workspace, mas seja cuidadoso, pois sua meta não deve ser atingir o limite diário.  Caso contrário, você perderá os dados no restante do dia, o que pode afetar outros serviços e soluções do Azure cuja funcionalidade pode depender de dados atualizados no workspace.  Como resultado, sua capacidade de observar e receber alertas quando as condições de integridade dos recursos que dão suporte a serviços de TI forem afetadas.  O limite diário deve ser usado como uma maneira de gerenciar o aumento inesperado do volume de dados de seus recursos gerenciados e ficar dentro do seu limite, ou quando você quiser limitar as taxas não planejadas para o seu espaço de trabalho.  

Logo após o limite diário ser atingido, a coleta de tipos de dados faturados pára pelo resto do dia. (A latência inerente à aplicação da tampa diária pode significar que a tampa não é aplicada como precisamente o nível de tampa diária especificado.) Um banner de aviso é exibido na parte superior da página para o espaço de trabalho log analytics selecionado e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement.** A coleta de dados é retomada após o tempo de redefinição definido em *O limite diário será definido em*. É recomendável definir uma regra de alerta com base nesse evento de operação, configurada para notificar quando o limite de dados diários for alcançado. 

> [!WARNING]
> O limite diário não impede a coleta de dados do Azure Security Center, exceto para espaços de trabalho nos quais o Azure Security Center foi instalado antes de 19 de junho de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Analise [Uso do Log Analytics e custos estimados](usage-estimated-costs.md) para reconhecer a tendência de ingestão de dados e qual é o limite diário de volume a ser definido. Ele deve ser considerado com cuidado, já que você não poderá monitorar seus recursos após o limite ser atingido. 

### <a name="set-the-daily-cap"></a>Defina o Daily Cap

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que o espaço de trabalho do Log Analytics ingerirá por dia.  

1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e custos estimados** para o workspace selecionado, clique em **Gerenciamento de volume de dados** na parte superior da página. 
3. A tampa diária é **OFF** por padrão clique **em ON** para habilitá-lo e, em seguida, defina o limite de volume de dados em GB/dia.

    ![Configuração do limite de dados do Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerta quando o Daily Cap chegou

Embora uma indicação visual seja apresentada no Portal do Azure quando o limite de dados é alcançado, esse comportamento não alinha-se necessariamente à maneira como você gerencia problemas operacionais que exigem atenção imediata.  Para receber uma notificação de alerta, é possível criar uma nova regra de alerta no Azure Monitor.  Para saber mais, [veja como criar, visualizar e gerenciar alertas.](alerts-metric.md)

Para começar, aqui estão as configurações recomendadas para o alerta:

- Destino: selecione o recurso do Log Analytics
- Critérios: 
   - Nome do sinal: pesquisa de registro personalizada
   - Consulta de pesquisa: operação | onde o Detalhe tem 'OverQuota'
   - Baseado em: número de resultados
   - Condição: maior do que
   - Limite: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra de alerta: limite de dados diários alcançado
- Gravidade: aviso (Grav 1)

Quando o alerta é definido e o limite é alcançado, um alerta é disparado e executa a resposta definida no Grupo de Ações. Ele pode notificar a equipe por email e mensagens de texto, ou automatizar ações usando webhooks, runbooks de Automação ou [integrando com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado

O uso aumenta devido a uma ou mais das seguintes causas:
- Mais nós do que o esperado enviando dados para o espaço de trabalho do Log Analytics
- Mais dados do que o esperado sendo enviados para o espaço de trabalho do Log Analytics (talvez devido ao início de usar uma nova solução ou uma alteração de configuração para uma solução existente)

## <a name="understanding-nodes-sending-data"></a>Entendendo os nós que enviam dados

Para entender o número de nós relatando batimentos cardíacos do agente todos os dias no último mês, use

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Obter uma contagem de nós que enviam dados nas últimas 24 horas use a consulta: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Para obter uma lista de nós que enviam quaisquer dados (e a quantidade de dados enviados por cada) a consulta a seguir pode ser usada:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar. Esta consulta substitui a maneira antiga de consultar informações por computador pelo tipo de dados de uso.  

## <a name="understanding-ingested-data-volume"></a>Entendendo o volume de dados ingeridos

Na página **Uso e custos estimados**, o gráfico *Ingestão de dados por solução* mostra o volume total de dados enviados e quanto está sendo enviado por cada solução. Isso permite determinar tendências, como se o uso geral de dados (ou uso por uma solução específica) está crescendo, permanecendo estável ou diminuindo. 

### <a name="data-volume-for-specific-events"></a>Volume de dados para eventos específicos

Para analisar o tamanho dos dados ingeridos para um determinado conjunto de eventos, `Event`você pode consultar a tabela específica (neste exemplo) e, em seguida, restringir a consulta aos eventos de interesse (neste exemplo, iD de evento 5145 ou 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Observe que `where IsBillable = true` a cláusula filtra os tipos de dados de determinadas soluções para as quais não há cobrança de ingestão. 

### <a name="data-volume-by-solution"></a>Volume de dados por solução

A consulta usada para visualizar o volume de dados faturados por solução ao longo do último mês (excluindo o último dia parcial) é:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

A cláusula `TimeGenerated` com é apenas para garantir que a experiência de consulta no portal Azure irá olhar para trás além do padrão 24 horas. Ao usar o tipo `StartTime` `EndTime` de dados de uso e representar os baldes de tempo para os quais os resultados são apresentados. 

### <a name="data-volume-by-type"></a>Volume de dados por tipo

Você pode perfurar mais para ver as tendências de dados para por tipo de dados:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Ou para ver uma tabela por solução e digitar para o último mês,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

O `Usage` tipo de dados não inclui informações no nível mais completo. Para ver o **tamanho** dos dados ingeridos por computador, use a `_BilledSize` [propriedade](log-standard-properties.md#_billedsize), que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [propriedade](log-standard-properties.md#_isbillable) especifica se os dados ingeridos incorrerão em encargos.

Para ver a **contagem** de eventos faturados ingeridos por computador, use 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recurso, grupo de recursos ou assinatura do Azure

Para dados de nódulos hospedados no Azure, você pode obter o **tamanho** dos dados ingeridos __por computador,__ usar a [propriedade](log-standard-properties.md#_resourceid)_ResourceId, que fornece o caminho completo para o recurso:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para dados de nódulos hospedados no Azure, você pode obter o **tamanho** dos `_ResourceId` dados ingeridos por assinatura do __Azure,__ analisar a propriedade como:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A `subscriptionId` `resourceGroup` alteração mostrará o volume de dados ingerido pelo grupo de recursos Do Zure. 

> [!WARNING]
> Alguns dos campos do tipo de dados Uso, ainda no esquema, foram reprovados e seus valores não serão mais preenchidos. Estes são **Computador**, bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Consultando tipos de dados comuns

Para aprofundar a fonte de dados de um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução do **Gerenciamento de log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Dicas para reduzir o volume de dados

Algumas sugestões para reduzir o volume de logs coletados incluem:

| Origem do alto volume de dados | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança mínima ou comuns](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Alterar a política de auditoria de segurança para coletar somente eventos necessários. Em particular, examine a necessidade para coletar eventos para <br> - [auditoria de plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditoria de registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditoria de sistema de arquivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditoria de objeto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditoria de manipulação de identificador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento removível |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](data-sources-performance-counters.md) para: <br> - Reduzir a frequência de coleta <br> - Reduzir o número de contadores de desempenho |
| Logs de eventos                 | Altere a [configuração de log de eventos](data-sources-windows-events.md) para: <br> - Reduzir o número de logs de eventos coletados <br> - Coletar somente níveis de eventos necessários. Por exemplo, não colete eventos de nível *informações* |
| syslog                     | Altere a [configuração do syslog](data-sources-syslog.md) para: <br> - Reduzir o número de instalações coletadas <br> - Coletar somente níveis de eventos necessários. Por exemplo, não coletar eventos de nível *Informações* e *Depurar* |
| AzureDiagnostics           | Altere a coleção de logs do recurso para: <br> - Reduzir o número de logs de envio de recursos para o Log Analytics <br> - Coletar somente os logs necessários |
| Dados da solução de computadores que não precisam da solução | Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Obtendo nós como cobrados na camada de preços por nó

Para obter uma lista de computadores que serão cobrados como nós se o espaço de trabalho estiver no nível de preços legado por Nó, procure nós que estão enviando tipos de **dados faturados** (alguns tipos de dados são gratuitos). Para isso, use `_IsBillable` a [propriedade](log-standard-properties.md#_isbillable) e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a contagem de computadores com dados faturados por hora (que é a granularidade na qual os nós são contados e cobrados):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obtendo contagens de nó de segurança e automação

Se você estiver no nível de preço "Por nó (OMS)", será cobrado com base no número de nós e soluções usados, o número de nós do Insights e do Analytics para os quais você está sendo faturado será mostrado na tabela do **Uso e custo estimado**.  

Para ver o número de nós de segurança distintos, você pode usar a consulta:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Para ver o número de nós de automação distintos, use a consulta:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Avaliando o nível de preços legado por nó

A decisão de saber se os espaços de trabalho com acesso ao nível de preços **legados do Por Nó** são melhores nesse nível ou em um nível atual **de Pay-As-You-Go** ou **Reserva de Capacidade** é muitas vezes difícil para os clientes avaliarem.  Isso envolve entender o trade-off entre o custo fixo por nó monitorado na camada de preços por nó e sua alocação de dados incluída de 500 MB/nó/dia e o custo de apenas pagar por dados ingeridos no nível Pay-As-You-Go (Per GB). 

Para facilitar essa avaliação, a seguinte consulta pode ser usada para fazer uma recomendação para o nível ideal de preços com base nos padrões de uso de um espaço de trabalho.  Esta consulta analisa os nódulos e dados monitorados ingeridos em um espaço de trabalho nos últimos 7 dias, e para cada dia avalia qual nível de preços teria sido ideal. Para usar a consulta, você precisa especificar se o espaço de `workspaceHasSecurityCenter` `true` trabalho `false`está usando o Azure Security Center definindo ou , e então (opcionalmente) atualizando os preços por nó e por GB que seu organizaiton recebe. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Esta consulta não é uma replicação exata de como o uso é calculado, mas funcionará para fornecer recomendações de nível de preços na maioria dos casos.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Crie um alerta quando a coleta de dados for alta

Este artigo descreve como criar um alerta quando:
- O volume de dados excede uma quantidade definida.
- A previsão do volume de dados excede uma quantidade definida.

Os alertas do Azure oferecem suporte a [alertas de log](alerts-unified-log.md) que usam consultas de pesquisa. 

A consulta abaixo tem um resultado quando há mais de 100 GB de dados coletados nas últimas 24 horas:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

A consulta abaixo usa uma fórmula simples para prever quando mais de 100 GB de dados serão enviados em um mesmo dia: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Para receber um alerta em caso de volume de dados diferente, altere o número 100 nas consultas para o número de GB que deve disparar um alerta.

Use as etapas descritas em [criar um novo alerta de log](alerts-metric.md) para ser notificado quando a coleta de dados for maior que a esperada.

Ao criar o alerta para a primeira consulta, quando há mais de 100 GB de dados em 24 horas, defina:  

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** **seleciona Pesquisa de log personalizado**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite ** de *0*
   - **Período de tempo** de *1440* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao criar o alerta para a segunda consulta, quando existe a previsão de que haverá mais de 100 GB de dados em 24 horas, defina:

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** **seleciona Pesquisa de log personalizado**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite ** de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Previsão de volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao receber um alerta, use as etapas na seção a seguir para solucionar problemas relativos ao uso acima do esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Taxas de transferência de dados usando o Log Analytics

O envio de dados para o Log Analytics pode incorrer em taxas de largura de banda de dados. Conforme descrito na página de preços de largura de banda do [Azure,](https://azure.microsoft.com/pricing/details/bandwidth/)transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, essa carga é muito pequena (poucos %) em comparação com os custos de ingestão de dados do Log Analytics. Consequentemente, controlar os custos do Log Analytics precisa se concentrar no seu volume de dados ingerido, e temos orientação para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solucionar problemas se o Log Analytics não está mais coletando dados

Se você estiver usando o tipo de preço gratuito herdado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida pelo restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.  O Log Analytics cria um evento de tipo Operação quando a coleta de dados inicia e para. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e perdendo dados: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a coleta de dados é parada, o Status da Operação é **Aviso**. Quando a coleta de dados é iniciada, o OperationStatus é **bem sucedido**. A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:  

|Motivo para a interrupção da coleta| Solução| 
|-----------------------|---------|
|Limite diário de tipo de preço gratuito herdado atingido |Aguarde até o dia seguinte para que a coleta seja reiniciada automaticamente ou altere para um tipo de preço pago.|
|O limite diário do seu workspace foi atingido|Aguarde para a coleta ser reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em Gerenciar o volume máximo de dados diário. O tempo de redefinição de limite diário é exibido na página **Gerenciamento de volume de dados**. |
|Assinatura do Azure está em um estado suspenso devido a:<br> A avaliação gratuita terminou<br> O Azure Pass expirou<br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)|Converter para uma assinatura paga<br> Remova o limite ou espere o limite ser redefinido|

Para ser notificado quando a coleta de dados parar, use as etapas descritas no *Create daily data cap* alert para ser notificado quando a coleta de dados parar. Use as etapas descritas no [criar um grupo](action-groups.md) de ação para configurar uma ação de e-mail, webhook ou runbook para a regra de alerta. 

## <a name="limits-summary"></a>Resumo de limites

Existem alguns limites adicionais do Log Analytics, alguns dos quais dependem do nível de preços do Log Analytics. Estes estão documentados [aqui.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)


## <a name="next-steps"></a>Próximas etapas

- Consulte [log pesquisas no Azure Monitor Logs](../log-query/log-query-overview.md) para saber como usar o idioma de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
- Use as etapas descritas em [criar um alerta de log](alerts-metric.md) para ser notificado quando um critério de pesquisa for atendido.
- Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
- Para configurar uma política de coleta de eventos eficaz, confira a [Política de filtragem da Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).
- Alterar [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as configurações da coleção de eventos, revise a [configuração do registro de eventos](data-sources-windows-events.md).
- Para modificar as configurações de coleta do syslog, revise a [configuração do syslog](data-sources-syslog.md).