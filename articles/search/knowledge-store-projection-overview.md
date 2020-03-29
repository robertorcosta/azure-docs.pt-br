---
title: Projeções em uma loja de conhecimento (visualização)
titleSuffix: Azure Cognitive Search
description: Salve e modele seus dados enriquecidos do pipeline de indexação de enriquecimento de IA em uma loja de conhecimento para uso em cenários diferentes da pesquisa completa de texto. O repositório de conhecimento está atualmente em versão prévia pública.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942983"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projeções em uma loja de conhecimento no Azure Cognitive Search

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

O Azure Cognitive Search permite o enriquecimento de conteúdo através de habilidades cognitivas incorporadas e habilidades personalizadas como parte da indexação. Os enriquecimentos criam novas informações onde nenhuma existia anteriormente: extrair informações de imagens, detectar sentimentos, frases-chave e entidades do texto, para citar alguns. Os enriquecimentos também adicionam estrutura ao texto indiferenciado. Todos esses processos resultam em documentos que tornam a busca completa por texto mais eficaz. Em muitos casos, documentos enriquecidos são úteis para outros cenários além da pesquisa, como a mineração de conhecimento.

Projeções, um componente do armazenamento de [conhecimento,](knowledge-store-concept-intro.md)são visões de documentos enriquecidos que podem ser salvos no armazenamento físico para fins de mineração de conhecimento. Uma projeção permite que você "projete" seus dados em uma forma que se alinhe às suas necessidades, preservando relacionamentos para que ferramentas como o Power BI possam ler os dados sem nenhum esforço adicional.

As projeções podem ser tabulares, com dados armazenados em linhas e colunas no armazenamento atabela Do Zure, ou objetos JSON armazenados no armazenamento Azure Blob. Você pode definir várias projeções de seus dados à medida que ele está sendo enriquecido. Várias projeções são úteis quando você deseja os mesmos dados moldados de forma diferente para casos de uso individual.

A loja de conhecimento suporta três tipos de projeções:

+ **Tabelas**: Para dados mais bem representados como linhas e colunas, as projeções de tabela permitem definir uma forma ou projeção esquematizada no armazenamento de tabelas. Apenas objetos JSON válidos podem ser projetados como tabelas, o documento enriquecido pode conter nós que não são chamados objetos JSON e ao projetar esses objetos, criar um objeto JSON válido com uma habilidade de shaper ou modelagem inline.

+ **Objetos**: Quando você precisa de uma representação JSON de seus dados e enriquecimentos, as projeções de objetos são salvas como bolhas. Somente objetos JSON válidos podem ser projetados como objetos, o documento enriquecido pode conter nós que não são chamados de objetos JSON e, ao projetar esses objetos, criar um objeto JSON válido com uma habilidade de shaper ou modelagem inline.

+ **Arquivos**: Quando você precisa salvar as imagens extraídas dos documentos, as projeções de arquivos permitem que você salve as imagens normalizadas para o armazenamento blob.

Para ver projeções definidas no contexto, passo através [criar uma loja de conhecimento em REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, você precisará projetar seus dados enriquecidos em diferentes formas para atender a diferentes objetivos. O armazenamento de conhecimento permite definir vários grupos de projeções. Os grupos de projeção têm as seguintes características-chave de exclusividade mútua e parentesco.

### <a name="mutual-exclusivity"></a>Exclusividade mútua

Todo o conteúdo projetado em um único grupo é independente de dados projetados em outros grupos de projeção.
Essa independência implica que você pode ter os mesmos dados moldados de forma diferente, mas repetidos em cada grupo de projeção.

### <a name="relatedness"></a>Relacionamento

Os grupos de projeção agora permitem que você projete seus documentos através de tipos de projeção, preservando as relações entre os tipos de projeção. Todo o conteúdo projetado em um único grupo de projeção preserva as relações dentro dos dados entre os tipos de projeção. Dentro das tabelas, as relações são baseadas em uma chave gerada e cada nó filho mantém uma referência ao nó pai. Entre os tipos (tabelas, objetos e arquivos), as relações são preservadas quando um único nó é projetado entre diferentes tipos. Por exemplo, considere um cenário onde você tenha um documento contendo imagens e texto. Você pode projetar o texto em tabelas ou objetos e as imagens em arquivos onde as tabelas ou objetos têm uma coluna/propriedade contendo a URL do arquivo.

## <a name="input-shaping"></a>Modelagem de entrada

Obter seus dados na forma ou estrutura correta é fundamental para um uso efetivo, sejam tabelas ou objetos. A capacidade de moldar ou estruturar seus dados com base em como você planeja acessá-los e usá-los é uma capacidade chave exposta como a habilidade **shaper** dentro do conjunto de habilidades.  

Projeções são mais fáceis de definir quando você tem um objeto na árvore de enriquecimento que corresponde ao esquema da projeção. A [habilidade atualizada do Shaper](cognitive-search-skill-shaper.md) permite que você componha um objeto de diferentes nódulos da árvore de enriquecimento e os tenha como pai um novo nó. A habilidade **shaper** permite definir tipos complexos com objetos aninhados.

Quando você tem uma nova forma definida que contém todos os elementos que você precisa projetar, agora você pode usar essa forma como fonte para suas projeções ou como uma entrada para outra habilidade.

## <a name="projection-slicing"></a>Corte de projeção

Ao definir um grupo de projeção, um único nó na árvore de enriquecimento pode ser cortado em várias tabelas ou objetos relacionados. Adicionar uma projeção com um caminho de origem que seja filho de uma projeção existente resultará na corte do nó da criança do nó pai e projetada na nova tabela ou objeto ainda relacionado. Esta técnica permite que você defina um único nó em uma habilidade de shaper que pode ser a fonte para todas as suas projeções.

## <a name="table-projections"></a>Projeções de tabela

Por facilitar a importação, recomendamos projeções de tabela para exploração de dados com Power BI. Além disso, as projeções de tabela permitem mudar a cardinalidade entre as relações de mesa. 

Você pode projetar um único documento em seu índice em várias tabelas, preservando as relações. Ao projetar para várias tabelas, a forma completa será projetada em cada tabela, a menos que um nó de criança seja a fonte de outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definindo uma projeção de tabela

Ao definir uma projeção de tabela dentro do `knowledgeStore` elemento de suas habilidades, comece mapeando um nó na árvore de enriquecimento para a fonte da tabela. Normalmente, este nó é a saída de uma habilidade **shaper** que você adicionou à lista de habilidades para produzir uma forma específica que você precisa projetar em tabelas. O nó que você escolher para projetar pode ser fatiado para projetar em várias tabelas. A definição de tabelas é uma lista de tabelas que você deseja projetar.

Cada tabela requer três propriedades:

+ nome da tabela: O nome da tabela no Armazenamento Azure.

+ gerouKeyName: O nome da coluna para a chave que identifica com exclusividade essa linha.

+ fonte: O nó da árvore de enriquecimento da onde você está fornecendo seus enriquecimentos. Este nó é geralmente a saída de um modelador, mas pode ser a saída de qualquer uma das habilidades.

Aqui está um exemplo de projeções de tabela.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Como demonstrado neste exemplo, as frases-chave e as entidades são modeladas em diferentes tabelas e conterão uma referência de volta ao pai (MainTable) para cada linha.

## <a name="object-projections"></a>Projeções de objetos

Projeções de objetos são representações JSON da árvore de enriquecimento que podem ser originadas de qualquer nó. Em muitos casos, a mesma habilidade **shaper** que cria uma projeção de tabela pode ser usada para gerar uma projeção de objeto. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Gerar uma projeção de objeto requer alguns atributos específicos do objeto:

+ storageContainer: O recipiente blob onde os objetos serão salvos
+ fonte: O caminho para o nó da árvore de enriquecimento que é a raiz da projeção

## <a name="file-projection"></a>Projeção de arquivos

As projeções de arquivos são semelhantes às projeções de objetos e só atuam na `normalized_images` coleção. Semelhante às projeções de objeto, as projeções de arquivo são salvas no recipiente blob com prefixo de pasta do valor codificado base64 do ID do documento. As projeções de arquivos não podem compartilhar o mesmo contêiner que as projeções de objetos e precisam ser projetadas em um recipiente diferente.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Ciclo de vida da projeção

Suas projeções têm um ciclo de vida que está ligado aos dados de origem em sua fonte de dados. À medida que seus dados são atualizados e reindexados, suas projeções são atualizadas com os resultados dos enriquecimentos garantindo que suas projeções sejam eventualmente consistentes com os dados em sua fonte de dados. As projeções herdam a política de exclusão configurada para o seu índice. As projeções não são excluídas quando o indexador ou o próprio serviço de pesquisa são excluídos.

## <a name="using-projections"></a>Usando projeções

Depois que o indexador é executado, você pode ler os dados projetados nos contêineres ou tabelas especificadas através de projeções.

Para análises, a exploração no Power BI é tão simples quanto definir o armazenamento da Tabela Azure como a fonte de dados. Você pode facilmente criar um conjunto de visualizações em seus dados usando os relacionamentos dentro.

Alternativamente, se você precisar usar os dados enriquecidos em um pipeline de ciência de dados, você pode [carregar os dados de blobs em um DataFrame pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Finalmente, se você precisar exportar seus dados do armazenamento de conhecimento, a Azure Data Factory possui conectores para exportar os dados e aterrissá-los no banco de dados de sua escolha. 

## <a name="next-steps"></a>Próximas etapas

Como próximo passo, crie seu primeiro armazenamento de conhecimento usando dados de amostra e instruções.

> [!div class="nextstepaction"]
> [Crie uma loja de conhecimento em REST](knowledge-store-create-rest.md).

Para um tutorial que abrange conceitos avançados de projeções como fatiamento, modelagem inline e relacionamentos, comece com [projeções definidas em uma loja de conhecimento](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definir projeções em uma loja de conhecimento](knowledge-store-projections-examples.md)
