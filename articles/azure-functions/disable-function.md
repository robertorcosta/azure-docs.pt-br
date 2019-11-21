---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions 1.x e 2.x.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233036"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. *Desabilitar* significa fazer com que o runtime igore o gatilho automático que está definido para a função. A maneira de fazer isso depende da versão de runtime e a linguagem de programação:

* Functions 2.x:
  * Uma maneira para todos os idiomas
  * Modo opcional para bibliotecas de classes do C#
* Functions 1.x:
  * Linguagens de script
  * Biblioteca de Classes C#

## <a name="functions-2x---all-languages"></a>Funtions 2.x - todos os idiomas

In Functions 2.x, you disable a function by using an app setting in the format `AzureWebJobs.<FUNCTION_NAME>.Disabled`. You can create and modify this application setting in a number of ways, including by using the [Azure CLI](/cli/azure/) and from your function's **Manage** tab in the [Azure portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

In the Azure CLI, you use the [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command to create and modify the app setting. The following command disables a function named `QueueTrigger` by creating an app setting named `AzureWebJobs.QueueTrigger.Disabled` set it to `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

To re-enable the function, rerun the same command with a value of `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

You can also use the **Function State** switch on the function's **Manage** tab. The switch works by creating and deleting the `AzureWebJobs.<FUNCTION_NAME>.Disabled` app setting.

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - bibliotecas de classes do C#

Em uma biblioteca de classes do Functions 2.x, é recomendável que você use o método que funciona para todos os idiomas. Mas se você preferir, você pode [usar o atributo desabilitar como no Functions 1.x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - linguagens de script

Para linguagens de script, como o script do C# e JavaScript, você deve usar a `disabled` propriedade do arquivo *Function. JSON* para informar para o runtime não disparar uma função. Essa propriedade pode ser definida como `true` ou o nome de uma configuração de aplicativo:

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

You can edit the file in the Azure portal or use the **Function State** switch on the function's **Manage** tab. The portal switch works by changing the *function.json* file.

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - bibliotecas de classes do C#

Em uma biblioteca de classes das Functions 1.x, você deve usar um `Disable` atributo para impedir que uma função que está sendo disparado. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

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

## <a name="next-steps"></a>Próximos passos

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).
