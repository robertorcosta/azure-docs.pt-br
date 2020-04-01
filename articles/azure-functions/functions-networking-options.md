---
title: Opções de rede do Azure Functions
description: Uma visão geral de todas as opções de rede disponíveis no Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419579"
---
# <a name="azure-functions-networking-options"></a>Opções de rede do Azure Functions

Este artigo descreve os recursos de rede disponíveis entre as opções de hospedagem para funções do Azure. Todas as seguintes opções de rede lhe dão alguma capacidade de acessar recursos sem usar endereços com internet ou restringir o acesso à Internet a um aplicativo de função.

Os modelos de hospedagem têm diferentes níveis de isolamento de rede disponíveis. Escolher o correto ajuda você a atender aos requisitos de isolamento da rede.

Você pode hospedar aplicativos de função de algumas maneiras:

* Você pode escolher entre opções de plano que são executadas em uma infra-estrutura multilocatário, com vários níveis de conectividade de rede virtual e opções de dimensionamento:
    * O [plano consumo](functions-scale.md#consumption-plan) dimensiona dinamicamente em resposta à carga e oferece opções mínimas de isolamento de rede.
    * O [plano Premium](functions-scale.md#premium-plan) também dimensiona dinamicamente e oferece um isolamento de rede mais abrangente.
    * O plano azure [App Service](functions-scale.md#app-service-plan) opera em uma escala fixa e oferece isolamento de rede semelhante ao plano Premium.
* Você pode executar funções em um [ambiente de serviço de aplicativo.](../app-service/environment/intro.md) Este método implanta sua função em sua rede virtual e oferece controle e isolamento de rede completos.

## <a name="matrix-of-networking-features"></a>Matriz de recursos de rede

|                |[Plano de consumo](functions-scale.md#consumption-plan)|[Plano premium](functions-scale.md#premium-plan)|[Plano de serviço de aplicativos](functions-scale.md#app-service-plan)|[Ambiente de serviço de aplicativos](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições de IP de entrada e acesso a sites privados](#inbound-ip-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração de rede virtual](#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Sim|
|[Gatilhos de rede virtuais (não HTTP)](#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|
|[Conexões híbridas](#hybrid-connections) (somente Windows)|❌Não|✅Sim|✅Sim|✅Sim|
|[Restrições de IP de saída](#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|

## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

Você pode usar restrições de IP para definir uma lista de endereços IP com pedido prioritário que são permitidos ou não acesso ao seu aplicativo. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe um "negar tudo" implícito no final da lista. As restrições de IP funcionam com todas as opções de hospedagem de funções.

> [!NOTE]
> Com as restrições de rede em vigor, você pode usar o editor do portal apenas de dentro de sua rede virtual, ou quando você colocou o endereço IP da máquina que você está usando para acessar o portal Azure na lista De Destinatários Seguros. No entanto, você ainda pode acessar quaisquer recursos na guia recursos da **Plataforma** de qualquer máquina.

Para saber mais, consulte [as restrições de acesso estática do Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Acesso a site particular

O acesso privado ao site refere-se a tornar seu aplicativo acessível apenas a partir de uma rede privada, como uma rede virtual Azure.

* O acesso ao site privado está disponível nos planos [Premium,](./functions-premium-plan.md) [Consumption](functions-scale.md#consumption-plan)e [App Service](functions-scale.md#app-service-plan) quando os pontos finais do serviço são configurados.
    * Os pontos finais do serviço podem ser configurados por aplicativo em **recursos da plataforma Configurando** > **Networking** > **restrições** > de acesso**adicionar regra**. As redes virtuais agora podem ser selecionadas como um tipo de regra.
    * Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Tenha em mente que com os pontos finais do serviço, sua função ainda tem acesso total à internet, mesmo com a integração de rede virtual configurada.
* O acesso ao site privado também está disponível dentro de um Ambiente de Serviço de Aplicativo configurado com um balanceador de carga interno (ILB). Para obter mais informações, consulte [Criar e usar um balanceador de carga interna com um ambiente de serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

Para saber como configurar o acesso ao site privado, consulte [Estabelecer o acesso ao site privado do Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integração de rede virtual

A integração de rede virtual permite que seu aplicativo de função acesse recursos dentro de uma rede virtual.
O Azure Functions suporta dois tipos de integração de rede virtual:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A integração de rede virtual no Azure Functions usa infra-estrutura compartilhada com aplicativos web do App Service. Para saber mais sobre os dois tipos de integração de rede virtual, consulte:

* [Integração regional da rede virtual](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integração de rede virtual exigida por gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para saber como configurar a integração de rede virtual, consulte [Integrar um aplicativo de função com uma rede virtual Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integração regional de rede virtual

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Conecte-se aos recursos protegidos do ponto final do serviço

Para fornecer um nível mais alto de segurança, você pode restringir uma série de serviços do Azure a uma rede virtual usando pontos finais de serviço. Em seguida, você deve integrar seu aplicativo de função com essa rede virtual para acessar o recurso. Essa configuração é suportada em todos os planos que suportam a integração de rede virtual.

Para saber mais, consulte [Pontos finais de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restringir sua conta de armazenamento a uma rede virtual

Ao criar um aplicativo de função, você deve criar ou vincular a uma conta de armazenamento Azure de uso geral que suporte o armazenamento Blob, Fila e Tabela. No momento, não é possível usar nenhuma restrição de rede virtual nesta conta. Se você configurar um ponto final de serviço de rede virtual na conta de armazenamento que você está usando para o seu aplicativo de função, essa configuração quebrará seu aplicativo.

Para saber mais, consulte [os requisitos da conta de armazenamento](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Use referências do Key Vault

Você pode usar as referências do Azure Key Vault para usar segredos do Azure Key Vault no aplicativo Azure Functions sem exigir nenhuma alteração de código. Azure Key Vault é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria.

Atualmente, [as referências do Key Vault](../app-service/app-service-key-vault-references.md) não funcionarão se o cofre principal estiver protegido com pontos finais de serviço. Para se conectar a um cofre de chaves usando a integração de rede virtual, você precisa chamar key vault em seu código de aplicativo.

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtuais (não HTTP)

Atualmente, você pode usar funções de gatilho não-HTTP de dentro de uma rede virtual de duas maneiras:

+ Execute seu aplicativo de função em um plano Premium e habilite o suporte ao gatilho de rede virtual.
+ Execute seu aplicativo de função em um plano de serviço de aplicativo ou ambiente de serviço de aplicativo.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plano premium com gatilhos de rede virtual

Quando você executa um plano Premium, você pode conectar funções de gatilho não-HTTP a serviços que são executados dentro de uma rede virtual. Para fazer isso, você deve habilitar o suporte de gatilho de rede virtual para o seu aplicativo de função. A configuração **de suporte ao gatilho de rede virtual** é encontrada no portal [Azure](https://portal.azure.com) em **Configurações do aplicativo Function**.

![Alternação de rede virtual](media/functions-networking-options/virtual-network-trigger-toggle.png)

Você também pode habilitar gatilhos de rede virtuais usando o seguinte comando Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Os gatilhos de rede virtuais são suportados na versão 2.x e acima do tempo de execução functions. Os seguintes tipos de gatilho não-HTTP são suportados.

| Extensão | Versão mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Armazenamento](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou mais|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou mais|

> [!IMPORTANT]
> Quando você habilita o suporte ao gatilho de rede virtual, apenas os tipos de gatilho mostrados na escala de tabela anterior dinamicamente com o aplicativo. Você ainda pode usar gatilhos que não estão na tabela, mas eles não são dimensionados além de sua contagem de instâncias pré-aquecidas. Para obter a lista completa de gatilhos, consulte [Gatilhos e vinculações](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plano de serviço de aplicativo e ambiente de serviço de aplicativos com gatilhos de rede virtual

Quando seu aplicativo de função é executado em um plano de serviço de aplicativo ou em um ambiente de serviço de aplicativo, você pode usar funções de gatilho não-HTTP. Para que suas funções sejam acionadas corretamente, você deve estar conectado a uma rede virtual com acesso ao recurso definido na conexão de gatilho.

Por exemplo, suponha que você deseja configurar o Azure Cosmos DB para aceitar o tráfego apenas a partir de uma rede virtual. Neste caso, você deve implantar seu aplicativo de função em um plano de Serviço de Aplicativo que fornece integração de rede virtual com essa rede virtual. A integração permite que uma função seja acionada por esse recurso Azure Cosmos DB.

## <a name="hybrid-connections"></a>Conexões Híbridas

[Conexões Híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é uma característica do Azure Relay que você pode usar para acessar recursos de aplicativos em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Você não pode usá-lo para acessar sua aplicação. As Conexões Híbridas estão disponíveis para funções que são executadas no Windows em todos, exceto no plano de consumo.

Como usado em Funções Azure, cada conexão híbrida se correlaciona com uma única combinação de host e porta TCP. Isso significa que o ponto final da conexão híbrida pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não sabe ou se importa com o que é o protocolo de aplicação ou o que você está acessando. Ele apenas fornece acesso à rede.

Para saber mais, consulte a documentação do [App Service para Conexões Híbridas](../app-service/app-service-hybrid-connections.md). Essas mesmas etapas de configuração suportam funções do Azure.

>[!IMPORTANT]
> Conexões híbridas só são suportadas em planos do Windows. O Linux não é suportado.

## <a name="outbound-ip-restrictions"></a>Restrições de IP de saída

As restrições de IP de saída estão disponíveis em um plano Premium, plano de serviço de aplicativo ou ambiente de serviço de aplicativo. Você pode configurar restrições de saída para a rede virtual onde o ambiente de serviço do aplicativo é implantado.

Quando você integra um aplicativo de função em um plano Premium ou um plano de Serviço de Aplicativo com uma rede virtual, o aplicativo ainda pode fazer chamadas de saída para a internet por padrão. Ao adicionar a `WEBSITE_VNET_ROUTE_ALL=1`configuração do aplicativo, você força todo o tráfego de saída a ser enviado para sua rede virtual, onde as regras do grupo de segurança da rede podem ser usadas para restringir o tráfego.

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre networking e funções do Azure:

* [Siga o tutorial sobre como começar com a integração de rede virtual](./functions-create-vnet.md)
* [Leia as funções de perguntas frequentes sobre a rede](./functions-networking-faq.md)
* [Saiba mais sobre a integração da rede virtual com o App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Habilite mais recursos de rede e controle com ambientes de serviço de aplicativos](../app-service/environment/intro.md)
* [Conecte-se a recursos individuais no local sem alterações de firewall usando conexões híbridas](../app-service/app-service-hybrid-connections.md)
