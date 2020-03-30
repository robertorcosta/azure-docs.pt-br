---
title: Como desabilitar funções no Azure Functions
description: Saiba como desativar e ativar funções em Funções Azure.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116154"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. *Desabilitar* significa fazer com que o runtime igore o gatilho automático que está definido para a função. Isso permite que você impeça que uma função específica seja executado sem parar todo o aplicativo de função.

A maneira recomendada de desativar uma função é usando `AzureWebJobs.<FUNCTION_NAME>.Disabled`uma configuração de aplicativo no formato . Você pode criar e modificar essa configuração de aplicativo de várias maneiras, inclusive usando o Cli do [Azure](/cli/azure/) e da guia **Gerenciar** sua função no [portal Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando você desativa uma função acionada HTTP usando os métodos descritos neste artigo, o ponto final ainda pode ser acessível ao ser executado no computador local.  

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Na CLI do Azure, [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) você usa o comando para criar e modificar a configuração do aplicativo. O comando a seguir `QueueTrigger` desativa uma `AzureWebJobs.QueueTrigger.Disabled` função nomeada `true`criando uma configuração de aplicativo chamada set it to . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para reativar a função, execute o mesmo `false`comando com um valor de .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Use o Portal

Você também pode usar o interruptor **Estado de função** na guia **Gerenciar** da função. O switch funciona criando e `AzureWebJobs.<FUNCTION_NAME>.Disabled` excluindo a configuração do aplicativo.

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Outros métodos

Embora o método de configuração do aplicativo seja recomendado para todos os idiomas e todas as versões em tempo de execução, existem várias outras maneiras de desativar funções. Esses métodos, que variam de acordo com a linguagem e a versão em tempo de execução, são mantidos para compatibilidade retrógrada. 

### <a name="c-class-libraries"></a>Biblioteca de Classes C#

Em uma função de biblioteca de `Disable` classe, você também pode usar o atributo para evitar que a função seja acionada. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

O atributo sem parâmetro de construtor exige que você recompilar e reimplanta o projeto para alterar o estado de desabilitado da função. Uma maneira mais flexível para usar o atributo deve incluir um parâmetro de construtor que se refere a uma configuração de aplicativo booliano, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Esse método permite habilitar e desabilitar a função, alterando a configuração de aplicativo, sem recompilar ou reinstalar. Alterar uma configuração de aplicativo faz com que o aplicativo de funções seja reiniciado, portanto, a alteração de estado desabilitado é reconhecida imediatamente.

> [!IMPORTANT]
> O `Disabled` atributo é a única maneira de desabilitar uma função de biblioteca de classe. Gerado o arquivo *function. JSON* para uma função de biblioteca de classe não se destina a ser editado diretamente. Se você editar esse arquivo, tudo o que faria para a `disabled` propriedade não terá efeito.
>
> O mesmo vale para o **estado de função** ative a guia **Gerenciar**, assim que funcionar, alterando o arquivo *Function. JSON*.
>
> Além disso, observe que o portal pode indicar que a função está desabilitada quando não estiver.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - linguagens de script

Na versão 1.x, você `disabled` também pode usar a propriedade do arquivo *function.json* para dizer ao tempo de execução para não acionar uma função. Este método só funciona para scripting de idiomas como c# script e JavaScript. A `disabled` propriedade pode `true` ser definida para ou para o nome de uma configuração do aplicativo:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
ou 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

No segundo exemplo, a função está desabilitada quando há uma configuração de aplicativo chamada IS_DISABLED e é definida como `true` ou 1.

Você pode editar o arquivo no portal Azure ou usar o interruptor **Estado de função** na guia **Gerenciar** da função. O switch do portal funciona alterando o arquivo *function.json.*

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).
