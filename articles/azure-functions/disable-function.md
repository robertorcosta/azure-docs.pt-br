---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8922edb7aaa41bcf50dcce5257b6600f1bde224a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115551"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. Para *desabilitar* uma função significa fazer com que o tempo de execução ignore o gatilho automático definido para a função. Isso permite impedir que uma função específica seja executada sem interromper o aplicativo de funções inteiro.

A maneira recomendada para desabilitar uma função é usando uma configuração de aplicativo no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Você pode criar e modificar essa configuração de aplicativo de várias maneiras, incluindo usando o [CLI do Azure](/cli/azure/) e da guia **gerenciar** da função na [portal do Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando você desabilita uma função disparada por HTTP usando os métodos descritos neste artigo, o ponto de extremidade ainda pode ser acessado quando executado no computador local.  

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

No CLI do Azure, você usa o [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando para criar e modificar a configuração do aplicativo. O comando a seguir desabilita uma função denominada `QueueTrigger` criando uma configuração de aplicativo chamada `AzureWebJobs.QueueTrigger.Disabled` set to `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para reabilitar a função, execute novamente o mesmo comando com um valor de `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Usar o portal

Você também pode usar os botões **habilitar** e **desabilitar** na página **visão geral** da função. Esses botões funcionam criando e excluindo a `AzureWebJobs.<FUNCTION_NAME>.Disabled` configuração do aplicativo.

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

> [!NOTE]  
> A funcionalidade de teste integrada ao portal ignora a `Disabled` configuração. Isso significa que uma função desabilitada ainda é executada quando iniciada na janela de **teste** no Portal. 

## <a name="other-methods"></a>Outros métodos

Embora o método de configuração de aplicativo seja recomendado para todas as linguagens e todas as versões de tempo de execução, há várias outras maneiras de desabilitar funções. Esses métodos, que variam por idioma e versão de tempo de execução, são mantidos para compatibilidade com versões anteriores. 

### <a name="c-class-libraries"></a>Biblioteca de Classes C#

Em uma função de biblioteca de classes, você também pode usar o `Disable` atributo para impedir que a função seja disparada. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

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

Na versão 1. x, você também pode usar a `disabled` Propriedade do arquivo *Function. JSON* para informar ao tempo de execução para não disparar uma função. Esse método funciona apenas para linguagens de script, como script C# e JavaScript. A `disabled` propriedade pode ser definida como `true` ou para o nome de uma configuração de aplicativo:

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

Você pode editar o arquivo no portal do Azure ou usar os botões **habilitar** e **desabilitar** na página de **visão geral** da função. A opção portal funciona, alterando o arquivo *function.json*.


## <a name="next-steps"></a>Próximas etapas

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).
