---
title: Criar um índice de Pesquisa Cognitiva do Azure no portal do Azure
titleSuffix: Azure Cognitive Search
description: Saiba como criar um índice para o Azure Pesquisa Cognitiva usando um designer de índice do portal interno.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9340b9c058ba780b8d74587f21c1b9fbe59576d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792453"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Criar um índice de Pesquisa Cognitiva do Azure no portal

O Azure Pesquisa Cognitiva inclui um designer de índice interno no portal útil para protótipos ou criação de um [índice de pesquisa](search-what-is-an-index.md) hospedado em seu serviço de pesquisa cognitiva do Azure. A ferramenta é usada para a construção de esquemas. Quando você salva a definição, um índice vazio se torna totalmente expresso no Azure Pesquisa Cognitiva. A forma como você o carrega com conteúdo pesquisável cabe a você.

O designer de índice é apenas uma abordagem para criar um índice. Como alternativa, você pode criar e carregar um índice usando o [Assistente de importação de dados](search-get-started-portal.md). O assistente só funciona com índices que ele cria. Programaticamente, é possível criar um índice usando APIs do [.NET](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md).

## <a name="start-index-designer"></a>Iniciar o designer de índice

1. Entre no [portal do Azure](https://portal.azure.com) e abra o painel de serviço. Você pode clicar em **Todos os serviços** na barra de navegação para procurar os “serviços de pesquisa” existentes na assinatura atual. 

2. Clique no link **Adicionar índice** na barra de comandos na parte superior da página.

   ![Adicionar link de índice na barra de comandos](media/search-create-index-portal/add-index.png "Adicionar link de índice na barra de comandos")

3. Nomeie seu índice de Pesquisa Cognitiva do Azure. Os nomes de índices são referenciados nas operações de indexação e de consulta. O nome do índice se torna parte da URL do ponto de extremidade usada em conexões com o índice e para enviar solicitações HTTP na API REST do Azure Pesquisa Cognitiva.

   * Comece com uma letra.
   * Use apenas letras minúsculas, dígitos ou traços (“-”).
   * Limite o nome a 60 caracteres.

## <a name="add-fields"></a>Adicionar campos

A composição de índice inclui uma *coleção Campos* que define os dados pesquisáveis no índice. Ao todo, a coleção de campos especifica a estrutura de documentos que você pode carregar separadamente. Uma coleção Campos inclui campos obrigatórios e opcionais, nomeados e tipados, com os atributos de índice que determinam como o campo pode ser usado.

1. Adicione campos para especificar totalmente os documentos que serão carregados, definindo um [tipo de dado](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um. Por exemplo, se os documentos consistirem em uma *id do hotel*, um *nome do hotel*, um *endereço*, uma *cidade* e uma *região*, crie um campo correspondente para cada um no índice. Examine as [orientações de design na seção abaixo](#design) para obter ajuda com as configurações de atributos.

1. Se os dados de entrada forem hierárquicos por natureza, o esquema deverá incluir [tipos complexos](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de exemplo interno, hotéis, ilustra os tipos complexos usando um endereço (contém vários subcampos) que têm uma relação um-para-um com cada hotel e uma coleção complexa de salas, em que várias salas são associadas a cada hotel. 

1. Especifique um campo *chave* do tipo Edm.String. Um campo de chave é obrigatório para cada índice de Pesquisa Cognitiva do Azure e deve ser uma cadeia de caracteres. Os valores para esse campo devem identificar exclusivamente cada documento. Por padrão, o campo é chamado *ID*, mas você pode renomeá-lo, desde que a cadeia de caracteres atenda às [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se sua coleção de campos inclui *id do hotel*, você escolheria isso para a sua chave. 

1. Defina atributos em cada campo. O designer de índices exclui todos os atributos que são inválidos para o tipo de dados, mas não sugere o que incluir. Examine a orientação na próxima seção para entender para que servem os atributos.

    A documentação da API de Pesquisa Cognitiva do Azure inclui exemplos de código que apresentam um índice de *Hotéis* simples. Na captura de tela abaixo, você pode ver a definição de índice, incluindo o analisador de idioma francês especificado durante a definição de índice, que pode ser recriado como um exercício no portal.

    ![Índice de demonstração de hotéis](media/search-create-index-portal/field-definitions.png "Índice de demonstração de hotéis")

1. Quando terminar, clique em **Criar** para salvar e criar o índice.

<a name="design"></a>

## <a name="set-attributes"></a>Definir atributos

Embora seja possível adicionar novos campos a qualquer momento, as definições de campo existentes são bloqueadas durante o tempo de vida do índice. Por esse motivo, os desenvolvedores geralmente usam o portal para criar índices simples, testar ideias ou usar as páginas do portal para pesquisar uma configuração. A iteração frequente em um design de índice será mais eficiente se você seguir uma abordagem baseada em código, de modo que você possa recriar o índice com facilidade.

Analisadores e sugestores são associados aos campos antes que o índice seja salvo. Lembre-se de adicionar os analisadores ou sugestores de idioma à definição de índice ao criá-lo.

Geralmente, os campos de cadeia de caracteres são marcados como **Pesquisáveis** e **Recuperáveis**. Os campos usados para restringir os resultados da pesquisa incluem **Classificável**, **Filtrável** e **Com Faceta**.

Os atributos de campo determinam como um campo é usado, por exemplo, se ele é usado na pesquisa de texto completo, na navegação facetada, nas operações de classificação e assim por diante. A tabela a seguir descreve cada atributo.

|Atributo|Descrição|  
|---------------|-----------------|  
|**searchable**|Pesquisável com texto completo, sujeito à análise lexical como separação de palavras durante a indexação. Se você definir um campo pesquisável com um valor como “dia ensolarado”, internamente, ele será dividido nos tokens individuais “dia” e “ensolarado”. Para obter detalhes, consulte [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md).|  
|**filterable**|Referenciado nas consultas **$filter**. Campos filtráveis dos tipos `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como “dia ensolarado”, `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. |  
|**sortable**|Por padrão, o sistema classifica os resultados pela pontuação, mas você pode configurar a classificação com base nos campos dos documentos. Campos do tipo `Collection(Edm.String)` não podem ser **sortable**. |  
|**facetable**|Normalmente, usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de ocorrências por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Campos do tipo `Edm.String` que são **filterable**, **sortable** ou **facetable** podem ter um tamanho de, no máximo, 32 quilobytes. Para obter detalhes, consulte [Criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**chave**|Identificador exclusivo para documentos no índice. Exatamente um campo deve ser escolhido como o campo chave e deve ser do tipo `Edm.String`.|  
|**retrievable**|Determina se o campo pode ser retornado em um resultado da pesquisa. Isso é útil quando você deseja usar um campo (por exemplo, *margem de lucro*) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

## <a name="next-steps"></a>Próximos passos

Depois de criar um índice de Pesquisa Cognitiva do Azure, você pode passar para a próxima etapa: [carregar dados pesquisáveis no índice](search-what-is-data-import.md).

Como alternativa, você também poderá [examinar os índices mais detalhadamente](search-what-is-an-index.md). Além da coleção Campos, um índice também especifica analisadores, sugestores, perfis de pontuação e configurações do CORS. O portal fornece páginas com guias para definir os elementos mais comuns: Campos, analisadores e sugestores. Para criar ou modificar outros elementos, use a API REST ou o SDK do .NET.

## <a name="see-also"></a>Consulte

 [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço Search](https://docs.microsoft.com/rest/api/searchservice/) [SDK do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

