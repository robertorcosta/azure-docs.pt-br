---
title: Escala e hospedagem no Azure Functions
description: Learn how to choose between Azure Functions Consumption plan and Premium plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9644e89591d7d8b7642b5f381434357191d1711
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226592"
---
# <a name="azure-functions-scale-and-hosting"></a>Escala e hospedagem no Azure Functions

When you create a function app in Azure, you must choose a hosting plan for your app. There are three hosting plans available for Azure Functions: [Consumption plan](#consumption-plan), [Premium plan](#premium-plan), and [App Service plan](#app-service-plan).

The hosting plan you choose dictates the following behaviors:

* How your function app is scaled.
* The resources available to each function app instance.
* Support for advanced features, such as VNET connectivity.

Both Consumption and Premium plans automatically add compute power when your code is running. Your app is scaled out when needed to handle load, and scaled down when code stops running. For the Consumption plan, you also don't have to pay for idle VMs or reserve capacity in advance.  

Premium plan provides additional features, such as premium compute instances, the ability to keep instances warm indefinitely, and VNet connectivity.

App Service plan allows you to take advantage of dedicated infrastructure, which you manage. Your function app doesn't scale based on events, which means is never scales down to zero. (Requires that [Always on](#always-on) is enabled.)

> [!NOTE]
> You can switch between Consumption and Premium plans by changing the plan property of the function app resource.

## <a name="hosting-plan-support"></a>Hosting plan support

Feature support falls into the following two categories:

* _Generally available (GA)_ : fully supported and approved for production use.
* _Preview_: not yet fully supported and approved for production use.

The following table indicates the current level of support for the three hosting plans, when running on either Windows or Linux:

| | Plano de consumo | Plano Premium | Dedicated plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plano de consumo

When you're using the Consumption plan, instances of the Azure Functions host are dynamically added and removed based on the number of incoming events. Esse plano sem servidor escala automaticamente, e você é cobrado pelos recursos de computação apenas durante a execução de suas funções. Em um plano de consumo, a execução de uma função expire após um período configurável.

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. A cobrança é agregada entre todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando suas funções forem executadas
* Escale horizontalmente de forma automática, mesmo durante períodos de carga alta

Function apps in the same region can be assigned to the same Consumption plan. There's no downside or impact to having multiple apps running in the same Consumption plan. Assigning multiple apps to the same consumption plan has no impact on resilience, scalability, or reliability of each app.

To learn more about how to estimate costs when running in a Consumption plan, see [Understanding Consumption plan costs](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium plan

When you're using the Premium plan, instances of the Azure Functions host are added and removed based on the number of incoming events just like the Consumption plan.  Premium plan supports the following features:

* Perpetually warm instances to avoid any cold start
* Conectividade de VNet
* Unlimited execution duration
* Premium instance sizes (one core, two core, and four core instances)
* More predictable pricing
* High-density app allocation for plans with multiple function apps

Information on how you can configure these options can be found in the [Azure Functions premium plan document](functions-premium-plan.md).

Instead of billing per execution and memory consumed, billing for the Premium plan is based on the number of core seconds and memory used across needed and pre-warmed instances. At least one instance must be warm at all times per plan. This means that there is a minimum monthly cost per active plan, regardless of the number of executions. Keep in mind that all function apps in a Premium plan share pre-warmed and active instances.

Consider the Azure Functions premium plan in the following situations:

* Os aplicativos de funções executam continuamente ou quase continuamente.
* You have a high number of small executions and have a high execution bill but low GB second bill in the consumption plan.
* You need more CPU or memory options than what is provided by the Consumption plan.
* Your code needs to run longer than the [maximum execution time allowed](#timeout) on the Consumption plan.
* You require features that are only available on a Premium plan, such as VNET/VPN connectivity.

When running JavaScript functions on a Premium plan, you should choose an instance that has fewer vCPUs. For more information, see the [Choose single-core Premium plans](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedicated (App Service) plan

Your function apps can also run on the same dedicated VMs as other App Service apps (Basic, Standard, Premium, and Isolated SKUs).

Consider an App Service plan in the following situations:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* You want to provide a custom image on which to run your functions.

You pay the same for function apps in an App Service Plan as you would for other App Service resources, like web apps. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

With an App Service plan, you can manually scale out by adding more VM instances. You can also enable autoscale. Para obter mais informações, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/manage-scale-up.md). 

Ao executar funções JavaScript em um plano do Serviço de Aplicativo, você deve escolher um plano que tenha menos vCPUs. For more information, see [Choose single-core App Service plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Se executar em um plano do Serviço de Aplicativo, você deverá habilitar a configuração **Always On** para que o aplicativo de funções execute corretamente. Em um plano do Serviço de Aplicativo, o runtime das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. Always On está disponível apenas em um plano de Serviço de Aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determine the hosting plan of an existing application

Para determinar o plano de hospedagem usado pelo aplicativo de funções, consulte **plano do Serviço de Aplicativo/tipo de preço** na guia **Visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para planos do Serviço de Aplicativo, o tipo de preço também é indicado.

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também é possível usar a CLI do Azure para determinar o plano, da seguinte maneira:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída desse comando for `dynamic`, o aplicativo de funções estará no plano de Consumo. When the output from this command is `ElasticPremium`, your function app is in the Premium plan. All other values indicate different tiers of an App Service plan.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage. Isso ocorre porque o Functions depende do Armazenamento do Microsoft Azure para operações como o gerenciamento de gatilhos e execuções de funções de registro em log, mas algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas, que incluem contas de armazenamento somente blob (incluindo armazenamento Premium) e contas de armazenamento para uso geral com replicação de armazenamento com redundância de zona, são filtradas das seleções da **Conta de Armazenamento** existente quando você cria um aplicativo de funções.

The same storage account used by your function app can also be used by your triggers and bindings to store your application data. However, for storage-intensive operations, you should use a separate storage account.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte [Apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>How the consumption and premium plans work

In the consumption and premium plans, the Azure Functions infrastructure scales CPU and memory resources by adding additional instances of the Functions host, based on the number of events that its functions are triggered on. Each instance of the Functions host in the consumption plan is limited to 1.5 GB of memory and one CPU.  An instance of the host is the entire function app, meaning all functions within a function app share resource within an instance and scale at the same time. Function apps that share the same consumption plan are scaled independently.  In the premium plan, your plan size will determine the available memory and CPU for all apps in that plan on that instance.  

Function code files are stored on Azure Files shares on the function's main storage account. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

### <a name="runtime-scaling"></a>Escalonamento de runtime

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

The unit of scale for Azure Functions is the function app. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente reduzido a zero quando nenhuma função está em execução em um aplicativo de funções.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. There are a few intricacies of scaling behaviors to be aware of:

* Um único aplicativo de funções será escalado verticalmente somente para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.
* For HTTP triggers, new instances will only be allocated at most once every 1 second.
* For non-HTTP triggers, new instances will only be allocated at most once every 30 seconds.

Gatilhos diferentes também podem ter diferentes limites de dimensionamento, como documentado abaixo:

* [Hub de Evento](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que afetarão a qualidade da escala, incluindo a configuração do host, o espaço de runtime e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Modelo de cobrança

Billing for the different plans is described in detail on the [Azure Functions pricing page](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)** . Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

Useful queries and information on how to understand your consumption bill can be found [on the billing FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites de serviço

The following table indicates the limits that apply to function apps when running in the various hosting plans:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
