---
title: Escala e hospedagem no Azure Functions
description: Saiba como escolher entre o plano de consumo de funções do Azure e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b000776c04550e1deb883039d94deeb735061ce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985874"
---
# <a name="azure-functions-scale-and-hosting"></a>Escala e hospedagem no Azure Functions

Ao criar um aplicativo de função no Azure, você deve escolher um plano de hospedagem para o seu aplicativo. Existem três planos de hospedagem disponíveis para funções do Azure: [plano de consumo,](#consumption-plan) [plano Premium](#premium-plan)e plano [Dedicado (App Service).](#app-service-plan)

O plano de hospedagem escolhido dita os seguintes comportamentos:

* Como seu aplicativo de função é dimensionado.
* Os recursos disponíveis para cada instância do aplicativo de função.
* Suporte para recursos avançados, como a conectividade Azure Virtual Network.

Os planos Consumption e Premium adicionam automaticamente poder de computação quando seu código está em execução. Seu aplicativo é dimensionado quando necessário para lidar com a carga e dimensionado quando o código pára de ser executado. Para o plano de Consumo, você também não precisa pagar por VMs ociosas ou capacidade de reserva antecipadamente.  

O plano premium fornece recursos adicionais, como instâncias de computação premium, a capacidade de manter as instâncias aquecidas indefinidamente e a conectividade VNet.

O plano app service permite que você aproveite a infra-estrutura dedicada, que você gerencia. Seu aplicativo de função não é dimensionado com base em eventos, o que significa que nunca é dimensionado em zero. (Requer que [always on](#always-on) está ativado.)

## <a name="hosting-plan-support"></a>Suporte a plano de hospedagem

O suporte a recursos se enquadra nas duas categorias a seguir:

* _Disponível geralmente (GA):_ totalmente apoiado e aprovado para uso de produção.
* _Visualização_: ainda não totalmente suportado nem aprovado para uso de produção.

A tabela a seguir indica o nível atual de suporte para os três planos de hospedagem, ao ser executado no Windows ou Linux:

| | Plano de consumo | Plano Premium | Plano dedicado |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plano de consumo

Quando você está usando o plano Consumo, as instâncias do host Funções do Azure são adicionadas e removidas dinamicamente com base no número de eventos recebidos. Esse plano sem servidor escala automaticamente, e você é cobrado pelos recursos de computação apenas durante a execução de suas funções. Em um plano de consumo, a execução de uma função expire após um período configurável.

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. A cobrança é agregada entre todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando suas funções forem executadas
* Escale horizontalmente de forma automática, mesmo durante períodos de carga alta

Os aplicativos de função na mesma região podem ser atribuídos ao mesmo plano de consumo. Não há desvantagem ou impacto em ter vários aplicativos rodando no mesmo plano de consumo. Atribuir vários aplicativos ao mesmo plano de consumo não tem impacto na resiliência, escalabilidade ou confiabilidade de cada aplicativo.

Para saber mais sobre como estimar custos ao executar um plano de consumo, consulte [Entendendo os custos do plano de consumo](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plano premium

Quando você está usando o plano Premium, as instâncias do host Funções do Azure são adicionadas e removidas com base no número de eventos recebidos, assim como o plano Consumo.  O plano premium suporta os seguintes recursos:

* Instâncias perpetuamente quentes para evitar qualquer partida fria
* Conectividade de VNet
* Duração de execução ilimitada (60 minutos garantidos)
* Tamanhos de instância premium (um núcleo, dois núcleos e quatro instâncias principais)
* Preços mais previsíveis
* Alocação de aplicativos de alta densidade para planos com aplicativos de múltiplas funções

Informações sobre como você pode configurar essas opções podem ser encontradas no documento do [plano Azure Functions Premium](functions-premium-plan.md).

Em vez de faturar por execução e memória consumida, o faturamento do plano Premium é baseado no número de segundos principais e na memória usada em instâncias necessárias e pré-aquecidas. Pelo menos uma instância deve estar quente o tempo todo por plano. Isso significa que há um custo mínimo mensal por plano ativo, independentemente do número de execuções. Tenha em mente que todos os aplicativos de função em um plano Premium compartilham instâncias pré-aquecidas e ativas.

Considere o plano Azure Functions Premium nas seguintes situações:

* Os aplicativos de funções executam continuamente ou quase continuamente.
* Você tem um alto número de pequenas execuções e tem uma conta de execução alta, mas baixa segunda conta GB no plano de consumo.
* Você precisa de mais opções de CPU ou memória do que o que é fornecido pelo plano Consumo.
* Seu código precisa ser executado por mais tempo do que o [tempo máximo de execução permitido](#timeout) no plano de consumo.
* Você precisa de recursos que só estão disponíveis em um plano Premium, como conectividade de rede virtual.

Ao executar funções JavaScript em um plano Premium, você deve escolher uma instância que tenha menos vCPUs. Para obter mais informações, consulte os [planos Premium single-core](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plano dedicado (App Service)

Seus aplicativos de função também podem ser executados nos mesmos VMs dedicados que outros aplicativos do App Service (SKUs básicos, padrão, premium e isolados).

Considere um plano de Serviço de Aplicativo nas seguintes situações:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* Você deseja fornecer uma imagem personalizada sobre a qual executar suas funções.

Você paga o mesmo por aplicativos de função em um Plano de Serviço de Aplicativo como faria para outros recursos do App Service, como aplicativos web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Com um plano de Serviço de Aplicativo, você pode dimensionar manualmente adicionando mais instâncias de VM. Você também pode habilitar a escala automática. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/manage-scale-up.md). 

Ao executar funções JavaScript em um plano do Serviço de Aplicativo, você deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [Escolha planos de serviço de aplicativo de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Sempre ligado

Se você executar em um plano de Serviço de Aplicativo, você deve ativar a configuração **Always on** para que seu aplicativo de função seja executado corretamente. Em um plano do Serviço de Aplicativo, o runtime das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. Always On está disponível apenas em um plano de Serviço de Aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determine o plano de hospedagem de um aplicativo existente

Para determinar o plano de hospedagem usado pelo aplicativo de funções, consulte **plano do Serviço de Aplicativo/tipo de preço** na guia **Visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para planos do Serviço de Aplicativo, o tipo de preço também é indicado.

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também é possível usar a CLI do Azure para determinar o plano, da seguinte maneira:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída desse comando for `dynamic`, o aplicativo de funções estará no plano de Consumo. Quando a saída deste `ElasticPremium`comando é, seu aplicativo de função está no plano Premium. Todos os outros valores indicam diferentes níveis de um plano de Serviço de Aplicativo.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, um aplicativo de função requer uma conta geral do Azure Storage, que suporta o armazenamento Azure Blob, Fila, Arquivos e Tabela. Isso ocorre porque o Functions depende do Armazenamento do Microsoft Azure para operações como o gerenciamento de gatilhos e execuções de funções de registro em log, mas algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas, que incluem contas de armazenamento somente blob (incluindo armazenamento Premium) e contas de armazenamento para uso geral com replicação de armazenamento com redundância de zona, são filtradas das seleções da **Conta de Armazenamento** existente quando você cria um aplicativo de funções.

A mesma conta de armazenamento usada pelo seu aplicativo de função também pode ser usada por seus gatilhos e vinculações para armazenar os dados do aplicativo. No entanto, para operações de uso intensivo de armazenamento, você deve usar uma conta de armazenamento separada.  

É certamente possível que vários aplicativos de função compartilhem a mesma conta de armazenamento sem problemas. (Um bom exemplo disso é quando você desenvolve vários aplicativos em seu ambiente local usando o Azure Storage Emulator, que age como uma conta de armazenamento.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte [Apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#core-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam o consumo e os planos premium

Nos planos Consumo e Premium, a infra-estrutura Funções do Azure dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host Functions, com base no número de eventos em que suas funções são acionadas. Cada instância do host Functions no plano Consumo é limitada a 1,5 GB de memória e uma CPU.  Uma instância do host é todo o aplicativo de função, o que significa que todas as funções dentro de um recurso de compartilhamento de aplicativo de função dentro de uma instância e escala ao mesmo tempo. Aplicativos de função que compartilham o mesmo plano de consumo são dimensionados de forma independente.  No plano Premium, o tamanho do seu plano determinará a memória e a CPU disponíveis para todos os aplicativos nesse plano nessa instância.  

Os arquivos de código de função são armazenados em compartilhamentos de arquivos Do Azure na conta de armazenamento principal da função. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

### <a name="runtime-scaling"></a>Escalonamento de runtime

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

A unidade de escala para funções do Azure é o aplicativo de função. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente *dimensionado para* zero quando nenhuma função está sendo executado dentro de um aplicativo de função.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. Existem alguns meandros de comportamentos de escala para estar ciente:

* Um único aplicativo de função só é dimensionado para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.
* Para gatilhos HTTP, novas instâncias são alocadas, no máximo, uma vez por segundo.
* Para gatilhos não-HTTP, novas instâncias são alocadas, no máximo, uma vez a cada 30 segundos. O dimensionamento é mais rápido ao executar um [plano Premium](#premium-plan).
* Para os gatilhos do Service Bus, use _Gerenciar_ os direitos sobre os recursos para obter o dimensionamento mais eficiente. Com os direitos _de ouvir,_ o dimensionamento não é tão preciso porque o comprimento da fila não pode ser usado para informar decisões de dimensionamento. Para saber mais sobre a definição de direitos nas políticas de acesso ao Service Bus, consulte [Política de autorização de acesso compartilhado](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Para os gatilhos do Event Hub, consulte a [orientação de dimensionamento](functions-bindings-event-hubs-trigger.md#scaling) no artigo de referência. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que afetarão a qualidade da escala, incluindo a configuração do host, o espaço de runtime e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

Para obter informações adicionais sobre o dimensionamento em Python e Node.js, consulte [o guia de desenvolvedores Python de funções do Azure - Escalae e conmoeda](functions-reference-python.md#scaling-and-concurrency) e [guia de desenvolvedor do Azure Functions Node.js - Dimensionamento e concorrência](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Modelo de cobrança

O faturamento dos diferentes planos é descrito em detalhes na página de preços funções do [Azure](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)**. Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

Consultas úteis e informações sobre como entender sua conta de consumo podem ser encontradas [no FAQ de faturamento](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites de serviço

A tabela a seguir indica os limites aplicáveis aos aplicativos de função ao serem executados nos vários planos de hospedagem:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
