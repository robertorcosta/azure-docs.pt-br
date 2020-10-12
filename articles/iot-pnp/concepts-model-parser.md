---
title: Entender o analisador do modelo digital gêmeos | Microsoft Docs
description: Como desenvolvedor, saiba como usar o analisador DTDL para validar modelos
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352107"
---
# <a name="understand-the-digital-twins-model-parser"></a>Entender o analisador do modelo dos Gêmeos Digitais

A DTDL (digital gêmeos Definition Language) é descrita na [especificação DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Os usuários podem usar o pacote NuGet do _digital gêmeos Model Parser_ para validar e consultar um modelo definido em vários arquivos.

## <a name="install-the-dtdl-model-parser"></a>Instalar o analisador de modelo DTDL

O analisador está disponível em NuGet.org com a ID: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Para instalar o analisador, use qualquer Gerenciador de pacotes NuGet compatível, como aquele no Visual Studio ou na `dotnet` CLI.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Usar o analisador para validar um modelo

O modelo que você deseja validar pode ser composto por uma ou mais interfaces descritas em arquivos JSON. Você pode usar o analisador para carregar todos os arquivos em uma determinada pasta e usar o analisador para validar todos os arquivos como um todo, incluindo quaisquer referências entre os arquivos:

1. Crie um `IEnumerable<string>` com uma lista de todo o conteúdo do modelo:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instanciar a `ModelParser` chamada e `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Verifique se há erros de validação. Se o analisador encontrar erros, ele lançará um `AggregateException` com uma lista de mensagens de erro detalhadas:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Inspecione o `Model` . Se a validação for realizada com sucesso, você poderá usar a API do analisador de modelo para inspecionar o modelo. O trecho de código a seguir mostra como iterar todos os modelos analisados e exibe as propriedades existentes:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas

A API do analisador de modelo revisada neste artigo habilita muitos cenários para automatizar ou validar tarefas que dependem de modelos de DTDL. Por exemplo, você pode criar dinamicamente uma interface do usuário com base nas informações no modelo.
