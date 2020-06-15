---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ee701e8df8faddef9bbdb16e7a1048c4dc2e40a5
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848732"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. *Desabilitar*uma função significa fazer com que o runtime ignore o gatilho automático que está definido para a função. Isso permite impedir que uma função específica seja executada sem interromper todo o aplicativo de funções.

A maneira recomendada de desabilitar uma função é usar uma configuração de aplicativo no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Você pode criar e modificar essa configuração de aplicativo de várias maneiras, usando a [CLI do Azure](/cli/azure/) e a partir da guia **Gerenciar** da sua função no [portal do Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando você desabilita uma função disparada por HTTP usando os métodos descritos neste artigo, o ponto de extremidade ainda pode ser acessado se estiver em execução no computador local.  

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Na CLI do Azure, use o comando [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) para criar e modificar a configuração do aplicativo. O comando a seguir desabilita uma função chamada `QueueTrigger` criando uma configuração de aplicativo chamada `AzureWebJobs.QueueTrigger.Disabled` e definindo-a como `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para habilitar novamente a função, execute o mesmo comando outra vez com um valor de `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Uso do Portal

Use os botões **Habilitar** e **Desabilitar** na página **Visão geral** da função. Esses botões criam e excluem a configuração de aplicativo `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

> [!NOTE]  
> A funcionalidade de teste integrada ao portal ignora a configuração `Disabled`. Isso significa que uma função desabilitada continua sendo executada quando é iniciada na janela **Testar** do portal. 

## <a name="other-methods"></a>Outros métodos

Embora o método de configuração de aplicativo seja recomendado para todas as linguagens e todas as versões de runtime, existem várias outras maneiras de desabilitar funções. Esses métodos, que variam por linguagem e versão de runtime, são mantidos para fins de compatibilidade com versões anteriores. 

### <a name="c-class-libraries"></a>Biblioteca de Classes C#

Também é possível usar o atributo `Disable` para impedir que a função seja disparada em uma função da biblioteca de classes. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

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

Na versão 1.x, você também pode usar a propriedade `disabled` do arquivo *function.json* para dizer ao runtime para não disparar uma função. Esse método funciona apenas para linguagens de script, como script de C# e JavaScript. A propriedade `disabled` pode ser definida como `true` ou com o nome de uma configuração de aplicativo:

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

>[!IMPORTANT]  
>O portal agora usa as configurações de aplicativo para desabilitar as funções v1.x. Pode ocorrer um erro quando uma configuração de aplicativo apresenta um conflito com o arquivo function.json. Remova a propriedade `disabled` do arquivo function.json para evitar erros. 


## <a name="next-steps"></a>Próximas etapas

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).
