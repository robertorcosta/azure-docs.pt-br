---
title: Atualize para Azure Search .NET SDK versão 9
titleSuffix: Azure Cognitive Search
description: Migre o código para o Azure Search .NET SDK versão 9 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793012"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Atualize para Azure Search .NET SDK versão 9

Se você estiver usando a versão 7.0-preview ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar seu aplicativo para usar a versão 9.

> [!NOTE]
> Se você deseja usar a versão 8.0-preview para avaliar recursos que ainda não estão disponíveis, você também pode seguir as instruções deste artigo para atualizar para 8.0-visualização de versões anteriores.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 9 do Azure Search .NET SDK contém muitas alterações em desumanas versões anteriores. Algumas delas são mudanças de separação, mas elas só devem exigir alterações relativamente pequenas no seu código. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 4.0-preview ou mais antiga, você deve atualizar para a versão 5 primeiro, e, em seguida, atualizar para a versão 9. Consulte [Atualizando para o Azure Search .NET SDK versão 5](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
A versão 9 do Azure Search .NET SDK tem como alvo a versão mais recente geralmente disponível da API Azure Search REST, especificamente 2019-05-06. Isso possibilita o uso de vários recursos novos do Azure Search por meio de um aplicativo .NET, incluindo o seguinte:

* [O enriquecimento de IA](cognitive-search-concept-intro.md) é a capacidade de extrair texto de imagens, blobs e outras fontes de dados não estruturadas - enriquecendo o conteúdo para torná-lo mais pesquisável em um índice de Pesquisa Do Azure.
* O suporte para [tipos complexos](search-howto-complex-data-types.md) permite modelar quase qualquer estrutura JSON aninhada em um índice de Pesquisa Azure.
* [O preenchimento automático](search-autocomplete-tutorial.md) fornece uma alternativa à API **sugerir** para implementar o comportamento de pesquisa como você tipo. O preenchimento automático "termina" a palavra ou expressão que um usuário está digitando no momento.
* [O modo de análise JsonLines](search-howto-index-json-blobs.md), parte da indexação do Azure Blob, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novos recursos de visualização na versão 8.0-preview
A versão 8.0-preview do Azure Search .NET SDK tem como alvo a versão API 2017-11-11-Preview. Esta versão inclui todos os mesmos recursos da versão 9, além disso:

* [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso é um novo recurso de visualização. Além da criptografia incorporada em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia onde você é o único proprietário das chaves.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, você precisará corrigir cada erro de compilação. Consulte [Breaking changes in version 9](#ListOfChanges) para obter detalhes sobre como resolver cada potencial erro de compilação.

Você pode ver avisos de build adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que deve ser usado no lugar do recurso preterido. Por exemplo, se o `DataSourceType.DocumentDb` seu aplicativo usa a propriedade, você deve receber um aviso que diz "Este membro é preterido. Use o CosmosDb em vez disso".

Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Novos recursos no SDK são detalhados no [que há de novo na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Alterações na versão 9

Existem várias alterações na versão 9 que podem exigir alterações de código, além de reconstruir seu aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de compilação, mas estão tecnicamente quebrando, uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Search .NET SDK. Tais alterações não estão listadas abaixo. Por favor, reconstrua seu aplicativo ao atualizar para a versão 9 para evitar quaisquer problemas de compatibilidade binária.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes modelo são agora imutáveis. Se você precisar criar instâncias personalizadas dessas classes para testes, você pode usar os novos construtores parametrizados:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações no campo

A `Field` classe mudou agora que também pode representar campos complexos.

As `bool` seguintes propriedades são agora anuladas:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isso porque essas propriedades `null` devem estar agora no caso de campos complexos. Se você tem um código que lê essas propriedades, ele tem que estar preparado para lidar com `null`. Observe que todas `Field` as outras propriedades de sempre foram e continuam `null` a ser anuladas, e algumas delas também serão no caso de campos complexos - especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem `Field` parâmetros `internal`foi feito. A partir de `Field` agora, cada um requer um nome explícito e um tipo de dados no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos simplificados de lotes e resultados

Na versão 7.0-preview e anteriormente, as várias classes que encapsulam grupos de documentos foram estruturadas em hierarquias de classes paralelas:

  -  `DocumentSearchResult`e `DocumentSearchResult<T>` herdado de`DocumentSearchResultBase`
  -  `DocumentSuggestResult`e `DocumentSuggestResult<T>` herdado de`DocumentSuggestResultBase`
  -  `IndexAction`e `IndexAction<T>` herdado de`IndexActionBase`
  -  `IndexBatch`e `IndexBatch<T>` herdado de`IndexBatchBase`
  -  `SearchResult`e `SearchResult<T>` herdado de`SearchResultBase`
  -  `SuggestResult`e `SuggestResult<T>` herdado de`SuggestResultBase`

Os tipos derivados sem um parâmetro de tipo genérico foram destinados a ser usados em cenários "dinamicamente" e assumiram o uso do `Document` tipo.

A partir da versão 8.0-preview, as classes base e as classes derivadas não genéricas foram removidas. Para cenários dinamicamente digitados, você pode usar `IndexBatch<Document>`, `DocumentSearchResult<Document>`e assim por diante.
 
### <a name="removed-extensibleenum"></a>Removed ExtensibleEnum

A classe de base `ExtensibleEnum` foi removida. Todas as classes derivadas dela são agora estruturas, `DataType`como `DataSourceType` `AnalyzerName`, e por exemplo. Seus `Create` métodos também foram removidos. Você pode simplesmente `Create` remover chamadas para uma vez que esses tipos são implicitamente conversíveis de strings. Se isso resultar em erros de compilador, você pode invocar explicitamente o operador de conversão através de fundição para tipos de sambiguate. Por exemplo, você pode alterar código sistino:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

para isto:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

As propriedades que possuíam valores opcionais desses tipos são agora explicitamente digitadas como anuladas, de modo que continuam a ser opcionais.

### <a name="removed-facetresults-and-hithighlights"></a>Faceta removidaResultados e hitHighlights

As `FacetResults` `HitHighlights` aulas foram removidas. Os resultados da faceta são agora digitados como `IDictionary<string, IList<FacetResult>>` e os destaques de hit como `IDictionary<string, IList<string>>`. Uma maneira rápida de resolver erros de `using` compilação introduzidos por essa alteração é adicionar aliases na parte superior de cada arquivo que usa os tipos removidos. Por exemplo: 

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alterar para SynonymMap 

O construtor `SynonymMap` não tem mais um parâmetro `enum` para `SynonymMapFormat`. Essa enumeração tinha apenas um valor e, portanto, era redundante. Se você observar erros de build como um resultado disso, basta remover as referências para o parâmetro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Mudanças de classe de modelos diversos

A `AutocompleteMode` propriedade `AutocompleteParameters` de não é mais anulada. Se você tiver um código `null`que atribui essa propriedade, você pode simplesmente removê-la e a propriedade será automaticamente inicializada para o valor padrão.

A ordem dos parâmetros para o `IndexAction` construtor mudou agora que este construtor é auto-gerado. Em vez de usar o construtor, recomendamos usar os métodos `IndexAction.Upload`de fábrica `IndexAction.Merge`, e assim por diante.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 8.0-preview para a versão 9, saiba que a criptografia com chaves gerenciadas pelo cliente foi removida, uma vez que esse recurso ainda está em pré-visualização. Especificamente, `EncryptionKey` as `Index` propriedades `SynonymMap` de e foram removidas.

Se o seu aplicativo tiver uma dependência difícil desse recurso, você não poderá atualizar para a versão 9 do Azure Search .NET SDK. Você pode continuar a usar a versão 8.0-visualização. No entanto, lembre-se que **não é recomendável usar SDKs de visualização em aplicativos de produção**. Os recursos de visualização destinam-se apenas a fins de avaliação e podem ser alterados.

> [!NOTE]
> Se você criou índices criptografados ou mapas de sinônimousando a versão 8.0-preview do SDK, você ainda poderá usá-los e modificar suas definições usando a versão 9 do SDK sem afetar negativamente seu status de criptografia. A versão 9 do SDK `encryptionKey` não enviará a propriedade para a API REST e, como resultado, a API REST não alterará o status de criptografia do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Mudança comportamental na recuperação de dados

Se você estiver `Search`usando as "digitações `Suggest`dinamicamente" ou `Get` `Document`APIs que retornam instâncias do tipo, saiba `object[]` que `string[]`elas agora desserializam matrizes JSON vazias para, em vez de .

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do .NET do Azure Search, confira o [Tutorial .NET](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, sinta-se livre para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!
