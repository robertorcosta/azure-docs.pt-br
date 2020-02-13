---
title: Definir projeções em uma loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns de como projetar documentos aprimorados na loja de conhecimento para uso com o Power BI ou o Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165508"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Projeções da loja de conhecimento: como formatar e exportar aprimoramentos na loja de conhecimento

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

As projeções são a expressão física de documentos aprimorados em uma loja de conhecimento. O uso efetivo de seus documentos aprimorados exige estrutura. Neste artigo, você explorará a estrutura e as relações, aprenderá a criar propriedades de projeção, bem como a relacionar dados nos tipos de projeção que você criar. 

Para criar uma projeção, você deve formatar os dados usando uma habilidade de formador para criar um objeto personalizado ou usar a sintaxe de shaping embutida. Uma forma de dados contém todos os dados que você pretende projetar. Este documento fornece um exemplo de cada opção, você pode optar por usar qualquer uma das opções para projeções que você criar.


Há três tipos de projeções:
+ Tabelas
+ Objetos
+ Arquivos

As projeções de tabela são armazenadas no armazenamento de tabelas do Azure. As projeções de objeto e arquivo são gravadas no armazenamento de BLOBs, as projeções de objeto são salvas como arquivos JSON e podem conter conteúdo do documento e quaisquer saídas ou aprimoramentos de habilidades. O pipeline de enriquecimento também pode extrair binários como imagens, esses binários são projetados como projeções de arquivo. Quando um objeto binário é projetado como uma projeção de objeto, somente os metadados associados a ele são salvos como um blob JSON. 

Para entender a interseção entre a modelagem de dados e as projeções, usaremos o seguinte qualificable como base para explorar várias configurações. Esse qualificable processa conteúdo bruto de imagem e texto. As projeções serão definidas a partir do conteúdo do documento e das saídas das habilidades para os cenários para os quais desejamos dar suporte.

Como alternativa, você pode baixar e usar um [exemplo de API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) com todas as chamadas neste passo a passos.

> [!IMPORTANT] 
> Ao experimentar projeções, é útil [definir a propriedade cache do indexador](search-howto-incremental-index.md) para garantir o controle de custos. A edição das projeções fará com que todo o documento seja aprimorado novamente se o cache do indexador não estiver definido. Quando o cache é definido e apenas as projeções são atualizadas, as execuções do conjunto de qualificações para documentos previamente aprimorados não resultam em nenhum encargo de serviços cognitivas.


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

Agora podemos adicionar o objeto `knowledgeStore` e configurar as projeções para cada um dos cenários, conforme necessário. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projeção para tabelas para cenários como Power BI

> [!NOTE] 
> Como a loja de conhecimento é uma conta de armazenamento do Azure, as projeções de tabela são tabelas de armazenamento do Azure e são governadas pelos limites de armazenamento em tabelas, para obter mais informações, consulte [limites de armazenamento de tabelas](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior que 64 KB. Essas restrições tornam as tabelas uma boa solução para armazenar um grande número de pequenas entidades.

Power BI pode ler de tabelas e descobrir relações com base nas chaves que as projeções da loja de conhecimento criam, isso torna as tabelas uma boa opção para projetar dados quando você estiver tentando criar um painel em seus dados aprimorados. Supondo que estamos tentando criar um painel no qual podemos Visualizar as frases-chave extraídas dos documentos como uma nuvem do Word, podemos adicionar uma habilidade de forma do contentor para criar uma forma personalizada que tenha detalhes específicos do documento e frases-chave. Adicione a habilidade do modelador ao contextset para criar um novo enriquecimento chamado ```pbiShape``` na ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade de formador para criar uma forma personalizada

Crie uma forma personalizada que você pode projetar no armazenamento de tabelas. Sem uma forma personalizada, uma projeção só pode fazer referência a um único nó (uma projeção por saída). A criação de uma forma personalizada permite agregar vários elementos em um novo lógico inteiro que pode ser projetado como uma única tabela ou segmentado e distribuído em uma coleção de tabelas. Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto é chamado de pbiShape e é pai em `/document`. 

> [!IMPORTANT] 
> Caminhos de origem para aprimoramentos são necessários para serem objetos JSON bem formados, antes que possam ser projetados. A árvore de enriquecimento pode representar aprimoramentos que não são JSON bem formados, por exemplo, quando um enriquecimento é pai de um primitve como uma cadeia de caracteres. Observe como `KeyPhrases` e `Entities` são encapsulados em um objeto JSON válido com o `sourceContext`, isso é necessário, pois `keyphrases` e `entities` são aprimoramentos em primitivos e precisam ser convertidos em JSON válido antes que possam ser projetados.

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
Adicione a habilidade do modelador que acabamos de definir à lista de habilidades no configurador de habilidades. 

Agora que temos todos os dados necessários para projetar em tabelas, atualize o objeto knowledgeStore com as definições de tabela. 

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

Defina a propriedade ```storageConnectionString``` como uma cadeia de conexão de conta de armazenamento de uso geral v2 válida. Nesse cenário, definimos três tabelas no objeto de projeção definindo as propriedades ```tableName```, ```source``` e ```generatedKeyName```. Agora você pode atualizar o configurador de habilidades emitindo a solicitação PUT.

### <a name="slicing"></a>Divisão 

Ao iniciar com uma forma consolidada onde todo o conteúdo que precisa ser projetado está em uma única forma (ou nó de enriquecimento), a divisão fornece a capacidade de dividir um único nó em várias tabelas ou objetos. Aqui, o objeto ```pbiShape``` é dividido em várias tabelas. O recurso de divisão permite extrair partes da forma, ```keyPhrases``` e ```Entities``` em tabelas separadas. Isso é útil, pois várias entidades e prefrases são associadas a cada documento. A divisão implícita gera uma relação entre as tabelas pai e filho, usando o ```generatedKeyName``` na tabela pai para criar uma coluna com o mesmo nome na tabela filho. 

### <a name="naming-relationships"></a>Relações de nomenclatura
As propriedades ```generatedKeyName``` e ```referenceKeyName``` são usadas para relacionar dados entre tabelas ou até mesmo em tipos de projeção. Cada linha na tabela/projeção filho tem uma propriedade apontando de volta para o pai. O nome da coluna ou da propriedade no filho é a ```referenceKeyName``` do pai. Quando o ```referenceKeyName``` não é fornecido, o serviço o padroniza para o ```generatedKeyName``` do pai. O PowerBI conta com essas chaves geradas para descobrir relações dentro das tabelas. Se você precisar da coluna na tabela filho denominada de modo diferente, defina a propriedade ```referenceKeyName``` na tabela pai. Um exemplo seria definir o ```generatedKeyName``` como ID na tabela pbiDocument e o ```referenceKeyName``` como DocumentID. Isso resultaria na coluna nas tabelas pbiEntities e pbiKeyPhrases que contêm a ID do documento que está sendo nomeada DocumentID.

Salvar o qualificable atualizado e executar o indexador, agora você tem uma projeção de trabalho com três tabelas. A importação dessas tabelas para Power BI deve resultar na Power BI descoberta automática das relações.

## <a name="projecting-to-objects"></a>Projeção para objetos

As projeções de objeto não têm as mesmas limitações que as projeções de tabela, são mais adequadas para projetar documentos grandes. Neste exemplo, projetamos o documento inteiro em uma projeção de objeto. As projeções de objeto são limitadas a uma única projeção em um contêiner.
Para definir uma projeção de objeto, usaremos a matriz de ```objects``` nas projeções. Você pode gerar uma nova forma usando a habilidade do modelador ou usar o Shaping embutido da projeção do objeto. Embora o exemplo de tabelas tenha demonstrado a abordagem de criação de uma forma e de divisão, este exemplo demonstra o uso de shaping embutido. O Shaping embutido é a capacidade de criar uma nova forma na definição das entradas para uma projeção. O Shaping embutido cria um objeto anônimo que é idêntico ao que um forma semelhante produziria. O Shaping embutido é útil se você estiver definindo uma forma que não planeja reutilizar.
A propriedade projetions é uma matriz, para este exemplo, estamos adicionando uma nova instância de projeção à matriz. Atualize a definição de knowledgeStore com as projeções definidas em linha, você não precisa de uma habilidade de forma de formatação ao usar projeções embutidas.

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
## <a name="file-projections"></a>Projeções de arquivo

Projeções de arquivos são imagens que são extraídas do documento de origem ou saídas de aprimoramentos que podem ser projetadas fora do processo de enriquecimento. As projeções de arquivo, semelhantes às projeções de objeto, são implementadas como BLOBs e contêm a imagem. Para gerar uma projeção de arquivo, usamos a matriz ```files``` no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contêiner chamado `samplefile`.

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

## <a name="projecting-to-multiple-types"></a>Projeção para vários tipos

Um cenário mais complexo pode exigir que você projeto o conteúdo entre os tipos de projeção. Por exemplo, se você precisar projetar alguns dados como frases-chave e entidades para tabelas, salve os resultados do OCR de texto e de layout como objetos e projeto as imagens como arquivos. Esta atualização para o configurador de habilidades:

1. Crie uma tabela com uma linha para cada documento.
2. Crie uma tabela relacionada à tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
3. Crie uma tabela relacionada à tabela de documentos com cada entidade identificada como uma linha nesta tabela.
4. Crie uma projeção de objeto com o texto de layout para cada imagem.
5. Crie uma projeção de arquivo, projetando cada imagem extraída.
6. Crie uma tabela de referência cruzada que contenha referências à tabela de documentos, projeção de objeto com o texto de layout e a projeção de arquivo.

Comece adicionando uma nova habilidade do modelador à matriz de habilidades que cria um objeto moldado. 

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

As projeções de objeto exigem um nome de contêiner para cada projeção, projeções de objeto ou projeções de arquivo não podem compartilhar um contêiner. 

### <a name="relationships"></a>Relações

Este exemplo também realça outro recurso de projeções, definindo vários tipos de projeções dentro do mesmo objeto de projeção, há uma relação expressa dentro e entre os diferentes tipos (tabelas, objetos, arquivos) de projeções, permitindo Você começa com uma linha de tabela para um documento e localiza todo o texto OCR das imagens dentro desse documento na projeção do objeto. Se você não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção, por exemplo, o trecho a seguir fará com que as tabelas estejam relacionadas, mas nenhuma relação entre as tabelas e as projeções de texto OCR. Os grupos de projeção são úteis quando você deseja projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel de Power BI e outro grupo de projeção para usar os dados para treinar um modelo de ia para uma habilidade.
Ao criar projeções de tipos diferentes, as projeções de arquivo e de objeto são geradas primeiro e os caminhos são adicionados às tabelas.

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

Esses exemplos demonstraram os padrões comuns de como usar projeções, agora você também deve ter uma boa compreensão dos conceitos sobre a criação de uma projeção para seu cenário específico.

## <a name="common-issues"></a>Problemas comuns

Ao definir uma projeção, há alguns problemas comuns que podem causar resultados imprevistos.

1. Não moldar os aprimoramentos de cadeia de caracteres. Quando as cadeias de caracteres são aprimoradas, por exemplo ```merged_content``` aprimoradas com frases-chave, a propriedade enriqueceda é representada como um filho de merged_content dentro da árvore de enriquecimento. Mas, no momento da projeção, isso precisa ser transformado em um objeto JSON válido com um nome e um valor.
2. Omitir o ```/*``` no final de um caminho de origem. Se, por exemplo, a origem de uma projeção for ```/document/pbiShape/keyPhrases``` a matriz de frases-chave é projetada como um único objeto/linha. Definir o caminho de origem para ```/document/pbiShape/keyPhrases/*``` gera uma única linha ou objeto para cada uma das frases-chave.
3. Os seletores de caminho diferenciam maiúsculas de minúsculas e podem levar a avisos de entrada ausentes se você não usar o caso exato para o seletor.

