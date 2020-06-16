---
title: Gerenciar os custos do Azure com automação
description: Este artigo explica como você pode gerenciar os custos do Azure com automação.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449824"
---
# <a name="manage-costs-with-automation"></a>Gerenciar os custos com a automação

Você pode usar a automação de Gerenciamento de Custos para criar um conjunto personalizado de soluções para recuperar e gerenciar dados de custo. Este artigo aborda cenários comuns de automação de Gerenciamento de Custos e opções disponíveis com base em sua situação. Se você quiser desenvolver usando APIs, exemplos comuns de solicitação de API são apresentados para ajudar a acelerar seu processo de desenvolvimento.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizar a recuperação de dados de custo para análise offline

Talvez seja necessário baixar os dados de custo do Azure para mesclá-los a outros conjuntos de dados. Ou talvez seja necessário integrar os dados de custo a seus próprios sistemas. Há opções diferentes disponíveis, dependendo da quantidade de dados envolvidos. Você deve ter permissões de Gerenciamento de Custos no escopo apropriado para usar APIs e ferramentas em qualquer caso. Para obter mais informações, confira [Atribuir acesso a dados](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestões para lidar com grandes conjuntos de dados

Se sua organização tiver uma grande presença do Azure em muitos recursos ou assinaturas, você terá uma grande quantidade de dados de detalhes de uso. O Excel geralmente não pode carregar arquivos grandes. Nessa situação, recomendamos as seguintes opções:

**Power BI**

O Power BI é usado para ingerir e lidar com grandes quantidades de dados. Se você for um cliente de Contrato Enterprise, poderá usar o aplicativo de modelo do Power BI para analisar os custos da conta de cobrança. O relatório contém as exibições de chave usadas pelos clientes. Para obter mais informações, confira [Analisar os custos do Azure com o aplicativo de modelo do Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Conector de dados do Power BI**

Se você quiser analisar seus dados diariamente, é recomendável usar o [conector de dados do Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) para obter dados para análise detalhada. Todos os relatórios que você criar serão mantidos atualizados pelo conector conforme mais custos forem acumulados.

**Exportações de Gerenciamento de Custos**

Talvez você não precise analisar os dados diariamente. Nesse caso, considere usar o recurso [Exportações](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) do Gerenciamento de Custos para agendar exportações de dados para uma conta de Armazenamento do Azure. Em seguida, você poderá carregar os dados para o Power BI conforme necessário ou analisá-los no Excel, se o arquivo for pequeno o suficiente. As exportações estão disponíveis no portal do Azure ou você pode configurar exportações com a [API de Exportações](https://docs.microsoft.com/rest/api/cost-management/exports).

**API de Detalhes de Uso**

Considere usar a [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usageDetails) se você tiver um pequeno conjunto de dados de custo. Se você tiver uma grande quantidade de dados de custo, solicite a menor quantidade de dados de uso possível para um período. Para fazer isso, especifique um intervalo de tempo pequeno ou use um filtro em sua solicitação. Por exemplo, em um cenário em que você precisa de três anos de dados de custo, a API tem um melhor desempenho quando você faz várias chamadas para diferentes intervalos de tempo, em vez de uma única chamada. Então você pode carregar os dados no Excel para análise posterior.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizar a recuperação com a API de Detalhes de Uso

A [API Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usageDetails) fornece um modo fácil de obter dados de custo brutos e não agregados que correspondem à sua fatura do Azure. A API é útil quando sua organização precisa de uma solução de recuperação de dados programática. Considere usar a API se você pretende analisar conjuntos de dados de custo menores. No entanto, você deve usar outras soluções identificadas anteriormente se tiver conjuntos de dados maiores. Os dados em Detalhes de Uso são fornecidos por medidor, por dia. Eles são usados ao calcular sua fatura mensal. A versão em GA (disponibilidade geral) das APIs é `2019-10-01`. Use `2019-04-01-preview` para acessar a versão de prévia para reservas e compras do Azure Marketplace com as APIs.

### <a name="usage-details-api-suggestions"></a>Sugestões da API de Detalhes de Uso

**Agenda de solicitação**

Recomendamos que você faça _não mais de uma solicitação_ à API de Detalhes de Uso por dia. Para obter mais informações sobre a frequência com que os dados de custo são atualizados e como o arredondamento é tratado, confira [Entender dados de gerenciamento de custos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Direcionar escopos de nível superior sem filtragem**

Use a API para obter todos os dados necessários no escopo de nível mais alto disponível. Aguarde até que todos os dados necessários sejam ingeridos antes de fazer qualquer filtragem, agrupamento ou análise agregada. A API é otimizada especificamente para fornecer grandes quantidades de dados de custo bruto não agregados. Para saber mais sobre escopos disponíveis no Gerenciamento de Custos, confira [Entender e trabalhar com escopos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Depois de baixar os dados necessários para um escopo, use o Excel para analisar ainda mais os dados com filtros e tabelas dinâmicas.

## <a name="example-usage-details-api-requests"></a>Exemplos de solicitações de API de Detalhes de Uso

As solicitações de exemplo a seguir são usadas pelos clientes da Microsoft para abordar os cenários comuns que você pode encontrar.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Obter os Detalhes de Uso para um escopo durante um intervalo de datas específico

Os dados retornados pela solicitação correspondem à data em que o uso foi recebido pelo sistema de cobrança. Ele pode incluir custos de várias notas fiscais.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Obter detalhes de custo amortizado

Se você precisa que os custos reais mostrem as compras conforme elas são efetuadas, altere a *métrica* para `ActualCost` na solicitação a seguir. Para usar os custos amortizados e reais, use a versão `2019-04-01-preview`. A versão atual da API funciona da mesma forma que a versão `2019-10-01`, exceto pelo novo tipo/atributo de métrica e pelos nomes de propriedade alterados. Se você tiver um Contrato de Cliente da Microsoft, seus filtros serão `startDate` e `endDate` no exemplo a seguir.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperar grandes conjuntos de dados de custo de modo recorrente com Exportações

O recurso Exportações é uma solução para agendar despejos de dados de custo regularmente. É a maneira recomendada de recuperar dados de custo não agregados para organizações cujos arquivos de uso podem ser muito grandes para chamar e baixar de modo confiável dados usando a API de Detalhes de Uso. Os dados são colocados em uma conta de Armazenamento do Azure de sua escolha. Então você pode carregá-los em seus próprios sistemas e analisá-los conforme necessário por suas equipes. Para configurar as exportações no portal do Azure, confira [Exportar dados](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Latência de dados e limites de taxa

Recomendamos que você chame as APIs não mais do que uma vez por dia. Os dados de gerenciamento de custos são atualizados a cada quatro horas à medida que novos dados de uso são recebidos dos provedores de recursos do Azure. Chamar com mais frequência não fornecerá dados adicionais. Em vez disso, criará uma carga maior. Para saber mais sobre a frequência com que os dados são alterados e como a latência de dados é manipulada, confira [Entender dados de gerenciamento de custos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Código de erro 429 – a contagem de chamadas excedeu os limites de taxa

Para habilitar uma experiência consistente para todos os assinantes de Gerenciamento de Custos, as APIs de Gerenciamento de Custos são limitadas por taxa. Quando você alcança o limite, recebe o código de status HTTP `429: Too many requests`. Os limites de taxa de transferência atuais para nossas APIs são os seguintes:

- 30 chamadas por minuto – determinado por escopo, por usuário ou por aplicativo.
- 200 chamadas por minuto – determinado por locatário, por usuário ou por aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Analisar os custos do Azure com o aplicativo de modelo do Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Crie e gerencie dados exportados](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) com Exportações.
- Saiba mais sobre a [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usageDetails).