---
title: Lidar com problemas de estrangulamento, ou '429 - Muitos erros de solicitações
description: Como contornar problemas de estrangulamento ou erros 'HTTP 429 Muitas solicitações', em Aplicativos de Lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272671"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Lidar com problemas de estrangulamento (429 - Erros de "muitas solicitações") no Azure Logic Apps

No [Azure Logic Apps](../logic-apps/logic-apps-overview.md), seu aplicativo lógico retorna um erro ["HTTP 429 Muitas solicitações" ao](https://developer.mozilla.org/docs/Web/HTTP/Status/429) experimentar o estrangulamento, que acontece quando o número de solicitações excede a taxa em que o destino pode lidar com um período específico de tempo. O estrangulamento pode criar problemas como processamento de dados atrasado, velocidade de desempenho reduzida e erros, como exceder a política de repetição especificada.

![Estrangulamento no conector SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Aqui estão alguns tipos comuns de estrangulamento que seu aplicativo lógico pode experimentar:

* [Aplicativo lógico](#logic-app-throttling)
* [Conector](#connector-throttling)
* [Serviço ou sistema de destino](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Estrangulamento de aplicativos lógicos

O serviço Azure Logic Apps tem seus próprios [limites de throughput](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Então, se o seu aplicativo lógico exceder esses limites, seu recurso de aplicativo lógico será estrangulado, não apenas uma instância específica ou executado.

Para encontrar eventos de estrangulamento neste nível, verifique o painel **Métricas** do seu aplicativo lógico no portal Azure.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **Monitoramento,** selecione **Métricas**.

1. Em **Chart Title,** **selecione Adicionar métrica** para que você adicione outra métrica à existente.

1. Na primeira barra métrica, da lista **MÉTRICA,** selecione **Action Throttled Events**. Na segunda barra métrica, da lista **MÉTRICA,** selecione **Trigger Throttled Events**.

Para lidar com o estrangulamento neste nível, você tem essas opções:

* Limite o número de instâncias lógicas do aplicativo que podem ser executadas ao mesmo tempo.

  Por padrão, se a condição de gatilho do seu aplicativo lógico for atendida mais de uma vez ao mesmo tempo, várias instâncias de gatilho para o seu aplicativo lógico são executadas simultaneamente ou *em paralelo*. Esse comportamento significa que cada instância de gatilho é acionada antes que a instância anterior do fluxo de trabalho termine de ser em execução.

  Embora o número padrão de instâncias de gatilho que podem ser executadas simultaneamente seja [ilimitado,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)você pode limitar esse número [ativando a configuração de simultâneo do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)e, se necessário, selecionar um limite diferente do valor padrão.

* Habilite o modo de alta throughput.

  Um aplicativo lógico tem um [limite padrão para o número de ações que podem ser executadas em um intervalo de rolamento de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar esse limite ao número máximo de ações, ative o [modo de throughput alto](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) no seu aplicativo lógico.

* Desativar o comportamento de desbatching da matriz ("dividir") nos gatilhos.

  Se um gatilho retornar uma matriz para as ações restantes do fluxo de trabalho para processar, a [configuração **Split On** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do gatilho dividirá os itens da matriz e iniciará uma instância de fluxo de trabalho para cada item de array, acionando efetivamente várias executações simultâneas até o limite Split [ **On** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Para controlar o estrangulamento, desligue o comportamento **Split On** e faça com que o aplicativo lógico processe toda a matriz com uma única chamada, em vez de lidar com um único item por chamada.

* Refatorar ações em aplicativos de lógica menor.

  Como mencionado anteriormente, um aplicativo lógico está limitado a um [número padrão de ações que podem ser executadas por um período de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Embora você possa aumentar esse limite habilitando [o modo de throughput alto,](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)você também pode considerar se deseja dividir as ações do seu aplicativo lógico em aplicativos de lógica menores para que o número de ações executadas em cada aplicativo lógico permaneça abaixo do limite. Dessa forma, você reduz a carga sobre um único recurso de aplicativo lógico e distribui a carga em vários aplicativos lógicos. Essa solução funciona melhor para ações que lidam com grandes conjuntos de dados ou giram tantas ações simultâneas executando ações, iterações de loop ou ações dentro de cada iteração de loop que excedem o limite de execução de ação.

  Por exemplo, este aplicativo lógico faz todo o trabalho para obter tabelas de um banco de dados do SQL Server e recebe as linhas de cada tabela. O **Para cada** loop itera simultaneamente através de cada tabela para que a ação Obter **linhas** retorne as linhas para cada tabela. Com base nas quantidades de dados nessas tabelas, essas ações podem exceder o limite de execuções de ação.

  ![Aplicativo lógico "antes" refatoração](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Depois de refatorar, o aplicativo lógico agora é um aplicativo de lógica de pais e filhos. O pai recebe as tabelas do SQL Server e, em seguida, chama um aplicativo de lógica filho para cada tabela para obter as linhas:

  ![Crie um aplicativo lógico para uma ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Aqui está o aplicativo de lógica infantil que é chamado pelo aplicativo de lógica pai para obter as linhas para cada tabela:

  ![Crie outro aplicativo lógico para uma segunda ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Estrangulamento do conector

Cada conector tem seus próprios limites de estrangulamento, que você pode encontrar na página de referência técnica do conector. Por exemplo, o [conector Azure Service Bus](https://docs.microsoft.com/connectors/servicebus/) tem um limite de estrangulamento que permite até 6.000 chamadas por minuto, enquanto o conector SQL Server tem [limites de estrangulamento que variam de acordo com o tipo de operação](https://docs.microsoft.com/connectors/sql/).

Alguns gatilhos e ações, como o HTTP, têm uma ["política de repetição"](../logic-apps/logic-apps-exception-handling.md#retry-policies) que você pode personalizar com base nos limites da [política de nova tentativa](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) para implementar o tratamento de exceções. Esta política especifica se e com que frequência um gatilho ou ação tenta uma solicitação quando a solicitação original falha ou se adia e resulta em uma resposta de 408, 429 ou 5xx. Assim, quando o estrangulamento inicia e retorna um erro de 429, o Logic Apps segue a política de repetição onde é suportado.

Para saber se um gatilho ou ação suporta políticas de repetição, verifique as configurações do gatilho ou da ação. Para exibir as tentativas de repetição de um gatilho ou de ação, acesse o histórico de execução do aplicativo lógico, selecione a execução que deseja analisar e expanda esse gatilho ou ação para exibir detalhes sobre entradas, saídas e quaisquer repetições, por exemplo:

![Exibir o histórico de execução da ação, repetições, entradas e saídas](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Embora o histórico de repetição forneça informações de erro, você pode ter problemas para diferenciar entre o estrangulamento do conector e [o estrangulamento do destino](#destination-throttling). Neste caso, você pode ter que rever os detalhes da resposta ou realizar alguns cálculos de intervalo de estrangulamento para identificar a fonte.

Para aplicativos lógicos no serviço global de aplicativos azure logic de vários locatários, o estrangulamento acontece no nível de *conexão.* Assim, por exemplo, para aplicativos lógicos que são executados em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)o estrangulamento ainda acontece para conexões não-ISE porque eles são executados no serviço global de aplicativos lógicos multilocatários. No entanto, as conexões ISE, que são criadas por conectores ISE, não são estranguladas porque são executadas em seu ISE.

Para lidar com o estrangulamento neste nível, você tem essas opções:

* Configure várias conexões para uma única ação para que o aplicativo lógico particione os dados para processamento.

  Para essa opção, considere se você pode distribuir a carga de trabalho dividindo as solicitações de uma ação em várias conexões para o mesmo destino usando as mesmas credenciais.

  Por exemplo, suponha que seu aplicativo lógico obtenha tabelas de um banco de dados do SQL Server e, em seguida, obtenha as linhas de cada tabela. Com base no número de linhas que você tem que processar, você pode usar várias conexões e vários **para cada** loop para dividir o número total de linhas em conjuntos menores para processamento. Este cenário usa dois **para cada** loop saem para dividir o número total de linhas pela metade. O primeiro **Para cada** loop usa uma expressão que fica com a primeira metade. O outro **Para cada** loop usa uma expressão diferente que fica com a segunda metade, por exemplo:<p>

    * Expressão 1: `take()` A função fica na parte frontal de uma coleção. Para obter mais [ **`take()`** ](workflow-definition-language-functions-reference.md#take)informações, consulte a função .

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressão 2: `skip()` A função remove a parte frontal de uma coleção e devolve todos os outros itens. Para obter mais [ **`skip()`** ](workflow-definition-language-functions-reference.md#skip)informações, consulte a função .

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Aqui está um exemplo visual que mostra como você pode usar essas expressões:

    ![Crie e use várias conexões para uma única ação](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Configure uma conexão diferente para cada ação.

  Para essa opção, considere se você pode distribuir a carga de trabalho espalhando as solicitações de cada ação sobre sua própria conexão, mesmo quando as ações se conectam ao mesmo serviço ou sistema e usam as mesmas credenciais.

  Por exemplo, suponha que seu aplicativo lógico obtenha as tabelas de um banco de dados do SQL Server e obtenha cada linha em cada tabela. Você pode usar conexões separadas para que as tabelas usem uma conexão, enquanto a obtenção de cada linha usa outra conexão.

  ![Crie e use conexões diferentes para cada ação](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Altere a simultudia ou o paralelismo em um [loop "Para cada".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

  Por padrão, as iterações de loop "Para cada" são executadas ao mesmo tempo até o [limite de simultu).](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Se você tem um conector que está sendo estrangulado dentro de um loop "Para cada", você pode reduzir o número de iterações de loop que são executadas em paralelo. Para saber mais, consulte esses tópicos:
  
  * [Loops "Para cada" - alterar a concorrência ou executar sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Esquema de linguagem de definição de fluxo de trabalho - Para cada loops](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Esquema de linguagem de definição de fluxo de trabalho - Alterar a concorrência do loop "Para cada"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Esquema de linguagem de definição de fluxo de trabalho - Executar loops "Para cada" seqüencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Serviço de destino ou estrangulamento do sistema

Embora um conector tenha seus próprios limites de estrangulamento, o serviço de destino ou sistema chamado pelo conector também pode ter limites de estrangulamento. Por exemplo, algumas APIs no Microsoft Exchange Server têm limites de estrangulamento mais rigorosos do que o conector do Office 365 Outlook.

Por padrão, as instâncias de um aplicativo lógico e quaisquer loops ou ramos dentro dessas instâncias, executados *em paralelo*. Esse comportamento significa que várias instâncias podem chamar o mesmo ponto final ao mesmo tempo. Cada instância não sabe sobre a existência do outro, então as tentativas de repetir as ações fracassadas podem criar [condições de corrida](https://en.wikipedia.org/wiki/Race_condition) onde várias chamadas tentam ser executadas ao mesmo tempo, mas para ter sucesso, essas chamadas devem chegar ao serviço de destino ou sistema antes que o estrangulamento comece a acontecer.

Por exemplo, suponha que você tenha uma matriz que tenha 100 itens. Você usa um loop "para cada" para iterar através da matriz e ativar o controle de simultâneo do loop para que você possa restringir o número de iterações paralelas a 20 ou o [limite padrão atual](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Dentro desse loop, uma ação insere um item da matriz em um banco de dados do SQL Server, que permite apenas 15 chamadas por segundo. Esse cenário resulta em um problema de estrangulamento porque um backlog de tentativas se acumula e nunca consegue ser executado.

Esta tabela descreve a linha do tempo para o que acontece no loop quando o intervalo de repetição da ação é de 1 segundo:

| Point-in-time | Número de ações que são executadas | Número de ações que falham | Número de repetições esperando |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 segundos | 20 inserções | 5 falha, devido ao limite de SQL | 5 repetições |
| T + 0,5 segundos | 15 inserções, devido às 5 tentativas anteriores de espera | Todos os 15 falham, devido ao limite anterior de SQL ainda em vigor por mais 0,5 segundos | 20 tentativas <br>(5 anteriores + 15 novos) |
| T + 1 segundo | 20 inserções | 5 falha mais 20 tentativas anteriores, devido ao limite de SQL | 25 repetições (20 anteriores + 5 novas)
|||||

Para lidar com o estrangulamento neste nível, você tem essas opções:

* Crie aplicativos lógicos para que cada um manuseie uma única operação.

  * Continuando com o exemplo do cenário do SQL Server nesta seção, você pode criar um aplicativo lógico que coloca itens de array em uma fila, como uma [fila de ônibus de serviço do Azure.](../connectors/connectors-create-api-servicebus.md) Em seguida, você cria outro aplicativo lógico que executa apenas a operação de inserção para cada item nessa fila. Dessa forma, apenas uma instância lógica do aplicativo é executada a qualquer momento específico, que ou completa a operação de inserção e passa para o próximo item na fila, ou a instância recebe 429 erros, mas não tenta tentativas improdutivas.

  * Crie um aplicativo de lógica pai que chame um filho ou um aplicativo lógico aninhado para cada ação. Se o pai precisar chamar diferentes aplicativos de filho com base no resultado dos pais, você pode usar uma ação de condição ou mudar de ação que determina qual aplicativo filho chamar. Esse padrão pode ajudá-lo a reduzir o número de chamadas ou operações.

    Por exemplo, suponha que você tenha dois aplicativos lógicos, cada um com um gatilho de votação que verifica sua conta de e-mail a cada minuto para assuntos específicos, como "Sucesso" ou "Falha". Esta configuração resulta em 120 chamadas por hora. Em vez disso, se você criar um aplicativo de lógica pai único que pesquisa a cada minuto, mas chama um aplicativo de lógica infantil que é executado com base se o assunto é "Sucesso" ou "Falha", você reduz o número de verificações de votação para metade, ou 60 neste caso.

* Configurar o processamento em lote.

  Se o serviço de destino suportar operações em lote, você pode lidar com o estrangulamento processando itens em grupos ou lotes, em vez de individualmente. Para implementar a solução de processamento em lote, você cria um aplicativo lógico "receptor de lote" e um aplicativo lógico "remetente em lote". O remetente de lote coleta mensagens ou itens até que seus critérios especificados sejam atendidos e, em seguida, envia essas mensagens ou itens em um único grupo. O receptor de lote aceita esse grupo e processa essas mensagens ou itens. Para obter mais informações, consulte [Enviar mensagens de processo em grupos](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Use as versões do webhook para gatilhos e ações, em vez das versões de votação.

  Por quê? Um gatilho de votação continua a verificar o serviço de destino ou o sistema em intervalos específicos. Um intervalo muito freqüente, como cada segundo, pode criar problemas de estrangulamento. No entanto, um webhook trigger ou ação, como [http webhook](../connectors/connectors-native-webhook.md), cria apenas uma única chamada para o serviço ou sistema de destino, que acontece na hora da assinatura e solicita que o destino notifique o gatilho ou a ação somente quando um evento acontece. Dessa forma, o gatilho ou ação não precisa verificar continuamente o destino.
  
  Então, se o serviço de destino ou sistema suporta webhooks ou fornece um conector que tenha uma versão webhook, essa opção é melhor do que usar a versão de votação. Para identificar gatilhos e ações do webhook, confirme se eles têm o `ApiConnectionWebhook` tipo ou que eles não exigem que você especifique uma recorrência. Para obter mais informações, consulte [o gatilho APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e [a ação APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [os limites e configuração dos Logic Apps](../logic-apps/logic-apps-limits-and-config.md)
