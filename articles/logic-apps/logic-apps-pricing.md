---
title: Modelo de faturamento de & de preços
description: Visão geral sobre como funciona o modelo de preços e faturamento para apps da Azure Logic
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f47c7412bdd5ada1e50d1005b8e740e3f46ffd8d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536226"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para os Aplicativos Lógicos do Azure

[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajuda você a criar e executar fluxos de trabalho de integração automatizados que podem ser dimensionados na nuvem. Este artigo descreve como o faturamento e os preços funcionam para o Azure Logic Apps. Para obter preços, consulte [Logic Apps Pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preço por consumo

Para novos aplicativos lógicos que são executados em público, serviço "global", multi-inquilino Azure Logic Apps, você paga apenas pelo que você usa. Esses aplicativos lógicos usam um plano baseado em consumo e modelo de preços. Em seu aplicativo lógico, cada passo é uma ação, e o Azure Logic Apps mede todas as ações que são executadas em seu aplicativo lógico.

Por exemplo, as ações incluem:

* [Gatilhos,](#triggers)que são ações especiais. Todos os aplicativos lógicos requerem um gatilho como o primeiro passo.

* ["Incorporada" ou ações nativas,](../connectors/apis-list.md#built-in) como HTTP, chamadas para funções do Azure e gerenciamento de API, e assim por diante

* Chamadas para [conectores gerenciados](../connectors/apis-list.md#managed-connectors) como Outlook 365, Dropbox e assim por diante

* [Controle as ações do fluxo de trabalho,](../connectors/apis-list.md#control-workflow) como loops, declarações condicionales, e assim por diante

[Os conectores padrão](../connectors/apis-list.md#managed-connectors) são cobrados pelo [preço do conector Padrão](https://azure.microsoft.com/pricing/details/logic-apps). Os [conectores Enterprise](../connectors/apis-list.md#managed-connectors) geralmente disponíveis são cobrados pelo preço do [conector Enterprise,](https://azure.microsoft.com/pricing/details/logic-apps)enquanto os conectores Corporativos de visualização pública são cobrados pelo [preço do conector Padrão](https://azure.microsoft.com/pricing/details/logic-apps).

Saiba mais sobre como funciona o faturamento nos níveis [de gatilhos](#triggers) e [ações.](#actions) Ou, para obter informações sobre limites, consulte [Limites e configuração para aplicativos azure logic](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma maneira isolada para você criar e executar aplicativos lógicos que podem acessar recursos em uma rede virtual Do Zure. Os aplicativos lógicos que são executados em um ISE não incorrem em custos de retenção de dados. Quando você cria um ISE, e somente durante a criação, você pode escolher um [nível ISE ou "SKU",](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)que têm [diferentes taxas de preços:](https://azure.microsoft.com/pricing/details/logic-apps)

* **Premium** ISE: Esta unidade base do SKU tem capacidade fixa, mas se você precisar de mais throughput, você pode [adicionar mais unidades de escala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante a criação do ISE ou depois. Para limites ISE, consulte [Limites e configuração para aplicativos de lógica do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Desenvolvedor** ISE: Este SKU não tem capacidade para escalar, nenhum contrato de nível de serviço (SLA) e nenhum limite publicado. Use este SKU apenas para experimentos, desenvolvimento e testes, não testes de produção ou desempenho.

Para aplicativos lógicos que você cria e executa em um ISE, você paga um [preço fixo mensal](https://azure.microsoft.com/pricing/details/logic-apps) por esses recursos:

* [Gatilhos](../connectors/apis-list.md#built-in) e ações incorporadas

  Dentro de um ISE, gatilhos e ações incorporados exibem o rótulo **Core** e são executados no mesmo ISE que seus aplicativos lógicos.

* [Conectores](../connectors/apis-list.md#managed-connectors) padrão e conectores [Enterprise,](../connectors/apis-list.md#enterprise-connectors) que permitem que você tenha quantas conexões Enterprise quiser

   Conectores padrão e corporativo que exibem o rótulo **ISE** são executados na mesma ISE que seus aplicativos lógicos. Conectores que não exibem o rótulo ISE executado sao públicos, serviço "global", de aplicativos lógicos multilocatários. Os preços mensais fixos também se aplicam a conectores que são executados no serviço de vários locatários quando você os usa com aplicativos lógicos que são executados em um ISE.

* Uso da [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) sem custo adicional, com base no seu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium** ISE SKU: Uma única conta de integração [de nível padrão](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Desenvolvedor** ISE SKU: Uma única conta de integração [de nível gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Cada ISE SKU é limitado a 5 contas de integração total. Por um custo adicional, você pode ter mais contas de integração, com base no seu ISE SKU:

  * **Premium** ISE SKU: Até quatro contas padrão. Sem contas gratuitas ou básicas.

  * **Desenvolvedor** ISE SKU: Até 4 contas Padrão a mais ou até 5 contas Padrão totais. Sem contas básicas.

  Para obter mais informações sobre os limites da conta de integração, consulte [Limites e configuração para aplicativos de lógica do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Você pode aprender mais sobre [os níveis de conta de integração e seu modelo de preços](#integration-accounts) mais tarde neste tópico.

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Os conectores do Azure Logic Apps ajudam seu aplicativo lógico a acessar aplicativos, serviços e sistemas na nuvem ou no local, fornecendo [gatilhos,](#triggers) [ações](#actions)ou ambos. Os conectores são classificados como Padrão ou Enterprise. Para obter uma visão geral sobre esses conectores, consulte [Conectores para Aplicativos de Lógica Azure](../connectors/apis-list.md). Se não houver conectores pré-construídos disponíveis para as APIs REST que você deseja usar em seus aplicativos lógicos, você poderá criar [conectores personalizados](https://docs.microsoft.com/connectors/custom-connectors), que são apenas invólucros em torno dessas APIs REST. Os conectores personalizados são cobrados como conectores Padrão. As seções a seguir fornecem mais informações sobre como funciona o faturamento dos gatilhos e ações.

<a name="triggers"></a>

## <a name="triggers"></a>Gatilhos

Os gatilhos são ações especiais que criam uma instância de aplicativo lógico quando ocorre um evento específico. Dispara ações de diferentes formas, o que afeta o modo como o aplicativo lógico é monitorado. Aqui estão os vários tipos de gatilhos que existem nos Aplicativos azure Logic:

* **Gatilho de votação**: Este gatilho verifica continuamente um ponto final para mensagens que satisfazem os critérios para criar uma instância lógica do aplicativo e iniciar o fluxo de trabalho. Mesmo quando nenhuma instância de aplicativo lógico é criada, os Aplicativos Lógicos medem cada solicitação de pesquisa como uma execução. Para especificar o intervalo de sondagem, configure o gatilho no Designer de Aplicativos Lógicos.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Gatilho webhook**: Este gatilho espera que um cliente envie uma solicitação para um ponto final específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. Por exemplo, a Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.

* **Gatilho de recorrência**: Este gatilho cria uma instância lógica do aplicativo com base no intervalo de recorrência que você configura no gatilho. Por exemplo, você pode configurar um gatilho de recorrência que é executado a cada três dias ou em um cronograma mais complexo.

<a name="actions"></a>

## <a name="actions"></a>Ações

A Azure Logic Apps tem ações "incorporadas", como http, como ações nativas. Por exemplo, as ações incorporadas incluem chamadas HTTP, chamadas de Funções do Azure ou gerenciamento de API e etapas de fluxo de controle, como condições, loops e instruções de switch. Cada ação tem seu próprio tipo de ação. Por exemplo, as ações que chamam [conectores](https://docs.microsoft.com/connectors) têm o tipo "ApiConnection". Esses conectores são classificados como conectores Padrão ou Enterprise, que são medidos com base em seus [respectivos preços.](https://azure.microsoft.com/pricing/details/logic-apps) Os conectores corporativos na *Visualização* são carregados como conectores Padrão.

A Azure Logic Apps tem todas as ações bem sucedidas e mal sucedidas como execuções. No entanto, o Logic Apps não mede essas ações:

* Ações que são ignoradas devido a condições não atendidas
* Ações que não são executadas porque o aplicativo lógico parou antes de terminar

Para ações que são executadas dentro de loops, o Azure Logic Apps conta cada ação para cada ciclo no loop. Por exemplo, suponha que você tenha um loop "para cada" que processe uma lista. Os aplicativos lógicos medem uma ação nesse loop multiplicando o número de itens da lista pelo número de ações no loop e adiciona a ação que inicia o loop. Assim, o cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos lógicos desativados

Os aplicativos de lógica desativados não são cobrados porque não podem criar novas instâncias enquanto estão desativados. Depois de desativar um aplicativo lógico, qualquer instância em execução no momento pode demorar algum tempo antes de parar completamente.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Um [modelo de preço fixo](https://azure.microsoft.com/pricing/details/logic-apps) se aplica a contas de [integração](logic-apps-enterprise-integration-create-integration-account.md) onde você pode explorar, desenvolver e testar os recursos de processamento [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) em Aplicativos de Lógica Do Azure sem custo adicional. Cada assinatura do Azure pode ter até um [limite específico de contas de integração.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Cada conta de integração pode armazenar até o limite específico de artefatos, que incluem parceiros comerciais, [acordos,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)mapas, esquemas, montagens, certificados, configurações em lote, e assim por diante.

O Azure Logic Apps oferece contas de integração gratuitas, básicas e padrão. Os níveis Básico e Padrão são suportados pelo SLA (Logic Apps service-level agreement, contrato de nível de serviço da Logic Apps), enquanto o nível Free não é suportado por um SLA e tem limites de throughput e uso. Com exceção das contas de integração de nível gratuito, você pode ter mais de uma conta de integração em cada região do Azure. Para obter preços, consulte [preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Se você tiver um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), [premium ou desenvolvedor,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)seu ISE pode ter 5 contas de integração total. Para saber como funciona o modelo de preços fixos para um ISE, consulte a seção anterior [do modelo de preços fixos](#fixed-pricing) neste tópico. Para obter preços, consulte [preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Para escolher entre uma conta de integração Gratuita, Básica ou Padrão, revise essas descrições de casos de uso:

* **Grátis**: Para quando você quiser experimentar cenários exploratórios, não cenários de produção

* **Básico**: Para quando você quer apenas o tratamento de mensagens ou para agir como um pequeno parceiro de negócios que tem um relacionamento de parceiro comercial com uma entidade empresarial maior

* **Padrão:** Para quando você tem relações B2B mais complexas e um número maior de entidades que você deve gerenciar

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Com exceção dos aplicativos lógicos que são executados em um ambiente de serviço de integração (ISE), todas as entradas e saídas armazenadas no histórico de execução do seu aplicativo lógico são cobradas com base no período de [retenção de execução](logic-apps-limits-and-config.md#run-duration-retention-limits)de um aplicativo lógico. Os aplicativos lógicos que são executados em um ISE não incorrem em custos de retenção de dados. Para obter preços, consulte [preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Para ajudá-lo a monitorar o consumo de armazenamento do seu aplicativo lógico, você pode:

* Veja o número de unidades de armazenamento em GB que seu aplicativo lógico usa mensalmente.
* Veja os tamanhos das entradas e saídas de uma ação específica no histórico de execução do aplicativo lógico.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Ver o consumo de armazenamento de aplicativos lógico

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, em **Monitoramento,** selecione **Métricas**.

1. No painel à direita, em **Chart Title**, da lista **Métrica,** selecione **Uso de Faturamento para Execuções de Consumo de Armazenamento**.

   Esta métrica fornece o número de unidades de consumo de armazenamento em GB por mês que estão sendo cobradas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Exibir tamanhos de entrada e saída de ação

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. No painel direito, em **Executa histórico,** selecione a execução que tem as entradas e saídas que você deseja verificar.

1. Em **Logic app run,** escolha **Run Details**.

1. No **painel de detalhes do aplicativo Logic,** na tabela ações, que lista o status e a duração de cada ação, selecione a ação que deseja exibir.

1. No painel de ação do **aplicativo Logic,** encontre os tamanhos para as entradas e saídas dessa ação aparecerem respectivamente no **link Entradas** e **no link Saídas**.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os aplicativos azure logic](logic-apps-overview.md)
* [Crie seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md)
