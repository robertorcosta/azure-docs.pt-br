---
title: Escala e hospedagem no Azure Functions
description: Saiba como escolher entre Azure Functions plano de consumo e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5dd703851054b058d96440a3a994b9d10eecfa3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372656"
---
# <a name="azure-functions-scale-and-hosting"></a>Escala e hospedagem no Azure Functions

Ao criar um aplicativo de funções no Azure, você deve escolher um plano de hospedagem para seu aplicativo. Há três planos de hospedagem básicos disponíveis para Azure Functions: [plano de consumo](#consumption-plan), [plano Premium](#premium-plan)e [plano dedicado (serviço de aplicativo)](#app-service-plan). Todos os planos de hospedagem estão geralmente disponíveis (GA) em máquinas virtuais Linux e Windows.

O plano de hospedagem que você escolher ditará os seguintes comportamentos:

* Como seu aplicativo de funções é dimensionado.
* Os recursos disponíveis para cada instância do aplicativo de funções.
* Suporte para recursos avançados, como a conectividade de rede virtual do Azure.

Os planos de consumo e Premium adicionam capacidade de computação automaticamente quando seu código está em execução. Seu aplicativo é escalado horizontalmente quando necessário para lidar com a carga e dimensionado quando o código para de ser executado. Para o plano de consumo, você também não precisa pagar por VMs ociosas ou reservar a capacidade antecipadamente.  

O plano Premium fornece recursos adicionais, como instâncias de computação Premium, a capacidade de manter instâncias quentes indefinidamente e conectividade VNet.

O plano do serviço de aplicativo permite que você aproveite a infraestrutura dedicada, que você gerencia. Seu aplicativo de funções não é dimensionado com base em eventos, o que significa que nunca é dimensionado para zero. (Requer que o [Always on](#always-on) esteja habilitado.)

Para obter uma comparação detalhada entre os vários planos de hospedagem (incluindo a hospedagem baseada em kubernetes), consulte a [seção comparação de planos de hospedagem](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Plano de consumo

Quando você estiver usando o plano de consumo, as instâncias do host Azure Functions serão adicionadas e removidas dinamicamente com base no número de eventos de entrada. Esse plano sem servidor é escalado automaticamente e você é cobrado pelos recursos de computação apenas durante a execução das funções. Em um plano de consumo, a execução de uma função expire após um período configurável.

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. O uso é agregado em todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando suas funções forem executadas
* Escale horizontalmente de forma automática, mesmo durante períodos de carga alta

Os aplicativos de funções na mesma região podem ser atribuídos ao mesmo plano de consumo. Não há nenhuma desvantagem ou impacto para ter vários aplicativos em execução no mesmo plano de consumo. A atribuição de vários aplicativos ao mesmo plano de consumo não afeta a resiliência, a escalabilidade ou a confiabilidade de cada aplicativo.

Para saber mais sobre como estimar os custos durante a execução em um plano de consumo, consulte [noções básicas sobre custos do plano de consumo](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plano Premium

Quando você estiver usando o plano Premium, as instâncias do host Azure Functions serão adicionadas e removidas com base no número de eventos de entrada, assim como o plano de consumo.  O plano Premium dá suporte aos seguintes recursos:

* Instâncias passivas perpétuas para evitar qualquer início frio
* Conectividade de VNet
* Duração de execução ilimitada (60 minutos garantidos)
* Tamanhos de instância Premium (um núcleo, duas principais e quatro instâncias de núcleo)
* Preços mais previsíveis
* Alocação de aplicativo de alta densidade para planos com vários aplicativos de funções

Para saber como você pode criar um aplicativo de funções em um plano Premium, consulte [Azure Functions plano Premium](functions-premium-plan.md).

Em vez de cobrança por execução e memória consumida, a cobrança para o plano Premium é baseada no número de segundos de núcleo e na memória alocada entre instâncias.  Não há nenhum encargo de execução com o plano Premium. Pelo menos uma instância deve ser alocada em todos os momentos por plano. Isso resulta em um custo mensal mínimo por plano ativo, independentemente se a função estiver ativa ou ociosa. Tenha em mente que todos os aplicativos de funções em um plano Premium compartilham instâncias alocadas.

Considere o plano Azure Functions Premium nas seguintes situações:

* Os aplicativos de funções executam continuamente ou quase continuamente.
* Você tem um número alto de execuções pequenas e tem uma cobrança de alta execução, mas a cobrança de baixo GB por segundo no plano de consumo.
* Você precisa de mais opções de CPU ou memória do que o fornecido pelo plano de consumo.
* Seu código precisa ser executado por mais tempo do que o [máximo permitido](#timeout) no plano de consumo.
* Você precisa de recursos que estão disponíveis apenas em um plano Premium, como a conectividade de rede virtual. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plano dedicado (serviço de aplicativo)

Seus aplicativos de funções também podem ser executados nas mesmas VMs dedicadas que outros aplicativos do serviço de aplicativo (SKUs Basic, Standard, Premium e Isolated).

Considere um plano do serviço de aplicativo nas seguintes situações:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* Você deseja fornecer uma imagem personalizada na qual executar suas funções.

Você paga o mesmo para aplicativos de funções em um plano do serviço de aplicativo como faria para outros recursos do serviço de aplicativo, como aplicativos Web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Usando um plano do serviço de aplicativo, você pode escalar horizontalmente manualmente Adicionando mais instâncias de VM. Você também pode habilitar o dimensionamento automático, embora o dimensionamento automático seja mais lento do que a escala elástica do plano Premium. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/manage-scale-up.md). 

Ao executar funções JavaScript em um plano do Serviço de Aplicativo, você deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [escolher planos de serviço de aplicativo de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

A execução em um [ambiente do serviço de aplicativo](../app-service/environment/intro.md) (ase) permite isolar totalmente suas funções e aproveitar o maior número de instâncias do que um plano do serviço de aplicativo.

### <a name="always-on"></a><a name="always-on"></a> Always On

Se você executar em um plano do serviço de aplicativo, habilite a configuração **Always on** para que seu aplicativo de funções seja executado corretamente. Em um plano do Serviço de Aplicativo, o runtime das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. Always On está disponível apenas em um plano de Serviço de Aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinar o plano de Hospedagem de um aplicativo existente

Para determinar o plano de hospedagem usado pelo seu aplicativo de funções, consulte **plano do serviço de aplicativo** na guia **visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para ver o tipo de preço, selecione o nome do **plano do serviço de aplicativo**e, em seguida, selecione **Propriedades** no painel esquerdo.

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também é possível usar a CLI do Azure para determinar o plano, da seguinte maneira:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída desse comando for `dynamic`, o aplicativo de funções estará no plano de Consumo. Quando a saída desse comando for `ElasticPremium` , seu aplicativo de funções estará no plano Premium. Todos os outros valores indicam diferentes camadas de um plano do serviço de aplicativo.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, um aplicativo de funções requer uma conta de armazenamento do Azure geral, que dá suporte ao blob do Azure, à fila, aos arquivos e ao armazenamento de tabelas. Isso ocorre porque Azure Functions se baseia no armazenamento do Azure para operações como gerenciar gatilhos e registrar execuções de função, mas algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas, que incluem contas de armazenamento somente blob (incluindo armazenamento Premium) e contas de armazenamento para uso geral com replicação de armazenamento com redundância de zona, são filtradas das seleções da **Conta de Armazenamento** existente quando você cria um aplicativo de funções.

A mesma conta de armazenamento usada por seu aplicativo de funções também pode ser usada por seus gatilhos e associações para armazenar os dados do aplicativo. No entanto, para operações com uso intensivo de armazenamento, você deve usar uma conta de armazenamento separada.  

É possível que vários aplicativos de funções compartilhem a mesma conta de armazenamento sem nenhum problema. (Um bom exemplo disso é quando você desenvolve vários aplicativos em seu ambiente local usando o emulador de armazenamento do Azure, que atua como uma conta de armazenamento.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte [apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#core-storage-services).

### <a name="in-region-data-residency"></a>Residência de dados na região

Quando necessário para que todos os dados do cliente permaneçam dentro de uma única região, a conta de armazenamento associada ao aplicativo de funções deve ser uma com [redundância de região](../storage/common/storage-redundancy.md).  Uma conta de armazenamento redundante na região também precisaria ser usada com o [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) para Durable functions.

Outros dados do cliente gerenciados pela plataforma serão armazenados apenas dentro da região ao hospedar em um Load Balancer interno Ambiente do Serviço de Aplicativo (ou ASE ILB).  Os detalhes podem ser encontrados na [redundância de zona do ase](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam os planos de consumo e premium

Nos planos de consumo e Premium, a infraestrutura de Azure Functions escala recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos em que suas funções são disparadas. Cada instância do host do Functions no plano de consumo é limitada a 1,5 GB de memória e uma CPU.  Uma instância do host é o aplicativo de funções inteiro, o que significa que todas as funções em um aplicativo de funções compartilham recursos dentro de uma instância e são dimensionadas ao mesmo tempo. Aplicativos de função que compartilham o mesmo plano de consumo são dimensionados de forma independente.  No plano Premium, o tamanho do plano determinará a memória disponível e a CPU para todos os aplicativos nesse plano nessa instância.  

Os arquivos de código de função são armazenados em compartilhamentos de arquivos do Azure na conta de armazenamento principal da função. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

### <a name="runtime-scaling"></a>Escalonamento de runtime

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

A unidade de escala para Azure Functions é o aplicativo de funções. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente *reduzido* para zero quando não há funções em execução em um aplicativo de funções.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Inicialização a frio

Depois que seu aplicativo de funções ficar ocioso por alguns minutos, a plataforma poderá dimensionar o número de instâncias nas quais seu aplicativo é executado até zero. A próxima solicitação tem a latência adicional de dimensionamento de zero para um. Essa latência é conhecida como uma _inicialização a frio_. O número de dependências que devem ser carregadas pelo seu aplicativo de funções pode afetar a hora de início frio. A inicialização a frio é mais um problema para operações síncronas, como gatilhos HTTP que devem retornar uma resposta. Se a frio for iniciada, afetará suas funções, considere a execução em um plano Premium ou em um plano dedicado com o Always on habilitado.   

### <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. Há algumas complexidades de comportamentos de dimensionamento a serem considerados:

* Um único aplicativo de funções só pode ser dimensionado para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.  Você pode [especificar um máximo inferior](#limit-scale-out) para limitar a escala, conforme necessário.
* Para gatilhos HTTP, novas instâncias são alocadas, no máximo, uma vez por segundo.
* Para gatilhos não HTTP, novas instâncias são alocadas, no máximo, uma vez a cada 30 segundos. O dimensionamento é mais rápido quando executado em um [plano Premium](#premium-plan).
* Para gatilhos do barramento de serviço, use _gerenciar_ direitos em recursos para o dimensionamento mais eficiente. Com os direitos de _escuta_ , o dimensionamento não é tão preciso porque o comprimento da fila não pode ser usado para informar decisões de dimensionamento. Para saber mais sobre como definir direitos nas políticas de acesso do barramento de serviço, consulte [política de autorização de acesso compartilhado](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Para gatilhos do hub de eventos, consulte as [diretrizes de dimensionamento](functions-bindings-event-hubs-trigger.md#scaling) no artigo de referência. 

### <a name="limit-scale-out"></a>Limitar escala horizontal

Talvez você queira restringir o número de instâncias para as quais um aplicativo é dimensionado.  Isso é mais comum para casos em que um componente downstream como um banco de dados tem taxa de transferência limitada.  Por padrão, as funções de plano de consumo serão escaladas horizontalmente para até 200 instâncias, e as funções de plano Premium serão expandidas para tantas de 100 instâncias.  Você pode especificar um máximo inferior para um aplicativo específico modificando o `functionAppScaleLimit` valor.  O `functionAppScaleLimit` pode ser definido como 0 ou nulo para irrestrito ou um valor válido entre 1 e o máximo do aplicativo.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que afetarão a qualidade da escala, incluindo a configuração do host, o espaço de runtime e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

Para obter mais informações sobre o dimensionamento em Python e Node.js, consulte [Azure Functions guia do desenvolvedor do Python – dimensionamento e simultaneidade](functions-reference-python.md#scaling-and-concurrency) e [Azure Functions Node.js guia do desenvolvedor – dimensionamento e simultaneidade](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Modelo de cobrança

A cobrança pelos diferentes planos é descrita em detalhes na [página de preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)**. Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

Consultas e informações úteis sobre como entender sua fatura de consumo podem ser encontradas [nas perguntas frequentes sobre cobrança](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Comparação dos planos de hospedagem

A tabela de comparação a seguir mostra todos os aspectos importantes para ajudar na decisão de Azure Functions opção de plano de Hospedagem de aplicativo:

### <a name="plan-summary"></a>Resumo do plano
| | |
| --- | --- |  
|**[Plano de consumo](#consumption-plan)**| Dimensione automaticamente e pague apenas pelos recursos de computação quando suas funções estiverem em execução. No plano de consumo, as instâncias do host do Functions são adicionadas e removidas dinamicamente com base no número de eventos de entrada.<br/> ✔ Plano de hospedagem padrão.<br/>✔ Pague somente quando suas funções estiverem em execução.<br/>✔ escalar horizontalmente automaticamente, mesmo durante períodos de alta carga.|  
|**[Plano Premium](#premium-plan)**|Embora o dimensionamento automático seja baseado sob demanda, use trabalhos pré-configurados para executar aplicativos sem atraso depois de ficar ocioso, executar em instâncias mais poderosas e conectar-se ao VNETs. Considere o plano Azure Functions Premium nas seguintes situações, além de todos os recursos do plano do serviço de aplicativo: <br/>✔ Seus aplicativos de funções são executados continuamente ou quase continuamente.<br/>✔ Você tem um número alto de execuções pequenas e tem uma cobrança de alta execução, mas a cobrança de baixo GB por segundo no plano de consumo.<br/>✔ Você precisa de mais opções de CPU ou memória do que o fornecido pelo plano de consumo.<br/>✔ Seu código precisa ser executado por mais tempo do que o máximo permitido no plano de consumo.<br/>✔ Você precisa de recursos que estão disponíveis apenas em um plano Premium, como a conectividade de rede virtual.|  
|**[Plano dedicado](#app-service-plan)**<sup>1</sup>|Execute suas funções em um plano do serviço de aplicativo em taxas regulares do plano do serviço de aplicativo. Boa opção para operações de longa execução, bem como quando mais custos e dimensionamento de previsão são necessários. Considere um plano do serviço de aplicativo nas seguintes situações:<br/>✔ Você tem VMs subutilizadas existentes que já estão executando outras instâncias do serviço de aplicativo.<br/>✔ Você deseja fornecer uma imagem personalizada na qual executar suas funções.|  
|**[Ase](#app-service-plan)**<sup>1</sup>|O Ambiente do Serviço de Aplicativo (ASE) é um recurso do serviço de aplicativo que fornece um ambiente totalmente isolado e dedicado para executar com segurança aplicativos do serviço de aplicativo em alta escala. ASEs são apropriadas para cargas de trabalho de aplicativo que exigem: <br/>✔ Escala muito alta.<br/>✔ Isolamento de computação completo e acesso seguro à rede.<br/>✔ Utilização de memória alta.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | O kubernetes fornece um ambiente totalmente isolado e dedicado em execução no topo da plataforma kubernetes.  Kubernetes é apropriado para cargas de trabalho de aplicativo que exigem: <br/>✔ Os requisitos de hardware personalizados.<br/>✔ Isolamento e acesso seguro à rede.<br/>✔ A capacidade de executar em um ambiente híbrido ou de várias nuvens.<br/>✔ Executado junto com os aplicativos e serviços kubernetes existentes.|  

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="operating-systemruntime"></a>Sistema operacional/tempo de execução

| | Linux<sup>1</sup><br/>Somente código | Windows<sup>2</sup><br/>Somente código | Linux<sup>1, 3</sup><br/>Contêiner do Docker |
| --- | --- | --- | --- |
| **[Plano de consumo](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Sem suporte  |
| **[Plano Premium](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Plano dedicado](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[Ase](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/d | n/d |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> O Linux é o único sistema operacional com suporte para a pilha de tempo de execução do Python.  
<sup>2</sup> O Windows é o único sistema operacional com suporte para a pilha de tempo de execução do PowerShell.   
<sup>3</sup> O Linux é o único sistema operacional com suporte para contêineres do Docker.
<sup>4</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="scale"></a>Escala

| | Escalar horizontalmente | N º máximo de instâncias |
| --- | --- | --- |
| **[Plano de consumo](#consumption-plan)** | Controlado por evento. Escale horizontalmente de forma automática, mesmo durante períodos de carga alta. A infraestrutura de Azure Functions dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos em que suas funções são disparadas. | 200 |
| **[Plano Premium](#premium-plan)** | Controlado por evento. Escale horizontalmente de forma automática, mesmo durante períodos de carga alta. A infraestrutura de Azure Functions dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos em que suas funções são disparadas. |100|
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Manual/dimensionamento automático |10-20|
| **[Ase](#app-service-plan)**<sup>1</sup> | Manual/dimensionamento automático |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Dimensionamento automático controlado por evento para clusters kubernetes usando [Keda](https://keda.sh). | Varia &nbsp; de acordo com o &nbsp; cluster.&nbsp;&nbsp;|

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="cold-start-behavior"></a>Comportamento de início frio

|    |    | 
| -- | -- |
| **[Plano de consumo &nbsp;](#consumption-plan)** | Os aplicativos podem ser dimensionados para zero se estiverem ociosos por um período de tempo, o que significa que algumas solicitações podem ter latência adicional na inicialização.  O plano de consumo tem algumas otimizações para ajudar a diminuir a hora de início frio, incluindo a extração de funções de espaço reservado pré-configuradas que já têm o host de funções e os processos de linguagem em execução. |
| **[Plano Premium](#premium-plan)** | Instâncias passivas perpétuas para evitar qualquer inicialização a frio. |
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Ao executar em um plano dedicado, o host do Functions pode ser executado continuamente, o que significa que a inicialização a frio não é realmente um problema. |
| **[Ase](#app-service-plan)**<sup>1</sup> | Ao executar em um plano dedicado, o host do Functions pode ser executado continuamente, o que significa que a inicialização a frio não é realmente um problema. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Depende da configuração do KEDA. Os aplicativos podem ser configurados para serem executados sempre e nunca têm inicialização a frio, ou configurados para serem dimensionados para zero, o que resulta em uma inicialização a frio em novos eventos. 

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="service-limits"></a>Limites de serviço

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Recursos de rede

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Cobrança

| | | 
| --- | --- |
| **[Plano de consumo](#consumption-plan)** | Pague apenas pelo tempo em que suas funções são executadas. A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. |
| **[Plano Premium](#premium-plan)** | O plano Premium é baseado no número de segundos de núcleo e na memória usada nas instâncias necessárias e pré-configuradas. Pelo menos uma instância por plano deve ser mantida quase sempre. Este plano fornece preços mais previsíveis. |
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Você paga o mesmo para aplicativos de funções em um plano do serviço de aplicativo como faria para outros recursos do serviço de aplicativo, como aplicativos Web.|
| **[Ase](#app-service-plan)**<sup>1</sup> | Há uma taxa mensal simples para um ASE que paga pela infraestrutura e não muda com o tamanho do ASE. Além disso, há um custo por vCPU do plano do serviço de aplicativo. Todos os aplicativos hospedados no ASE estão em um SKU de preços Isolado. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Você paga apenas os custos de seu cluster kubernetes; nenhuma cobrança adicional para funções. Seu aplicativo de funções é executado como uma carga de trabalho de aplicativo sobre o cluster, assim como um aplicativo regular. |

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="next-steps"></a>Próximas etapas

+ [Início Rápido: Criar um projeto do Azure Functions usando o Visual Studio Code](functions-create-first-function-vs-code.md)
+ [Tecnologias de implantação no Azure Functions](functions-deployment-technologies.md) 
+ [Guia do desenvolvedor do Azure Functions](functions-reference.md)
