---
title: Gatilho de aquecimento das funções do Azure
description: Entenda como usar o gatilho de aquecimento em Funções Azure.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funções, funções, processamento de eventos, aquecimento, início a frio, premium, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167322"
---
# <a name="azure-functions-warm-up-trigger"></a>Gatilho de aquecimento das funções do Azure

Este artigo explica como trabalhar com o gatilho de aquecimento em Funções Azure. O gatilho de aquecimento é suportado apenas para aplicativos de função em execução em um [plano Premium](functions-premium-plan.md). Um gatilho de aquecimento é invocado quando uma instância é adicionada para escalar um aplicativo de função em execução. Você pode usar um gatilho de aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-aquecimento](./functions-premium-plan.md#pre-warmed-instances) para que suas funções estejam prontas para iniciar o processamento de solicitações imediatamente. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

O pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão **3.0.5 ou superior,** é necessário. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Gatilho

O gatilho de aquecimento permite definir uma função que será executada em uma nova instância quando for adicionada ao seu aplicativo em execução. Você pode usar uma função de aquecimento para abrir conexões, carregar dependências ou executar qualquer outra lógica personalizada antes que seu aplicativo comece a receber tráfego. 

O gatilho de aquecimento destina-se a criar dependências compartilhadas que serão usadas pelas outras funções em seu aplicativo. [Veja exemplos de dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

Observe que o gatilho de aquecimento só é chamado durante operações de scale-out, não durante reinicializações ou outras startups não dimensionadas. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho de aquecimento. O carregamento preguiçoso é um bom padrão para conseguir isso.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo. Um atributo de valor de retorno não é necessário.


* Sua função deve ```warmup``` ser nomeada (case-insensitive) e pode haver apenas uma função de aquecimento por aplicativo.
* Para usar o aquecimento como uma função de biblioteca de classe .NET, certifique-se de ter uma referência de pacote ao **Microsoft.Azure.WebJobs.Extensões >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Comentários de espaço reservado mostram onde no aplicativo declarar e inicializar dependências compartilhadas. 
[Saiba mais sobre dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Script do C#](#tab/csharp-script)


O exemplo a seguir mostra um gatilho de aquecimento em um arquivo *function.json* e uma [função de script C#](functions-reference-csharp.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo.

Sua função deve ```warmup``` ser nomeada (case-insensitive), e pode haver apenas uma função de aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script C# que associa a um `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra um gatilho de aquecimento em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo.

Sua função deve ```warmup``` ser nomeada (case-insensitive) e pode haver apenas uma função de aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra um gatilho de aquecimento em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo.

Sua função deve ```warmup``` ser nomeada (case-insensitive) e pode haver apenas uma função de aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra um gatilho de aquecimento que é executado quando cada nova instância é adicionada ao seu aplicativo.

Sua função deve `warmup` ser nomeada (case-insensitive) e pode haver apenas uma função de aquecimento por aplicativo.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classe C#,](functions-dotnet-class-library.md)o atributo `WarmupTrigger` está disponível para configurar a função.

# <a name="c"></a>[C #](#tab/csharp)

Este exemplo demonstra como usar o atributo [de aquecimento.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Observe que sua função ```Warmup``` deve ser chamada e só pode haver uma função de aquecimento por aplicativo.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Para um exemplo completo, veja o exemplo do [gatilho](#trigger---example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

O gatilho de aquecimento não é suportado em Java como um atributo.

---

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de `WarmupTrigger` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **type** | n/d| Obrigatório – deve ser definido como `warmupTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Necessário - o nome da variável usado no código de função.|

## <a name="trigger---usage"></a>Gatilho - uso

Nenhuma informação adicional é fornecida a uma função acionada de aquecimento quando for invocada.

## <a name="trigger---limits"></a>Gatilho - limites

* O gatilho de aquecimento só está disponível para aplicativos em execução no [plano Premium](./functions-premium-plan.md).
* O gatilho de aquecimento só é chamado durante operações de scale up, não durante reinicializações ou outras startups não-dimensionadas. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho de aquecimento. O carregamento preguiçoso é um bom padrão para conseguir isso.
* O gatilho de aquecimento não pode ser invocado uma vez que uma instância já esteja em execução.
* Só pode haver uma função de gatilho de aquecimento por aplicativo de função.

## <a name="next-steps"></a>Próximas etapas

[Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
