---
title: Mapeamentos de campo em indexadores
titleSuffix: Azure Cognitive Search
description: Configure mapeamentos de campo em um indexador para explicar diferenças em nomes de campo e representações de dados.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275145"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapeamentos de campo e transformações usando indexadores de pesquisa cognitiva do Azure

Ao usar indexadores de pesquisa cognitiva do Azure, às vezes você descobre que os dados de entrada não correspondem ao esquema do seu índice de destino. Nesses casos, você pode usar **mapeamentos de campo** para remodelar seus dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* Sua fonte de dados `_id`tem um campo chamado , mas o Azure Cognitive Search não permite nomes de campo que começam com um sublinhado. Um mapeamento de campo permite que você efetivamente renomeie um campo.
* Você deseja preencher vários campos no índice a partir dos mesmos dados de origem de dados. Por exemplo, você pode querer aplicar diferentes analisadores a esses campos.
* Você deseja preencher um campo de índice com dados de mais de uma fonte de dados, e as fontes de dados usam nomes de campo diferentes.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.

> [!NOTE]
> O recurso de mapeamento de campo dos indexadores de pesquisa cognitiva do Azure fornece uma maneira simples de mapear campos de dados para campos de indexação, com algumas opções para conversão de dados. Dados mais complexos podem exigir pré-processamento para remodelá-los em uma forma fácil de indexar.
>
> O Microsoft Azure Data Factory é uma poderosa solução baseada em nuvem para importar e transformar dados. Você também pode escrever código para transformar dados de origem antes de indexar. Para exemplos de código, consulte [Dados relacionais do modelo](search-example-adventureworks-modeling.md) e [facetas multinível do modelo](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo é composto por três partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Essa propriedade é obrigatória.
2. Um `targetFieldName`opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction`opcional, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de `fieldMappings` campo são adicionados à matriz da definição do indexador.

## <a name="map-fields-using-the-rest-api"></a>Mapear campos usando a API REST

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a solicitação de API do [Create Indexer.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Você pode gerenciar os mapeamentos de campo de um indexador existente usando a solicitação de API [do Indexador de atualização.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Por exemplo, veja como mapear um campo de origem para um campo de destino com um nome diferente:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> O Azure Cognitive Search usa comparação insensível a casos para resolver os nomes de campo e função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas corretas), mas isso significa que a fonte de dados ou o índice não pode ter campos que diferem somente maiúsculas e minúsculas.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapear campos usando o .NET SDK

Você define mapeamentos de campo no SDK .NET usando `SourceFieldName` a `TargetFieldName`classe [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) que tem as propriedades e , e uma referência opcional. `MappingFunction`

Você pode especificar mapeamentos de campo ao construir o `Indexer.FieldMappings` indexador ou mais tarde definindo diretamente a propriedade.

O exemplo C# a seguir define os mapeamentos de campo ao construir um indexador.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Uma função de mapeamento de campo transforma o conteúdo de um campo antes de ser armazenado no índice. As seguintes funções de mapeamento são suportadas atualmente:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [Urlencode](#urlEncodeFunction)
* [Urldecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - pesquisa de chave de documento

Apenas caracteres seguros para URL podem aparecer em uma chave de documento de pesquisa cognitiva do Azure (porque os clientes devem ser capazes de abordar o documento usando a [API de pesquisa).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Se o campo de origem da sua chave contiver caracteres inseguros em URL, você poderá usar a `base64Encode` função para convertê-la na hora da indexação. No entanto, uma chave de documento (antes e depois da conversão) não pode ser maior que 1.024 caracteres.

Quando você recuperar a chave codificada no momento `base64Decode` da pesquisa, você pode usar a função para obter o valor-chave original e usá-la para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Se você não incluir uma propriedade de parâmetros para sua `{"useHttpServerUtilityUrlTokenEncode" : true}`função de mapeamento, ela será padrão para o valor .

A Busca Cognitiva do Azure suporta duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais informações, consulte [as opções de codificação base64](#base64details) para decidir quais parâmetros usar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a decodificação de Base64 da cadeia de caracteres de entrada. A entrada é assumida como uma seqüência *de* string codificada pela BASE64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo - decodificar metadados ou URLs

Seus dados de origem podem conter strings codificadas pelo Base64, como seqüências de metadados blob ou URLs da Web, que você deseja tornar pesquisável como texto simples. Você pode `base64Decode` usar a função para transformar os dados codificados de volta em strings regulares ao preencher seu índice de pesquisa.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Se você não incluir uma propriedade de parâmetros, ela será padrão para o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

A Busca Cognitiva do Azure suporta duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais detalhes, consulte [as opções de codificação base64](#base64details) para decidir quais parâmetros usar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação base64

A Pesquisa Cognitiva do Azure suporta codificação base64 segura de URL e codificação base64 normal. Uma string que é base64 codificada durante a indexação deve ser decodificada posteriormente com as mesmas opções de codificação, ou então o resultado não corresponderá ao original.

Se `useHttpServerUtilityUrlTokenEncode` os `useHttpServerUtilityUrlTokenDecode` parâmetros ou parâmetros para codificação e decodificação, `true`respectivamente, forem definidos como `base64Encode` [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` se comportarem como [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Se `base64Encode` for usado para `useHttpServerUtilityUrlTokenEncode` produzir valores-chave, deve ser definido como verdadeiro. Somente a codificação base64 segura de URL pode ser usada para valores-chave. Consulte [regras de nomeação &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) para obter o conjunto completo de restrições em caracteres em valores-chave.

As bibliotecas .NET no Azure Cognitive Search assumem o Framework .NET completo, que fornece codificação incorporada. As `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` opções e as opções aproveitam essa função incorporada. Se você estiver usando o .NET Core ou `false` outra estrutura, recomendamos definir essas opções e chamar diretamente as funções de codificação e decodificação do seu framework.

A tabela a seguir compara codificações diferentes de base64 da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para suas funções de base64, aplique sua função de codificação de biblioteca na cadeia de caracteres `00>00?00` e compare a saída com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificação Base64 | Processamento adicional após a codificação da biblioteca | Processamento adicional antes da decodificação da biblioteca |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Use caracteres com URL segura e remova o preenchimento | Use caracteres base64 padrão e adicione o preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Use caracteres com URL segura | Use caracteres base64 padrão |
| Base64 com preenchimento e URL segura | `MDA-MDA_MDA=` | Remover preenchimento | Adicionar preenchimento |
| Base64 sem preenchimento e URL segura | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função extractTokenPosition

Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Esta função usa os seguintes parâmetros:

* `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição baseada em zero do número inteiro do token para escolher depois que a cadeia de caracteres de entrada for dividida.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(espaço) e o `position` for 0, o resultado será `Jane`; se o `position` for 1, o resultado será `Doe`. Se a posição se refere a um token que não existe, um erro será retornado.

#### <a name="example---extract-a-name"></a>Exemplo - extrair um nome

Sua fonte de dados contém um campo `PersonName` e você deseja indexá-la como dois campos `FirstName` e `LastName` separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>função jsonArrayToStringCollection

Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher um campo `Collection(Edm.String)` no índice.

Por exemplo, se a cadeia de caracteres de entrada for `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white` e `blue`. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro retornará.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo - preencher a coleta a partir de dados relacionais

O Banco de Dados SQL do Azure não tem `Collection(Edm.String)` um tipo de dados incorporado que naturalmente mapeia para campos na Pesquisa Cognitiva do Azure. Para preencher os campos de coleta de strings, você pode pré-processar `jsonArrayToStringCollection` seus dados de origem como uma matriz de stringSON e, em seguida, usar a função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Para um exemplo detalhado que transforma dados relacionais em campos de coleta de índices, consulte [Dados relacionais do Modelo](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>função urlEncode

Esta função pode ser usada para codificar uma seqüência de string para que ela seja "segura de URL". Quando usado com uma seqüência que contém caracteres que não são permitidos em uma URL, essa função converterá esses caracteres "inseguros" em equivalentes de entidade de caracteres. Esta função usa o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - pesquisa de chave de documento

`urlEncode`função pode ser usada como `base64Encode` uma alternativa à função, se apenas caracteres inseguros url devem ser convertidos, mantendo outros caracteres como está.

Digamos, a seqüência de entrada `<hello>` `(Edm.String)` é - então o campo de destino do tipo será preenchido com o valor`%3chello%3e`

Quando você recuperar a chave codificada no momento `urlDecode` da pesquisa, você pode usar a função para obter o valor-chave original e usá-la para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlFunçãoDecode

 Esta função converte uma seqüência codificada por URL em uma seqüência decodificada usando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo - decodificar metadados blob

 Alguns clientes de armazenamento do Azure codificam automaticamente os metadados do blob se ele contiver caracteres não ASCII. No entanto, se você quiser tornar esses metadados pesquisáveis `urlDecode` (como texto simples), você pode usar a função para transformar os dados codificados de volta em strings regulares ao preencher seu índice de pesquisa.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>função de código de comprimento fixo
 
 Esta função converte uma seqüência de qualquer comprimento em uma seqüência de comprimento fixo.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exemplo - chaves de documento de mapa que são muito longas
 
Ao enfrentar erros reclamando que a chave de documento tem mais de 1024 caracteres, essa função pode ser aplicada para reduzir o comprimento da chave do documento.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
