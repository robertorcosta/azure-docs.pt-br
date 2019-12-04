---
title: Introdução ao repositório de conhecimento (versão prévia)
titleSuffix: Azure Cognitive Search
description: Envie documentos enriquecidos ao armazenamento do Azure onde você pode exibir, reformatar e consumir documentos enriquecidos na Pesquisa Cognitiva do Azure e em outros aplicativos. Esse recurso está em uma versão prévia.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: aa32f671756b8ba7f17c25592b6a15b66de42b2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790014"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução aos repositórios de conhecimento na Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

O repositório de conhecimento é um recurso do Azure Pesquisa Cognitiva que persiste a saída de um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) para análise posterior ou outro processamento downstream. Um *documento enriquecido* é a saída de um pipeline, criado com base no conteúdo que foi extraído, estruturado e analisado usando processos de IA. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Com o repositório de conhecimento, os documentos enriquecidos são preservados. 

Se já usou habilidades cognitivas com a Pesquisa Cognitiva do Azure, você sabe que os *conjuntos de habilidades* movem um documento por uma sequência de aprimoramentos. O resultado pode ser um índice de pesquisa ou projeções (uma novidade nesta versão prévia) em um repositório de conhecimento. As duas saídas, o índice de pesquisa e o repositório de conhecimento, compartilham o mesmo conteúdo, mas são armazenados e usados de maneiras muito diferentes.

Fisicamente, um repositório de conhecimento é um [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), podendo ser o Armazenamento de Tabelas do Azure, o Armazenamento de Blobs do Azure ou ambos. Qualquer ferramenta ou processo que pode se conectar ao Armazenamento do Azure pode consumir o conteúdo de um repositório de conhecimento.

![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Diagrama do repositório de conhecimento no diagrama")

Para usar o repositório de dados de conhecimento, adicione um elemento `knowledgeStore` a um conjunto de qualificações que defina operações por etapas em um pipeline de indexação. Durante a execução, a Pesquisa Cognitiva do Azure cria um espaço em sua conta de armazenamento do Azure e projeta os documentos enriquecidos como blobs ou em tabelas, dependendo de sua configuração.

## <a name="benefits-of-knowledge-store"></a>Vantagens do repositório de dados de conhecimento

Um repositório de dados de conhecimento fornece estrutura, contexto e conteúdo real, compilados de arquivos de dados não estruturados e semiestruturados como blobs, arquivos de imagem que passaram por análise ou mesmo dados estruturados que são reformatados. Em um [passo a](knowledge-store-howto.md)passo, você pode ver em primeira mão como um documento de JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream, como o aprendizado de máquina e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de enriquecimento de IA pode produzir, o poder real do repositório de conhecimento é a capacidade de reformatar dados. Você pode começar com um conjunto de qualificações básicas e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que podem ser combinados em novas estruturas e consumidos em outros aplicativos além da Pesquisa Cognitiva do Azure.

Se enumeradas, as vantagens do repositório de dados de conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [ferramentas de relatórios e análise](#tools-and-apps) que não sejam de pesquisa. O Power BI com Power Query é uma opção interessante, mas qualquer ferramenta ou aplicativo que consiga se conectar ao armazenamento do Azure poderá efetuar pull de um repositório de dados de conhecimento criado.

+ Refinar um pipeline de indexação por IA durante as etapas de depuração e definições de conjuntos de qualificações. Um repositório de dados de conhecimento mostra o produto de uma definição de conjuntos de qualificações em um pipeline de indexação por IA. Você pode usar esses resultados para criar um conjunto de qualificações enriquecido já que pode ver exatamente como são os enriquecimentos. Você pode usar o [Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de um repositório de dados de conhecimento.

+ Reformatar os dados. A reformatação é codificada em conjuntos de qualificações. No entanto, a questão é que um conjunto de qualificações já pode oferecer essa capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) na Pesquisa Cognitiva do Azure foi estendida para acomodar essa tarefa. A reformatação permite definir uma projeção que se alinha com o uso pretendido dos dados enquanto preserva as relações.

> [!Note]
> Novidade para aprimoramento de ia e habilidades cognitivas? A Pesquisa Cognitiva do Azure integra-se aos recursos de Visão de Computação e Idioma nos Serviços Cognitivos para extrair e enriquecer os dados de origem usando OCR (Reconhecimento Óptico de Caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, confira [Enriquecimento de IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Criando um repositório de conhecimento

Um repositório de conhecimento faz parte de um [repositório de conhecimento](cognitive-search-working-with-skillsets.md), que, por sua vez, faz parte de um [indexar](search-indexer-overview.md). 

Nesta versão prévia, você pode criar um repositório de conhecimento usando a API REST e `api-version=2019-05-06-Preview`, ou por meio do assistente **Importar dados** no portal.

### <a name="json-representation-of-a-knowledge-store"></a>Representação JSON de um repositório de conhecimento

O JSON a seguir especifica um `knowledgeStore` que faz parte de um conjunto de qualificações chamado por um indexador (não mostrado). Se você já estiver familiarizado com o enriquecimento da IA, um conjunto de qualificações determinará a criação, a organização e a substância de cada documento enriquecido. Um conjunto de habilidades precisa conter pelo menos uma habilidade, provavelmente uma habilidade de Shaper, quando você está modulando estruturas de dados.

Um `knowledgeStore` consiste em uma conexão e projeções. 

+ A conexão é para uma conta de armazenamento na mesma região que o Pesquisa Cognitiva do Azure. 

+ As projeções podem ser de tabela, objetos JSON ou arquivos. `Tables` define a expressão física dos documentos enriquecidos no armazenamento de Tabelas do Azure. `Objects` definir os objetos JSON físicos no armazenamento de BLOBs do Azure. `Files` são binários como imagens que foram extraídas do documento que serão persistidas.

+ Projeções é uma coleção de objetos de projeção, cada objeto de projeção pode conter `tables`, `objects` e `files`. Os aprimoramentos projetados em uma única projeção são relacionados mesmo quando projetados entre tipos (tabelas, objetos ou arquivos). Projeções entre objetos de projeção não são relacionadas e são independentes. A mesma forma pode ser projetada aross vários objetos de projeção.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ], 
            "files": [

            ]  
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ],
            "files": [
                
            ]  
        }        
    ]     
    } 
}
```

Este exemplo não contém nenhuma imagem, para um exemplo de como usar projeções de arquivo, consulte [trabalhando com projeções](knowledge-store-projection-overview.md).
### <a name="sources-of-data-for-a-knowledge-store"></a>Fontes de dados para um repositório de conhecimento

Se um repositório de conhecimento for uma saída de um pipeline de enriquecimento de IA, quais serão as entradas? Os dados originais que você deseja extrair, enriquecer e, eventualmente, salvar em um repositório de conhecimento podem se originar de qualquer fonte de dados do Azure com suporte dos indexadores de pesquisa: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)

* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)

* [SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores e conjuntos de habilidades que você cria extraem e enriquecem ou transformam esse conteúdo como parte de uma carga de trabalho de indexação e, em seguida, salvam os resultados em um repositório de conhecimento.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>APIs REST usadas na criação de um repositório de conhecimento

Somente duas APIs têm as extensões necessárias para criar um repositório de conhecimento (Criar Conjunto de Habilidades e Criar Indexador). Outras APIs são usadas no estado em que se encontram.

| Objeto | API REST | Descrição |
|--------|----------|-------------|
| fonte de dados | [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa do Azure, fornecendo os dados de origem usados para criar documentos enriquecidos.  |
| conjunto de qualificações | [Criar conjunto de habilidades (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades internas](cognitive-search-predefined-skills.md) e de [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) em um pipeline de enriquecimento durante a indexação. Um conjunto de habilidades tem uma definição de `knowledgeStore` como elemento filho. |
| índice | [Criar o índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de pesquisa. Os campos no índice são mapeados para os campos na fonte de dados ou para os campos fabricados durante a fase de enriquecimento (por exemplo, um campo para os nomes de organização criados pelo reconhecimento de entidade). |
| indexador | [Criar Indexador (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, um conjunto de habilidades, associações de campo de origem e estruturas de dados intermediárias para o índice de destino e o próprio índice. Executar o indexador é o gatilho para a ingestão de dados e o enriquecimento. A saída é um índice de pesquisa com base no esquema de índice, preenchido com os dados de origem, enriquecidos por conjuntos de qualificações.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Composição física de um repositório de conhecimento

 Uma *projeção*, que é um elemento de uma definição de `knowledgeStore`, articula o esquema e a estrutura de saída de modo a corresponder ao uso pretendido. Será possível definir várias projeções se você tiver aplicativos que consomem os dados em diferentes formatos e formas. 

As projeções podem ser articuladas como objetos ou tabelas:

+ Como objeto, a projeção mapeia para o armazenamento de Blobs onde a projeção é salva em um contêiner no qual estão os objetos ou representações hierárquicas no JSON para cenários como um pipeline de ciência de dados.

+ Como tabela, a projeção mapeia para o armazenamento da Tabelas. Uma representação tabular preserva os relacionamentos para cenários como análise de dados ou exportação como quadros de dados para aprendizado de máquina. As projeções enriquecidas podem ser facilmente importadas para outros repositórios de dados. 

Você pode criar várias projeções em um repositório de dados de conhecimento para acomodar os vários clientes da organização. Talvez um desenvolvedor precise de acesso à representação JSON completa de um documento enriquecido e cientistas de dados ou analistas talvez queiram estruturas de dados granulares ou modulares formatadas pelo conjunto de qualificações.

Por exemplo, se um dos objetivos do processo de enriquecimento também for criar um conjunto de dados para treinar um modelo, projetar os dados no armazenamento de objetos seria uma maneira de usar os dados em seus pipelines de ciência de dados. Como alternativa, se você quiser criar um painel rápido do Power BI com base nos documentos enriquecidos, a projeção tabular funcionará bem.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Conectando-se com ferramentas e aplicativos

Após os enriquecimentos estarem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de Blobs ou de Tabelas do Azure poderá ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) para exibir o conteúdo e a estrutura do documento enriquecido. Considere isso como sua ferramenta de linha de base para visualizar o conteúdo do repositório de dados de conhecimento.

+ O [Power BI](knowledge-store-connect-power-bi.md) para as ferramentas de relatório e análise se você tiver dados numéricos.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) para manipular mais.

## <a name="next-steps"></a>Próximos passos

O repositório de conhecimento oferece persistência de documentos enriquecidos, que é útil ao criar um conjunto de habilidades ou ao criar novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de Armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é por meio do assistente **Importar dados**, mas você também pode usar o Postman e a API REST, que é mais útil quando você quer obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar um repositório de conhecimento usando o portal](knowledge-store-create-portal.md)
> [Criar um repositório de conhecimento usando o Postman e a API REST](knowledge-store-create-rest.md)
