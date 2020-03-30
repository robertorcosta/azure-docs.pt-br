---
title: Criar um conjunto de habilidades
titleSuffix: Azure Cognitive Search
description: Defina a extração de dados, o processamento de linguagem natural ou as etapas de análise de imagem para enriquecer e extrair informações estruturadas de seus dados para uso na Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754555"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Como criar um skillset em um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure 

O enriquecimento de IA extrai e enriquece os dados para torná-los pesquisáveis na Pesquisa Cognitiva do Azure. Nós chamamos as etapas de extração e enriquecimento de *habilidades cognitivas*, combinadas em um *conjunto de habilidades* referenciado durante a indexação. Um skillset pode usar [habilidades incorporadas](cognitive-search-predefined-skills.md) ou habilidades personalizadas (ver [Exemplo: Criando uma habilidade personalizada em um pipeline de enriquecimento de IA](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, você aprenderá a criar um pipeline de enriquecimento para as habilidades que quiser usar. Um skillset é anexado a um [indexador](search-indexer-overview.md)de pesquisa cognitiva do Azure . Uma parte do design do pipeline, abordada neste artigo, é a criação do próprio conjunto de habilidades. 

> [!NOTE]
> Outra parte do design do pipeline, abordada na [próxima etapa](#next-step), é a especificação de um indexador. Uma definição do indexador inclui uma referência ao conjunto de habilidades, além dos mapeamentos de campo usados para conectar as entradas às saídas no índice de destino.

Pontos principais a serem lembrados:

+ Você pode ter apenas um conjunto de habilidades por indexador.
+ Um conjunto de qualificações precisa ter pelo menos uma habilidade.
+ É possível criar várias habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Comece pensando no fim

Uma etapa inicial recomendada é decidir quais dados devem ser extraídos de seus dados brutos e como você deseja usar esses dados em uma solução de pesquisa. Criar uma ilustração de todo o pipeline de enriquecimento pode ajudar a identificar as etapas necessárias.

Suponha que você esteja interessado em processar um conjunto de comentários de um analista financeiro. Para cada arquivo, você deseja extrair os nomes das empresas e o sentimento geral dos comentários. Você também pode querer escrever um enriquecedor personalizado que usa serviço de Pesquisa de Entidade do Bing para encontrar informações adicionais sobre as empresas, como o tipo de negócios em que estão envolvidas. Essencialmente, você deseja extrair informações como as seguintes, indexadas para cada documento:

| texto de registro | empresas | sentimento | descrições das empresas |
|--------|-----|-----|-----|
|registro de amostra| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation é uma empresa de tecnologia multinacional americana...", "O LinkedIn é uma rede social concentrada em negócios e empregos..."]

O diagrama a seguir ilustra um pipeline de enriquecimento hipotético:

![Um hipotético gasoduto de enriquecimento](media/cognitive-search-defining-skillset/sample-skillset.png "Um hipotético gasoduto de enriquecimento")


Depois de ter uma boa ideia do que deseja conquistar com o pipeline, você poderá expressar o conjunto de habilidades que fornece estas etapas. Funcionalmente, o skillset é expresso quando você envia sua definição de indexador para a Pesquisa Cognitiva do Azure. Para saber mais sobre como carregar o indexador, consulte a [documentação do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


No diagrama, a etapa *quebra de documento* acontece automaticamente. Essencialmente, o Azure Cognitive Search sabe como abrir arquivos conhecidos e cria um campo de *conteúdo* contendo o texto extraído de cada documento. As caixas brancas são enriquecedores internos e a caixa pontilhada "Pesquisa de Entidade do Bing" representa um enriquecedor personalizado que você está criando. Conforme ilustrado, o conjunto de habilidades contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de conjunto de habilidades em RET

Um conjunto de habilidades é definido como uma matriz de habilidades. Cada uma delas define a origem de suas entradas e o nome das saídas produzidas. Usando a [API REST de Criação de Conjunto de Habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset), é possível definir um conjunto de habilidades correspondente ao diagrama anterior: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Criar um conjunto de habilidades

Quando cria um conjunto de habilidades, você pode fornecer uma descrição que ele seja autodocumentado. A descrição é opcional, mas é útil para você estar ciente do que o conjunto de habilidades faz. Como o conjunto de habilidades é um documento JSON, que não permite comentários, use um elemento `description` para fazer isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima parte do conjunto de habilidades é a matriz de habilidades. Você pode pensar em cada habilidade como um elemento primitivo de enriquecimento. Cada uma delas executa uma pequena tarefa no pipeline de enriquecimento. Cada uma usa uma entrada (ou um conjunto de entradas) e retorna algumas saídas. As próximas seções se concentram em como especificar habilidades incorporadas e personalizadas, acorrentando habilidades em conjunto através de referências de entrada e saída. As entradas podem vir dos dados de origem ou de outra habilidade. As saídas podem ser mapeadas para um campo em um índice de pesquisa ou usadas como entrada de uma habilidade downstream.

## <a name="add-built-in-skills"></a>Adicionar habilidades incorporadas

Vamos olhar para a primeira habilidade, que é a habilidade de reconhecimento de [entidade](cognitive-search-skill-entity-recognition.md)incorporada:

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Toda habilidade incorporada `odata.type` `input`tem, `output` e propriedades. Propriedades específicas da habilidade fornecem informações adicionais aplicáveis a ela. Para o reconhecimento de entidade, `categories` é uma entidade em um conjunto fixo de tipos de entidade que o modelo pré-treinado pode reconhecer.

* Cada habilidade deve ter um ```"context"```. O contexto representa o nível no qual as operações ocorrem. Na habilidade acima, o contexto é todo o documento, o que significa que a habilidade de reconhecimento da entidade é chamada uma vez por documento. As saídas também são produzidas nesse nível. Mais especificamente, ```"organizations"``` são gerados como um membro de ```"/document"```. Nas habilidades downstream, você pode se referir a essas informações recém-criadas como ```"/document/organizations"```.  Se o campo ```"context"``` não for definido explicitamente, o contexto padrão será o documento.

* A habilidade tem uma entrada denominada "text", com a entrada de origem definida como ```"/document/content"```. A habilidade (reconhecimento da entidade) opera no campo de *conteúdo* de cada documento, que é um campo padrão criado pelo indexador de blob do Azure. 

* A habilidade tem uma saída chamada ```"organizations"```. As saídas existem somente durante o processamento. Para encadear essa saída à entrada de uma habilidade downstream, faça referência à saída como ```"/document/organizations"```.

* Para um documento específico, o valor de ```"/document/organizations"``` é uma matriz de organizações extraída do texto. Por exemplo: 

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Em algumas situações, é necessário referenciar cada elemento de uma matriz separadamente. Por exemplo, suponha que você queira passar cada elemento de ```"/document/organizations"``` separadamente para outra habilidade (como o enriquecedor de Pesquisa de Entidade do Bing personalizado). Você pode referenciar cada elemento da matriz adicionando um asterisco ao caminho: ```"/document/organizations/*"``` 

A segunda habilidade de extração de sentimento segue o mesmo padrão que o primeiro enriquecedor. Ela usa ```"/document/content"``` como entrada e retorna uma pontuação de sentimento para cada instância de conteúdo. Como você não definiu o ```"context"``` campo explicitamente, a saída (mySentiment) agora é um filho de ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Adicionar uma habilidade personalizada

Lembre-se da estrutura do enriquecedor de Pesquisa de Entidade do Bing personalizado:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Essa definição é uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md) que chama uma API web como parte do processo de enriquecimento. Para cada organização identificada pelo reconhecimento da entidade, essa habilidade chama uma API web para encontrar a descrição dessa organização. A orquestração de quando chamar a API Web e como transmitir as informações recebidas é feita internamente pelo mecanismo de enriquecimento. No entanto, a inicialização necessária para chamar essa API personalizada deve ser fornecida no JSON (como URI, httpHeaders e as entradas esperadas). Para obter diretrizes sobre a criação de uma API Web personalizada para o pipeline de enriquecimento, confira [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md).

Observe que o campo "context" está definido como ```"/document/organizations/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada *para cada* organização em ```"/document/organizations"```. 

A saída, nesse caso a descrição de uma empresa, é gerada para cada organização identificada. Ao fazer referência à descrição em uma etapa downstream (por exemplo, na extração de frases-chave), você usaria o caminho ```"/document/organizations/*/description"``` para fazer isso. 

## <a name="add-structure"></a>Adicionar estrutura

O conjunto de habilidades gera informações estruturadas usando dados não estruturados. Considere o exemplo a seguir:

*"No quarto trimestre, a Microsoft registrou US$ 1,1 bilhão em receita do LinkedIn, a empresa de redes sociais que comprou no ano passado. A aquisição permite que a Microsoft combine os recursos do LinkedIn com seus recursos de CRM e Office. Os acionistas estão animados com o progresso até agora."*

Um resultado provável seria uma estrutura gerada semelhante à ilustração a seguir:

![Estrutura de saída da amostra](media/cognitive-search-defining-skillset/enriched-doc.png "Estrutura de saída da amostra")

Até agora, essa estrutura foi apenas interna, somente de memória, e usada apenas em índices de pesquisa cognitiva do Azure. A adição de uma loja de conhecimento lhe dá uma maneira de economizar enriquecimentos moldados para uso fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicionar uma loja de conhecimento

[O knowledge store](knowledge-store-concept-intro.md) é um recurso de pré-visualização na Pesquisa Cognitiva do Azure para salvar seu documento enriquecido. Um armazenamento de conhecimento que você cria, apoiado por uma conta de armazenamento do Azure, é o repositório onde seus dados enriquecidos pousam. 

Uma definição de armazenamento de conhecimento é adicionada a um skillset. Para um passo a passo de todo o processo, consulte [Criar uma loja de conhecimento em REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Você pode optar por salvar os documentos enriquecidos como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento blob. A saída de qualquer uma das habilidades no skillset pode ser originada como a entrada para a projeção. Se você está procurando projetar os dados em uma forma específica, a habilidade atualizada do [shaper](cognitive-search-skill-shaper.md) pode agora modelar tipos complexos para você usar. 

<a name="next-step"></a>

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o pipeline de enriquecimento e com os conjuntos de habilidades, continue com [Como referenciar anotações em um conjunto de habilidades](cognitive-search-concept-annotations-syntax.md) ou [Como mapear saídas para campos em um índice](cognitive-search-output-field-mapping.md). 
