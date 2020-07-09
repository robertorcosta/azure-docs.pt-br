---
title: Criar SDKs personalizados para o gêmeos digital do Azure com o autorest
titleSuffix: Azure Digital Twins
description: Consulte como gerar SDKs personalizados para usar o gêmeos digital do Azure com idiomas diferentes do C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 895e33a111fe5bb881d198ee4995b9534ca3d528
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135872"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Criar SDKs personalizados para o gêmeos digital do Azure usando o REST

No momento, o único SDK do plano de dados publicado para interagir com as APIs do gêmeos digital do Azure é para .NET (C#). Você pode ler sobre o SDK do .NET e as APIs em geral, em [How-to: Use the Azure digital gêmeos APIs and SDKs](how-to-use-apis-sdks.md). Se você estiver trabalhando em outra linguagem, este artigo mostrará como gerar seu próprio SDK no idioma de sua escolha, usando o REST.

## <a name="set-up-your-machine"></a>Configurar seu computador

Para gerar um SDK, será necessário:
* O [REST](https://github.com/Azure/autorest), versão 2.0.4413 (versão 3 não tem suporte no momento)
* [Node.js](https://nodejs.org) como um pré-requisito para o REST
* O [arquivo openapi (Azure digital gêmeos Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) intitulado *digitaltwins.jsem*e sua pasta de exemplos que o acompanha. Baixe o arquivo do Swagger e sua pasta de exemplos em seu computador local.

Depois que o computador estiver equipado com tudo, na lista acima, você estará pronto para usar o REST para criar o SDK.

## <a name="create-the-sdk-with-autorest"></a>Criar o SDK com o REST 

Se você tiver Node.js instalado, poderá executar esse comando para verificar se você tem a versão correta do REST instalado:
```cmd/sh
npm install -g autorest@2.0.4413
```

Para executar o REST no arquivo do Azure digital gêmeos Swagger, siga estas etapas:
1. Copie o arquivo Swagger do Azure digital gêmeos e sua pasta de exemplos que o acompanha em um diretório de trabalho.
2. Use uma janela de prompt de comando para alternar para esse diretório de trabalho.
3. Execute o comando executar como a seguir. Substitua o `<language>` espaço reservado pelo idioma de sua escolha: `--python` , `--java` ,, `--go` etc. (você pode encontrar a lista completa de opções no [Leiame do REST](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Como resultado, você verá uma nova pasta chamada *ADTApi* em seu diretório de trabalho. Os arquivos do SDK gerados terão o namespace *ADTApi*, que você continuará a usar no restante dos exemplos de uso neste artigo.

O REST oferece suporte a uma ampla variedade de geradores de código de linguagem.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Adicionar o SDK a um projeto do Visual Studio

Você pode incluir os arquivos gerados pelo autorest diretamente em uma solução .NET. No entanto, como você provavelmente precisará do SDK do Azure digital gêmeos em vários projetos separados (seus aplicativos cliente, aplicativos Azure Functions e assim por diante), pode ser útil criar um projeto separado (uma biblioteca de classes do .NET) a partir dos arquivos gerados. Você pode então incluir esse projeto de biblioteca de classes em várias soluções como uma referência de projeto.

Esta seção fornece instruções sobre como criar o SDK como uma biblioteca de classes, que é seu próprio projeto e pode ser incluído em outros projetos. Essas etapas dependem do **Visual Studio** (você pode instalar a versão mais recente [aqui](https://visualstudio.microsoft.com/downloads/)).

Aqui estão as etapas para fazer isso:

1. Criar uma nova solução do Visual Studio para uma biblioteca de classes
2. Usar *ADTApi* como o nome do projeto
3. No Gerenciador de soluções, selecione o projeto *ADTApi* da solução gerada e escolha *Adicionar > item existente...*
4. Localize a pasta na qual você gerou o SDK e selecione os arquivos no nível raiz
5. Pressione "OK"
6. Adicione uma pasta ao projeto (selecione o projeto com o botão direito do mouse em Gerenciador de Soluções e escolha *adicionar > nova pasta*)
7. Nomear os *modelos* de pasta
8. Selecione a pasta *modelos* no Gerenciador de soluções e selecione *Adicionar > item existente...*
9. Selecione os arquivos na pasta *modelos* do SDK gerado e pressione "OK"

Para compilar o SDK com êxito, seu projeto precisará destas referências:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Para adicioná-los, abra *ferramentas > Gerenciador de pacotes nuget > gerenciar pacotes NuGet para solução...*.

1. No painel, certifique-se de que a guia *procurar* esteja selecionada
2. Pesquisar *Microsoft. REST*
3. Selecione os pacotes *ClientRuntime* e *ClientRuntime. Azure* e adicione-os à sua solução

Agora você pode criar o projeto e incluí-lo como uma referência de projeto em qualquer aplicativo de gêmeos digital do Azure que você escreve.

## <a name="general-guidelines-for-generated-sdks"></a>Diretrizes gerais para SDKs gerados

Esta seção contém informações gerais sobre o e as diretrizes para usar o SDK gerado.

### <a name="synchronous-and-asynchronous-calls"></a>Chamadas síncronas e assíncronas

Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

### <a name="typed-and-untyped-data"></a>Dados digitados e não tipados

As chamadas à API REST geralmente retornam objetos fortemente tipados. No entanto, como o Azure digital gêmeos permite que os usuários definam seus próprios tipos personalizados para o gêmeos, não há como definir previamente os dados de retorno estáticos para muitas chamadas de gêmeos digitais do Azure. Em vez disso, as APIs retornam tipos de wrapper fortemente tipados, quando aplicável, e os dados de texto cruzado personalizado estão em objetos Json.NET (usados sempre que o tipo de dados "Object" aparece nas assinaturas de API). Você pode converter esses objetos adequadamente no seu código.

### <a name="error-handling"></a>Tratamento de erros

Sempre que ocorrer um erro no SDK (incluindo erros de HTTP, como 404), o SDK gerará uma exceção. Por esse motivo, é importante encapsular todas as chamadas de API nos blocos try/catch.

Aqui está um trecho de código que tenta adicionar um entrelaçamento e captura todos os erros nesse processo:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Paginamento

O REST gera dois tipos de padrões de paginação para o SDK:
* Um para todas as APIs, exceto a API de consulta
* Um para a API de consulta

No padrão de paginação não consulta, há duas versões de cada chamada:
* Uma versão para fazer a chamada inicial (como `DigitalTwins.ListEdges()` )
* Uma versão para obter páginas subsequentes, sufixadas com "Next" (como `DigitalTwins.ListEdgesNext()` )

Aqui está um trecho de código que mostra como recuperar uma lista paginável de relações de saída do Azure digital gêmeos:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

O segundo padrão é gerado somente para a API de consulta. Ele usa um `continuationToken` explicitamente.

Aqui está um exemplo com esse padrão:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Próximas etapas

Percorra as etapas para criar um aplicativo cliente onde você pode usar seu SDK:
* [Tutorial: codificar um aplicativo cliente](tutorial-code.md)