---
title: Implantação de tempo de inatividade zero para funções duráveis
description: Saiba como ativar sua orquestração de funções duráveis para implantações de tempo zero de inatividade.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231265"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implantação de tempo de inatividade zero para funções duráveis

O [modelo de execução confiável](durable-functions-checkpointing-and-replay.md) de Funções Duráveis exige que as orquestrações sejam determinísticas, o que cria um desafio adicional a considerar quando você implantar atualizações. Quando uma implantação contém alterações nas assinaturas da função de atividade ou na lógica orquestradora, as instâncias de orquestração a bordo falham. Essa situação é especialmente um problema para casos de orquestrações de longa duração, que podem representar horas ou dias de trabalho.

Para evitar que essas falhas aconteçam, você tem duas opções: 
- Atrase sua implantação até que todas as instâncias de orquestração em execução tenham sido concluídas.
- Certifique-se de que quaisquer instâncias de orquestração em execução usem as versões existentes de suas funções. 

> [!NOTE]
> Este artigo fornece orientação para aplicativos de funções que visam funções duráveis 1.x. Ele não foi atualizado para explicar as alterações introduzidas nas Funções Duráveis 2.x. Para obter mais informações sobre as diferenças entre as versões de extensão, consulte [as versões Durable Functions](durable-functions-versions.md).

O gráfico a seguir compara as três principais estratégias para alcançar uma implantação de tempo zero de inatividade para funções duráveis: 

| Estratégia |  Quando usar | Vantagens | Desvantagens |
| -------- | ------------ | ---- | ---- |
| [Controle de versão](#versioning) |  Aplicações que não experimentam [mudanças](durable-functions-versioning.md) freqüentes de quebra. | Simples de implementar. |  Aumento do tamanho do aplicativo de função na memória e número de funções.<br/>Duplicação de código. |
| [Verificação de status com slot](#status-check-with-slot) | Um sistema que não tem orquestrações de longa duração que duram mais de 24 horas ou frequentemente sobrepõem orquestrações. | Base de código simples.<br/>Não requer gerenciamento adicional de aplicativos de função. | Requer gerenciamento adicional de conta de armazenamento ou hub de tarefas.<br/>Requer períodos de tempo em que nenhuma orquestração está sendo executado. |
| [Roteamento de aplicativos](#application-routing) | Um sistema que não tem períodos de tempo em que as orquestrações não estão funcionando, como aqueles períodos de tempo com orquestrações que duram mais de 24 horas ou com orquestrações frequentemente sobrepostas. | Lida com novas versões de sistemas com orquestrações continuamente em execução que têm mudanças de quebra. | Requer um roteador de aplicativos inteligente.<br/>Poderia maximizar o número de aplicativos de função permitidos por sua assinatura. O padrão é 100. |

## <a name="versioning"></a>Controle de versão

Defina novas versões de suas funções e deixe as versões antigas em seu aplicativo de função. Como você pode ver no diagrama, a versão de uma função torna-se parte de seu nome. Como as versões anteriores das funções são preservadas, as instâncias de orquestração a bordo podem continuar a referencia-las. Enquanto isso, solicitações de novas instâncias de orquestração exigem a versão mais recente, que sua função cliente de orquestração pode referenciar a partir de uma configuração de aplicativo.

![Estratégia de versionamento](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Nesta estratégia, todas as funções devem ser copiadas, e suas referências a outras funções devem ser atualizadas. Você pode facilitar escrevendo um roteiro. Aqui está um [projeto de amostra](https://github.com/TsuyoshiUshio/DurableVersioning) com um script de migração.

>[!NOTE]
>Essa estratégia usa slots de implantação para evitar o tempo de inatividade durante a implantação. Para obter informações mais detalhadas sobre como criar e usar novos slots de implantação, consulte [os slots de implantação do Azure Functions](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verificação de status com slot

Enquanto a versão atual do aplicativo de função estiver sendo executado no seu slot de produção, implante a nova versão do seu aplicativo de função no seu slot de preparação. Antes de trocar seus slots de produção e encenação, verifique se há alguma instância de orquestração em execução. Depois que todas as instâncias de orquestração estiverem completas, você pode fazer a troca. Essa estratégia funciona quando você tem períodos previsíveis quando não há instâncias de orquestração em vôo. Esta é a melhor abordagem quando suas orquestrações não são longas e quando suas execuções de orquestração não se sobrepõem frequentemente.

### <a name="function-app-configuration"></a>Configuração do aplicativo de função

Use o seguinte procedimento para configurar este cenário.

1. [Adicione slots de implantação](../functions-deployment-slots.md#add-a-slot) ao seu aplicativo de função para encenação e produção.

1. Para cada slot, defina a configuração do [aplicativo AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) como a seqüência de conexão de uma conta de armazenamento compartilhado. Esta seqüência de conexão de conta de armazenamento é usada pelo tempo de execução funções do Azure. Essa conta é usada pelo tempo de execução do Azure Functions e gerencia as chaves da função.

1. Para cada slot, crie uma nova `DurableManagementStorage`configuração de aplicativo, por exemplo, . Defina seu valor para a seqüência de conexão de diferentes contas de armazenamento. Essas contas de armazenamento são usadas pela extensão Durable Functions para [execução confiável](durable-functions-checkpointing-and-replay.md). Use uma conta de armazenamento separada para cada slot. Não marque essa configuração como uma configuração de slot de implantação.

1. Na [seção durableTask do arquivo host.json](durable-functions-bindings.md#hostjson-settings)do `azureStorageConnectionStringName` aplicativo de função, especifique como o nome da configuração do aplicativo que você criou na etapa 3.

O diagrama a seguir mostra a configuração descrita de slots de implantação e contas de armazenamento. Neste cenário potencial de pré-implantação, a versão 2 de um aplicativo de função está sendo executado no slot de produção, enquanto a versão 1 permanece no slot de preparação.

![Slots de implantação e contas de armazenamento](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>exemplos host.json

Os seguintes fragmentos de JSON são exemplos da configuração de seqüência de string no arquivo *host.json.*

#### <a name="functions-20"></a>Funções 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Configuração do gasoduto CI/CD

Configure seu pipeline de CI/CD para implantar somente quando seu aplicativo de função não tiver instâncias de orquestração pendentes ou em execução. Quando você estiver usando o Azure Pipelines, você pode criar uma função que verifica essas condições, como no exemplo a seguir:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Em seguida, configure o portão de preparação para esperar até que nenhuma orquestração esteja em execução. Para obter mais informações, consulte [Liberar o controle de implantação usando portões](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Portão de implantação](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

O Azure Pipelines verifica seu aplicativo de função para executar instâncias de orquestração antes que sua implantação seja iniciada.

![Portão de implantação (em execução)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Agora, a nova versão do seu aplicativo de função deve ser implantada no slot de preparação.

![Slot de preparação](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Finalmente, troque os slots. 

As configurações do aplicativo que não estão marcadas como configurações de slot de implantação também são trocadas, de modo que o aplicativo versão 2 mantém sua referência à conta de armazenamento A. Como o estado de orquestração é rastreado na conta de armazenamento, quaisquer orquestrações executadas no aplicativo versão 2 continuam a ser executadas no novo slot sem interrupção.

![Slot de implantação](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Para usar a mesma conta de armazenamento para ambos os slots, você pode alterar os nomes de seus hubs de tarefas. Neste caso, você precisa gerenciar o estado dos seus slots e as configurações do HubName do seu aplicativo. Para saber mais, consulte [Hubs task em Funções Duráveis](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Roteamento de aplicativos

Essa estratégia é a mais complexa. No entanto, ele pode ser usado para aplicativos de função que não têm tempo entre orquestrações em execução.

Para esta estratégia, você deve criar um *roteador* de aplicativos na frente de suas Funções Duráveis. Este roteador pode ser implementado com funções duráveis. O roteador tem a responsabilidade de:

* Implantar o aplicativo de função.
* Gerencie a versão de Funções Duráveis. 
* Solicitações de orquestração de rota para funções de aplicativos.

Na primeira vez que uma solicitação de orquestração é recebida, o roteador faz as seguintes tarefas:

1. Cria um novo aplicativo de função no Azure.
2. Implanta o código do seu aplicativo de função para o novo aplicativo de função no Azure.
3. Encaminha o pedido de orquestração para o novo aplicativo.

O roteador gerencia o estado de qual versão do código do seu aplicativo é implantada para qual aplicativo de função no Azure.

![Roteamento de aplicativos (primeira vez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

O roteador direciona as solicitações de implantação e orquestração para o aplicativo de função apropriado com base na versão enviada com a solicitação. Ele ignora a versão de patch.

Quando você implanta uma nova versão do seu aplicativo sem uma alteração de quebra, você pode incrementar a versão de patch. O roteador é implantado no aplicativo de função existente e envia solicitações para as versões antigas e novas do código, que são roteadas para o mesmo aplicativo de função.

![Roteamento do aplicativo (sem alteração de quebra)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Quando você implanta uma nova versão do seu aplicativo com uma mudança de ruptura, você pode incrementar a versão principal ou menor. Em seguida, o roteador de aplicativos cria um novo aplicativo de função no Azure, implanta para ele e encaminha solicitações para a nova versão do seu aplicativo para ele. No diagrama a seguir, as orquestrações em execução na versão 1.0.1 do aplicativo continuam sendo realizadas, mas as solicitações para a versão 1.1.0 são encaminhadas para o novo aplicativo de função.

![Roteamento do aplicativo (alteração de quebra)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

O roteador monitora o status das orquestrações na versão 1.0.1 e remove aplicativos depois que todas as orquestrações são concluídas. 

### <a name="tracking-store-settings"></a>Rastreamento das configurações do armazenamento

Cada aplicativo de função deve usar filas de agendamento separadas, possivelmente em contas de armazenamento separadas. Se você quiser consultar todas as instâncias de orquestrações em todas as versões do seu aplicativo, você pode compartilhar tabelas de instância e histórico em seus aplicativos de função. Você pode compartilhar tabelas `trackingStoreConnectionStringName` `trackingStoreNamePrefix` configurando as configurações e configurações no arquivo [de configurações host.json](durable-functions-bindings.md#host-json) para que todos usem os mesmos valores.

Para obter mais informações, consulte [Gerenciar instâncias em Funções Duráveis no Azure](durable-functions-instance-management.md).

![Rastreamento das configurações do armazenamento](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Versão funções duráveis](durable-functions-versioning.md)

