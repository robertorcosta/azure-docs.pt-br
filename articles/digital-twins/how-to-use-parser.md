---
title: Analisar e validar modelos
titleSuffix: Azure Digital Twins
description: Saiba como usar a biblioteca do analisador para analisar modelos de DTDL.
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5a6f05835362dbcde36b1ab9cc3782b172b43f7c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079153"
---
# <a name="dtdl-client-side-parser-library"></a>Biblioteca do analisador do lado do cliente DTDL

Os [modelos](concepts-models.md) no Azure digital gêmeos são definidos usando o DTDL (digital gêmeos Definition Language) baseado em JSON-ld. Para casos em que é útil analisar seus modelos, uma biblioteca de análise de DTDL é fornecida no NuGet.org como uma biblioteca do lado do cliente: [Microsoft. Azure. DigitalTwins. Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/).

Essa biblioteca fornece acesso de modelo às definições de DTDL, essencialmente agindo como equivalente à reflexão do C# para DTDL. Essa biblioteca pode ser usada independentemente de qualquer [SDK do gêmeos digital do Azure](how-to-use-apis-sdks.md), especialmente para validação de DTDL em um editor de texto ou Visual. É útil para verificar se os arquivos de definição de modelo são válidos antes de tentar carregá-los no serviço.

Para usar a biblioteca do analisador, você fornece um conjunto de documentos DTDL. Normalmente, você recuperaria esses documentos de modelo do serviço, mas também poderá tê-los disponíveis localmente, se o cliente tiver sido responsável por carregá-los no serviço em primeiro lugar. 

Este é o fluxo de trabalho geral para usar o analisador:
1. Recupere alguns ou todos os documentos do DTDL do serviço.
2. Passe os documentos de DTDL retornados e na memória para o analisador.
3. O analisador validará o conjunto de documentos passados para ele e retornará informações detalhadas sobre o erro. Essa capacidade é útil em cenários de editor.
4. Use as APIs do analisador para continuar analisando os modelos incluídos no conjunto de documentos. 

Os recursos do analisador incluem:
* Obter todas as interfaces de modelo implementadas (o conteúdo da `extends` seção da interface).
* Obtenha todas as propriedades, telemetria, comandos, componentes e relações declaradas no modelo. Esse comando também obtém todos os metadados incluídos nessas definições e usa a herança ( `extends` seções) em conta.
* Obter todas as definições de modelo complexas.
* Determine se um modelo pode ser atribuído de outro modelo.

> [!NOTE]
> Dispositivos [de IoT plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) usam uma variante de sintaxe pequena para descrever sua funcionalidade. Essa variante de sintaxe é um subconjunto compatível semanticamente do DTDL que é usado no gêmeos digital do Azure. Ao usar a biblioteca do analisador, você não precisa saber qual variante de sintaxe foi usada para criar o DTDL para sua grade digital. O analisador sempre retornará, por padrão, o mesmo modelo para a sintaxe PnP e gêmeos digital do Azure.

## <a name="use-the-dtdl-validator-sample"></a>Usar o exemplo de validador DTDL

Há um código de exemplo disponível que pode validar documentos de modelo para garantir que o DTDL seja válido. Ele é criado na biblioteca do analisador DTDL e não é independente de linguagem. Encontre aqui: [exemplo de validador de DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

O exemplo do validador pode ser usado como um utilitário de linha de comando para validar uma árvore de diretórios de arquivos DTDL. Ele também fornece um modo interativo. O código-fonte mostra exemplos de como usar a biblioteca do analisador.

Na pasta do exemplo do validador DTDL, consulte o arquivo *README.MD* para obter instruções sobre como empacotar o exemplo em um executável independente.

Depois de criar um pacote autocontido e adicionar o executável ao seu caminho, você poderá executar o validador com esse comando em um console do em seu computador:

```cmd/sh
DTDLValidator
```

Com as opções padrão, o exemplo procurará por `*.json` arquivos no diretório atual e em todos os subdiretórios. Você também pode adicionar a opção a seguir para fazer com que a pesquisa de exemplo no diretório indicado e todos os subdiretórios de arquivos com a extensão *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Você pode adicionar a `-i` opção para o exemplo entrar no modo interativo:

```cmd/sh
DTDLValidator -i
```

Para obter mais informações sobre este exemplo, consulte o código-fonte ou execute `DTDLValidator --help` .

## <a name="use-the-parser-library-in-code"></a>Usar a biblioteca do analisador no código

Você também pode usar a biblioteca do analisador diretamente, para coisas como validação de modelos em seu próprio aplicativo ou para gerar interface do usuário, painéis e relatórios dinâmicos, controlados por modelos.

Para dar suporte ao exemplo de código do analisador abaixo, considere vários modelos definidos em uma instância do gêmeos digital do Azure:

> [!TIP] 
> O `dtmi:com:contoso:coffeeMaker` modelo está usando a sintaxe do *modelo de funcionalidade* , o que implica que ele foi instalado no serviço conectando um dispositivo PnP expondo esse modelo.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

O código a seguir mostra um exemplo de como usar a biblioteca do analisador para refletir sobre essas definições em C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Quando terminar de escrever seus modelos, consulte como carregá-los (e outras operações de gerenciamento) com as APIs DigitalTwinsModels:
* [*Instruções: Gerenciar modelos personalizados*](how-to-manage-model.md)