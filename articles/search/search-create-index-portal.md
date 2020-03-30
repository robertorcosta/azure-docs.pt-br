---
title: Crie um índice de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Aprenda a criar um índice para a Pesquisa Cognitiva do Azure usando um designer de índicede portal incorporado.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112853"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Criar um índice da Pesquisa Cognitiva do Azure no portal

O Azure Cognitive Search inclui um designer de índice embutido no portal útil para protótipos ou a criação de um [índice de pesquisa](search-what-is-an-index.md) hospedado no seu serviço de Pesquisa Cognitiva Do Azure. A ferramenta é usada para a construção de esquemas. Quando você salva a definição, um índice vazio se torna totalmente expresso na Pesquisa Cognitiva do Azure. Como você carregá-lo com conteúdo pesquisável depende de você.

O designer de índice é apenas uma abordagem para criar um índice. Alternativamente, você pode criar e carregar um índice usando o [assistente de dados Importação](search-get-started-portal.md). O assistente só trabalha com índices que ele mesmo cria. Programaticamente, é possível criar um índice usando APIs do [.NET](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md).

## <a name="start-index-designer"></a>Iniciar o designer de índice

1. Entre no [portal do Azure](https://portal.azure.com) e abra o painel de serviço. Você pode clicar em **Todos os serviços** na barra de navegação para procurar os “serviços de pesquisa” existentes na assinatura atual. 

2. Clique no link **Adicionar índice** na barra de comandos na parte superior da página.

   ![Adicionar link de índice na barra de comando](media/search-create-index-portal/add-index.png "Adicionar link de índice na barra de comando")

3. Nomeie seu índice de Pesquisa Cognitiva Do Azure. Os nomes de índices são referenciados nas operações de indexação e de consulta. O nome do índice torna-se parte da URL de ponto final usada em conexões ao índice e para o envio de solicitações HTTP na API Azure Cognitive Search REST.

   * Comece com uma letra.
   * Use apenas letras minúsculas, dígitos ou traços (“-”).
   * Limite o nome a 60 caracteres.

## <a name="add-fields"></a>Adicionar campos

A composição de índice inclui uma *coleção Campos* que define os dados pesquisáveis no índice. Ao todo, a coleção de campos especifica a estrutura de documentos que você pode carregar separadamente. Uma coleção Campos inclui campos obrigatórios e opcionais, nomeados e tipados, com os atributos de índice que determinam como o campo pode ser usado.

1. Adicione campos para especificar totalmente os documentos que serão carregados, definindo um [tipo de dado](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um. Por exemplo, se os documentos consistirem em uma *id do hotel*, um *nome do hotel*, um *endereço*, uma *cidade* e uma *região*, crie um campo correspondente para cada um no índice. Examine as [orientações de design na seção abaixo](#design) para obter ajuda com as configurações de atributos.

1. Se os dados de entrada forem de natureza hierárquica, seu esquema deve incluir [tipos complexos](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de amostra incorporado, Hotels, ilustra tipos complexos usando um Endereço (contém vários subcampos) que tem uma relação um-a-um com cada hotel, e uma coleção complexa de quartos, onde vários quartos estão associados a cada hotel. 

1. Especifique um campo *chave* do tipo Edm.String. Um campo-chave é obrigatório para cada índice de Pesquisa Cognitiva do Azure e deve ser uma seqüência. Os valores para esse campo devem identificar exclusivamente cada documento. Por padrão, o campo é chamado *ID*, mas você pode renomeá-lo, desde que a cadeia de caracteres atenda às [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se sua coleção de campos inclui *id do hotel*, você escolheria isso para a sua chave. 

1. Defina atributos em cada campo. O designer de índices exclui todos os atributos que são inválidos para o tipo de dados, mas não sugere o que incluir. Examine a orientação na próxima seção para entender para que servem os atributos.

    A documentação da API de Pesquisa Cognitiva do Azure inclui exemplos de código com um índice de *hotéis* simples. Na captura de tela abaixo, você pode ver a definição de índice, incluindo o analisador de idioma francês especificado durante a definição de índice, que pode ser recriado como um exercício no portal.

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
|**Filtráveis**|Referenciado nas consultas **$filter**. Campos filtráveis dos tipos `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como “dia ensolarado”, `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. |  
|**Classificável**|Por padrão, o sistema classifica os resultados pela pontuação, mas você pode configurar a classificação com base nos campos dos documentos. Campos do tipo `Collection(Edm.String)` não podem ser **sortable**. |  
|**facetable**|Normalmente, usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de ocorrências por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Campos do tipo `Edm.String` que são **filterable**, **sortable** ou **facetable** podem ter um tamanho de, no máximo, 32 quilobytes. Para obter detalhes, consulte [Criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**Chave**|Identificador exclusivo para documentos no índice. Exatamente um campo deve ser escolhido como o campo chave e deve ser do tipo `Edm.String`.|  
|**Recuperáveis**|Determina se o campo pode ser retornado em um resultado da pesquisa. Isso é útil quando você deseja usar um campo (por exemplo, *margem de lucro*) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

## <a name="next-steps"></a>Próximas etapas

Depois de criar um índice de pesquisa cognitiva do Azure, você pode passar para o próximo passo: [carregar dados pesquisáveis para o índice](search-what-is-data-import.md).

Como alternativa, você também poderá [examinar os índices mais detalhadamente](search-what-is-an-index.md). Além da coleção Campos, um índice também especifica analisadores, sugestores, perfis de pontuação e configurações do CORS. O portal fornece páginas com guias para definir os elementos mais comuns: Campos, analisadores e sugestores. Para criar ou modificar outros elementos, use a API REST ou o SDK do .NET.

## <a name="see-also"></a>Confira também

 [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço Search](https://docs.microsoft.com/rest/api/searchservice/) [SDK do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

