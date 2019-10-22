---
title: Criar um índice de Azure Search no portal do Azure-Azure Search
description: Saiba como criar um índice para Azure Search usando um designer de índice do portal interno.
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: heidist
ms.openlocfilehash: 4abef5a3030643d4c7b91d2911f350190972f1eb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71937275"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Criar um índice de Azure Search no portal

O Azure Search inclui um designer de índice interno no portal útil para protótipos ou a criação de um [índice de pesquisa](search-what-is-an-index.md) hospedado em seu serviço de Azure Search. A ferramenta é usada para construção de esquema. Quando você salva a definição, um índice vazio se torna totalmente expresso em Azure Search. A forma como você o carrega com conteúdo pesquisável cabe a você.

O designer de índice é apenas uma abordagem para a criação de um índice. Como alternativa, você pode criar e carregar um índice usando o [Assistente de importação de dados](search-get-started-portal.md). O assistente só funciona com índices que ele cria. Programaticamente, você pode criar um índice usando as APIs do [.net](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md) .

## <a name="start-index-designer"></a>Iniciar o designer de índice

1. Entre no [portal do Azure](https://portal.azure.com) e abra o painel de serviço. Você pode clicar em **todos os serviços** na barra de atalhos para pesquisar os "serviços de pesquisa" existentes na assinatura atual. 

2. Clique no link **Adicionar índice** na barra de comandos na parte superior da página.

   ![Adicionar link de índice na barra de comandos](media/search-create-index-portal/add-index.png "Adicionar link de índice na barra de comandos")

3. Dê um nome ao seu índice de Azure Search. Os nomes de índice são referenciados em operações de indexação e consulta. O nome do índice se torna parte da URL do ponto de extremidade usada em conexões com o índice e para enviar solicitações HTTP na API REST do Azure Search.

   * Comece com uma letra.
   * Use apenas letras minúsculas, dígitos ou traços ("-").
   * Limite o nome a 60 caracteres.

## <a name="add-fields"></a>Adicionar campos

A composição de índice inclui uma *coleção de campos* que define os dados pesquisáveis no índice. Totalmente, a coleção Fields especifica a estrutura de documentos que você carrega separadamente. Uma coleção Fields inclui campos obrigatórios e opcionais, nomeados e tipados, com atributos de índice que determinam como o campo pode ser usado.

1. Adicione campos para especificar completamente os documentos que serão carregados, definindo um [tipo de dados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um. Por exemplo, se os documentos consistem em um *Hotel-ID*, *Hotel-Name*, *Address*, *City*e *Region*, crie um campo correspondente para cada um no índice. Examine as [diretrizes de design na seção abaixo](#design) para obter ajuda com a configuração de atributos.

1. Se os dados de entrada forem hierárquicos por natureza, o esquema deverá incluir [tipos complexos](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de exemplo interno, hotéis, ilustra os tipos complexos usando um endereço (contém vários subcampos) que têm uma relação um-para-um com cada hotel e uma coleção complexa de salas, em que várias salas são associadas a cada hotel. 

1. Especifique um campo de *chave* do tipo EDM. String. Um campo de chave é obrigatório para cada índice de Azure Search e deve ser uma cadeia de caracteres. Os valores para esse campo devem identificar exclusivamente cada documento. Por padrão, o campo é chamado *ID* , mas você pode renomeá-lo, desde que a cadeia de caracteres satisfaça [as regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se a coleção Fields incluir o *Hotel-ID*, você escolheria isso para sua chave. 

1. Defina atributos em cada campo. O designer de índice exclui todos os atributos que são inválidos para o tipo de dados, mas não sugere o que incluir. Revise as diretrizes na próxima seção para entender a finalidade dos atributos.

    A documentação da API Azure Search inclui exemplos de código que apresentam um índice de *Hotéis* simples. Na captura de tela abaixo, você pode ver a definição do índice, incluindo o analisador de idioma francês especificado durante a definição do índice, que você pode recriar como um exercício de prática no Portal.

    ![Índice de demonstração de hotéis](media/search-create-index-portal/field-definitions.png "Índice de demonstração de hotéis")

1. Quando terminar, clique em **criar** para salvar e criar o índice.

<a name="design"></a>

## <a name="set-attributes"></a>Definir atributos

Embora você possa adicionar novos campos a qualquer momento, as definições de campo existentes são bloqueadas para o tempo de vida do índice. Por esse motivo, os desenvolvedores normalmente usam o portal para criar índices simples, testar ideias ou usar as páginas do portal para pesquisar uma configuração. A iteração frequente sobre um design de índice é mais eficiente se você seguir uma abordagem baseada em código para poder recriar o índice facilmente.

Analisadores e sugestores são associados a campos antes de o índice ser salvo. Certifique-se de adicionar analisadores de idioma ou sugestores à definição de índice enquanto você o estiver criando.

Os campos de cadeia de caracteres geralmente são marcados como **pesquisáveis** e **recuperáveis**. Os campos usados para restringir os resultados da pesquisa incluem **classificável**, **filtrável**e **facetable**.

Os atributos de campo determinam como um campo é usado, como se ele é usado na pesquisa de texto completo, navegação facetada, operações de classificação e assim por diante. A tabela a seguir descreve cada atributo.

|Atributo|Descrição|  
|---------------|-----------------|  
|**pesquisável**|Pesquisa de texto completo, sujeita à análise léxica, como quebra de palavras durante a indexação. Se você definir um campo pesquisável com um valor como "dia ensolarado", internamente ele será dividido nos tokens individuais "ensolarado" e "dia". Para obter detalhes, consulte [como funciona a pesquisa de texto completo](search-lucene-query-architecture.md).|  
|**filtrável**|Referenciado em consultas **$Filter** . Os campos filtráveis do tipo `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras, de modo que as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como "dia ensolarado", `$filter=f eq 'sunny'` não encontrará nenhuma correspondência, mas `$filter=f eq 'sunny day'`. |  
|**classificável**|Por padrão, o sistema classifica os resultados por Pontuação, mas você pode configurar a classificação com base em campos nos documentos. Campos do tipo `Collection(Edm.String)` não podem ser **classificável**. |  
|**com faceta**|Normalmente usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de acesso por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Os campos do tipo `Edm.String` que são **filtráveis**, **classificável**ou **facetable** podem ter, no máximo, 32 quilobytes de comprimento. Para obter detalhes, consulte [criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**chave**|Identificador exclusivo para documentos dentro do índice. Exatamente um campo deve ser escolhido como o campo de chave e deve ser do tipo `Edm.String`.|  
|**recuperáveis**|Determina se o campo pode ser retornado em um resultado de pesquisa. Isso é útil quando você deseja usar um campo (como margem de *lucro*) como um mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo fique visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

## <a name="next-steps"></a>Próximos passos

Depois de criar um índice de Azure Search, você pode passar para a próxima etapa: [carregar dados pesquisáveis no índice](search-what-is-data-import.md).

Como alternativa, você também pode obter uma [análise mais profunda dos índices](search-what-is-an-index.md). Além da coleção Fields, um índice também especifica analisadores, sugestores, perfis de Pontuação e configurações de CORS. O portal fornece páginas com guias para definir os elementos mais comuns: campos, analisadores e sugestores. Para criar ou modificar outros elementos, você pode usar a API REST ou o SDK do .NET.

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md)  
 [SDK do .net](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet) da [API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)

