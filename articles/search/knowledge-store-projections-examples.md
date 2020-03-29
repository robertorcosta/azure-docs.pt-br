---
title: Definir projeções em uma loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns sobre como projetar documentos enriquecidos na loja de conhecimento para uso com Power BI ou Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943673"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Projeções de armazenamento de conhecimento: como moldar e exportar enriquecimentos

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

Projeções são a expressão física de documentos enriquecidos em uma loja de conhecimento. O uso efetivo de seus documentos enriquecidos requer estrutura. Neste artigo, você explorará a estrutura e as relações, aprendendo como construir propriedades de projeção, bem como como relacionar dados entre os tipos de projeção que você cria. 

Para criar uma projeção, você deve moldar os dados usando uma [habilidade shaper](cognitive-search-skill-shaper.md) para criar um objeto personalizado ou usar a sintaxe de modelagem inline dentro de uma definição de projeção. 

Uma forma de dados contém todos os dados que você pretende projetar, formados como uma hierarquia de nós. Este artigo mostra várias técnicas para moldar dados para que possam ser projetados em estruturas físicas que sejam propícias a relatórios, análises ou processamento a jusante. 

Os exemplos apresentados neste artigo podem ser encontrados nesta [amostra de API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), que você pode baixar e executar em um cliente HTTP.

## <a name="introduction-to-the-examples"></a>Introdução aos exemplos

Se você está familiarizado com [projeções,](knowledge-store-projection-overview.md)você vai lembrar que existem três tipos:

+ Tabelas
+ Objetos
+ Arquivos

As projeções de tabela são armazenadas no armazenamento da Tabela Azure. As projeções de objetos e arquivos são escritas para o armazenamento blob, onde as projeções de objeto saem como arquivos JSON e podem conter conteúdo do documento de origem, bem como quaisquer saídas de habilidades ou enriquecimentos. O gasoduto de enriquecimento também pode extrair binários como imagens, esses binários são projetados como projeções de arquivos. Quando um objeto binário é projetado como uma projeção de objeto, apenas os metadados associados a ele são salvos como uma bolha JSON. 

Para entender a intersecção entre modelagem de dados e projeções, usaremos as seguintes habilidades como base para explorar várias configurações. Este conjunto de habilidades processa conteúdo bruto de imagem e texto. As projeções serão definidas a partir do conteúdo do documento e das saídas das habilidades, para os cenários que queremos apoiar.

> [!IMPORTANT] 
> Ao experimentar com projeções, é útil [definir a propriedade de cache indexador](search-howto-incremental-index.md) para garantir o controle de custos. A edição de projeções fará com que todo o documento seja enriquecido novamente se o cache do indexador não for definido. Quando o cache é definido e apenas as projeções atualizadas, execuções skillset para documentos previamente enriquecidos não resultam em novas taxas de Serviços Cognitivos.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Usando esse skillset, `knowledgeStore` com sua base nula, nosso `knowledgeStore` primeiro exemplo preenche o objeto, configurado com projeções que criam estruturas de dados tabulares que podemos usar em outros cenários. 

## <a name="projecting-to-tables"></a>Projetando para tabelas

Projetar para tabelas no Azure Storage é útil para relatórios e análises usando ferramentas como power bi. O POWER BI pode ler a partir de tabelas e descobrir relacionamentos com base nas teclas geradas durante a projeção. Se você está tentando construir um dashboard, ter dados relacionados simplificará essa tarefa. 

Vamos supor que estamos tentando construir um painel onde possamos visualizar as frases-chave extraídas dos documentos como uma nuvem de palavras. Para criar a estrutura de dados certa, podemos adicionar uma habilidade shaper ao skillset para criar uma forma personalizada que tenha os detalhes específicos do documento e frases-chave. A forma personalizada `pbiShape` será `document` chamada no nó raiz.

> [!NOTE] 
> As projeções de tabela são tabelas de armazenamento Do Zure, regidas pelos limites de armazenamento impostos pelo Azure Storage. Para obter mais informações, consulte [os limites de armazenamento da tabela](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior que 64 KB. Essas restrições fazem das mesas uma boa solução para armazenar um grande número de pequenas entidades.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade shaper para criar uma forma personalizada

Crie uma forma personalizada que você pode projetar no armazenamento de tabelas. Sem uma forma personalizada, uma projeção só pode referenciar um único nó (uma projeção por saída). A criação de uma forma personalizada permite agregar vários elementos em um novo conjunto lógico que pode ser projetado como uma única tabela, ou fatiado e distribuído em uma coleção de tabelas. 

Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto `pbiShape` é chamado e `/document`é pai em . 

> [!IMPORTANT] 
> Um dos objetivos da modelagem é garantir que todos os nós de enriquecimento sejam expressos em JSON bem formado, que é necessário para projetar-se na loja de conhecimento. Isso é especialmente verdade quando uma árvore de enriquecimento contém nós que não são bem formados JSON (por exemplo, quando um enriquecimento é pai de um primitivo como uma corda).
>
> Observe os dois últimos `KeyPhrases` nódulos, e. `Entities` Estes são embrulhados em um `sourceContext`objeto JSON válido com o . Isso é `keyphrases` necessário `entities` como e são enriquecimentos em primitivos e precisam ser convertidos em JSON válido antes que eles possam ser projetados.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Adicione a habilidade shaper acima ao skillset. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Agora que temos todos os dados necessários para projetar para tabelas, atualize o objeto knowledgeStore com as definições da tabela. Neste exemplo, temos três tabelas, `tableName`definidas por definir o , `source` e `generatedKeyName` propriedades.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Você pode processar seu trabalho seguindo estas etapas:

1. Defina ```storageConnectionString``` a propriedade como uma seqüência de conexão de armazenamento de uso geral V2 válida.  

1. Atualize o skillset emitindo a solicitação PUT.

1. Depois de atualizar o skillset, execute o indexador. 

Agora você tem uma projeção de trabalho com três mesas. A importação dessas tabelas para o Power BI deve resultar na descoberta automática do Power BI das relações.

Antes de seguir em frente com o próximo exemplo, vamos revisitar aspectos da projeção da tabela para entender a mecânica do corte e da relação de dados.

### <a name="slicing"></a>Cortar 

O corte é uma técnica que subdivide toda uma forma consolidada em partes constituintes. O resultado consiste em tabelas separadas, mas relacionadas, com as qual você pode trabalhar individualmente.

No exemplo, `pbiShape` está a forma consolidada (ou nó de enriquecimento). Na definição de `pbiShape` projeção, é fatiado em tabelas adicionais, ```keyPhrases``` ```Entities```o que permite retirar partes da forma, e . No Power BI, isso é útil, pois várias entidades e frases-chave estão associadas a cada documento, e você obterá mais insights se puder ver entidades e frases-chave como dados categorizados.

O fatiamento gera implicitamente uma relação entre ```generatedKeyName``` as tabelas pai e filho, usando a tabela na mesa dos pais para criar uma coluna com o mesmo nome na tabela filho. 

### <a name="naming-relationships"></a>Nomeando relacionamentos

As ```generatedKeyName``` ```referenceKeyName``` propriedades são usadas para relacionar dados entre tabelas ou mesmo entre tipos de projeção. Cada linha na tabela/projeção da criança tem uma propriedade que aponta para o pai. O nome da coluna ou propriedade ```referenceKeyName``` na criança é o do pai. Quando ```referenceKeyName``` o serviço não é fornecido, o ```generatedKeyName``` serviço o inadimplência é do pai. 

O POWER BI conta com essas chaves geradas para descobrir relacionamentos dentro das tabelas. Se você precisar da coluna na tabela filho ```referenceKeyName``` nomeada de forma diferente, defina a propriedade na tabela dos pais. Um exemplo seria definir ```generatedKeyName``` o iD na tabela pbiDocument e o ```referenceKeyName``` como DocumentID. Isso resultaria na coluna nas tabelas pbiEntities e pbiKeyPhrases contendo o id do documento que está sendo chamado documentID.

## <a name="projecting-to-objects"></a>Projetando para objetos

As projeções de objetos não têm as mesmas limitações que as projeções de tabela e são mais adequadas para projetar documentos grandes. Neste exemplo, projetamos todo o documento para uma projeção de objeto. As projeções de objetos são limitadas a uma única projeção em um recipiente e não podem ser fatiadas.

Para definir uma projeção de ```objects``` objeto, usaremos a matriz nas projeções. Você pode gerar uma nova forma usando a habilidade shaper ou usar a modelagem inline da projeção do objeto. Enquanto o exemplo das tabelas demonstrou a abordagem de criar uma forma e fatiamento, este exemplo demonstra o uso da modelagem inline. 

Modelagem inline é a capacidade de criar uma nova forma na definição das entradas para uma projeção. A modelagem inline cria um objeto anônimo que é idêntico ao `pbiShape`que uma habilidade shaper produziria (no nosso caso, ). A modelagem inline é útil se você estiver definindo uma forma que você não planeja reutilizar.

A propriedade de projeções é uma matriz. Para este exemplo, estamos adicionando uma nova instância de projeção à matriz, onde a definição knowledgeStore contém projeções inline. Ao usar projeções inline, você pode omiti-lo a habilidade shaper.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Projetando para arquivar

Projeções de arquivos são imagens que são extraídas do documento de origem ou saídas de enriquecimento que podem ser projetadas fora do processo de enriquecimento. Projeções de arquivos, semelhantes às projeções de objetos, são implementadas como blobs no Azure Storage e contêm a imagem. 

Para gerar uma projeção `files` de arquivo, usamos a matriz no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contêiner chamado `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projetando para vários tipos

Um cenário mais complexo pode exigir que você projete conteúdo entre tipos de projeção. Por exemplo, se você precisar projetar alguns dados, como frases-chave e entidades em tabelas, salve os resultados de OCR de texto e texto de layout como objetos e, em seguida, projete as imagens como arquivos. 

Neste exemplo, as atualizações para o skillset incluem as seguintes alterações:

1. Crie uma tabela com uma linha para cada documento.
1. Crie uma tabela relacionada à tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
1. Crie uma tabela relacionada à tabela de documentos com cada entidade identificada como uma linha nesta tabela.
1. Crie uma projeção de objeto com o texto de layout para cada imagem.
1. Crie uma projeção de arquivo, projetando cada imagem extraída.
1. Crie uma tabela de referência cruzada que contenha referências à tabela do documento, projeção de objeto com o texto do layout e a projeção do arquivo.

Essas mudanças são refletidas na definição de knowledgeStore mais abaixo. 

### <a name="shape-data-for-cross-projection"></a>Dados de forma para projeção cruzada

Para obter as formas que precisamos para essas projeções, comece adicionando `crossProjection`uma nova habilidade shaper que cria um objeto em forma chamado . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definir projeções de tabela, objeto e arquivo

A partir do objeto crossProjection consolidado, podemos cortar o objeto em várias tabelas, capturar a saída De OCR como blobs e, em seguida, salvar a imagem como arquivos (também no armazenamento Blob).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

As projeções de objeto exigem um nome de contêiner para cada projeção, projeções de objetoou projeções de arquivos não podem compartilhar um contêiner. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relacionamentos entre projeções de tabela, objeto e arquivo

Este exemplo também destaca outra característica das projeções. Ao definir vários tipos de projeções dentro do mesmo objeto de projeção, há uma relação expressa dentro e entre os diferentes tipos (tabelas, objetos, arquivos), permitindo que você comece com uma linha de tabela para um documento e encontre todo o texto OCR para as imagens dentro desse documento na projeção do objeto. 

Se você não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção. Por exemplo, o seguinte trecho resultará na relação das tabelas, mas sem relações entre as tabelas e as projeções do objeto (texto OCR). 

Grupos de projeção são úteis quando você deseja projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel power bi, e outro grupo de projeção para captura de dados usados para treinar um modelo de aprendizado de máquina envolto em uma habilidade personalizada.

Ao construir projeções de diferentes tipos, as projeções de arquivos e objetos são geradas primeiro, e os caminhos são adicionados às tabelas.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Problemas comuns

Ao definir uma projeção, existem algumas questões comuns que podem causar resultados imprevistos. Verifique se essas questões no armazenamento de conhecimento não é o que você espera.

+ Não moldar os enriquecimentos de cordas em JSON válido. Quando as cordas são enriquecidas, por exemplo, `merged_content` enriquecidas com frases-chave, a propriedade enriquecida é representada como uma criança de dentro da árvore de `merged_content` enriquecimento. A representação padrão não é bem formada JSON. Então, na hora da projeção, certifique-se de transformar o enriquecimento em um objeto JSON válido com um nome e um valor.

+ Omitindo ```/*``` o fim de um caminho de origem. Se a fonte de `/document/pbiShape/keyPhrases`uma projeção for, a matriz de frases-chave será projetada como um único objeto/linha. Em vez disso, `/document/pbiShape/keyPhrases/*` defina o caminho de origem para produzir uma única linha ou objeto para cada uma das frases-chave.

+ Erros de sintaxe de caminho. Os seletores de caminho são sensíveis a maiúsculas e minúsculas e podem levar a avisos de entrada ausentes se você não usar o caso exato do seletor.

## <a name="next-steps"></a>Próximas etapas

Os exemplos deste artigo demonstram padrões comuns sobre como criar projeções. Agora que você tem uma boa compreensão dos conceitos, você está melhor equipado para construir projeções para o seu cenário específico.

À medida que você explora novos recursos, considere o enriquecimento incremental como seu próximo passo. O enriquecimento incremental é baseado no cache, que permite que você reutilize quaisquer enriquecimentos que não sejam afetados de outra forma por uma modificação de skillset. Isso é especialmente útil para pipelines que incluem OCR e análise de imagens.

> [!div class="nextstepaction"]
> [Introdução ao enriquecimento incremental e cache](cognitive-search-incremental-indexing-conceptual.md)

Para uma visão geral das projeções, saiba mais sobre capacidades como grupos e fatiamento, e como você [defini-los em um conjunto de habilidades](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções em uma loja de conhecimento](knowledge-store-projection-overview.md)

