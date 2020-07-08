---
title: 'Início Rápido: Treinar um modelo e extrair dados de formulário usando o cURL – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usará a API REST do Reconhecimento de Formulários com o cURL para treinar em um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: f89ab93820770eb8b5485bb7911c60fe2438454a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026812"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Início Rápido: Treinar em um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o cURL

Neste Início Rápido, você usará a API REST do Reconhecimento de Formulários do Azure com o cURL para treinar e pontuar formulários a fim de extrair pares chave-valor e tabelas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, é necessário ter:
- [cURL](https://curl.haxx.se/windows/) instalado.
- Um conjunto com pelo menos seis formulários do mesmo tipo. Você usará cinco deles para treinar o modelo e, em seguida, o testará com o sexto formato. Seus formulários podem ser de tipos de arquivo diferentes, mas devem ser do mesmo tipo de documento. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este início rápido. Faça upload dos arquivos de treinamento na raiz de um contêiner de Armazenamento de Blobs em uma conta do Armazenamento do Azure. Você pode colocar os arquivos de teste em uma pasta separada.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Treinar um modelo do Reconhecimento de Formulários

Primeiro, você precisará de um conjunto de dados de treinamento em um blob do Armazenamento do Azure. É necessário ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/da mesma estrutura dos dados de entrada principais. Ou você pode usar um único formulário vazio com dois formulários preenchidos. O nome do arquivo do formulário vazio precisa incluir a palavra "vazio". Veja [Criar um conjunto de dados de treinamento para um modelo personalizado](../build-training-data-set.md) para obter dicas e opções para compilar os dados de treinamento.

> [!NOTE]
> Você pode usar o recurso de dados rotulados para rotular manualmente alguns ou todos os seus dados de treinamento com antecedência. Esse é um processo mais complexo, mas resulta em um modelo melhor treinado. Confira a seção [Treinar com rótulos](../overview.md#train-with-labels) da visão geral para saber mais sobre esse recurso.

Para treinar um modelo do Reconhecimento de Formulários usando os documentos no contêiner de blobs do Azure, chame a API **[Treinar Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** executando o comando cURL a seguir. Antes de executar o comando, faça essas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `<SAS URL>` pela URL da Assinatura de Acesso Compartilhado (SAS) do contêiner de armazenamento de Blobs do Azure. Para recuperar a URL de SAS, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no seu contêiner e selecione **Obter assinatura de acesso compartilhado**. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Você receberá uma resposta `201 (Success)` com um cabeçalho **Location**. O valor desse cabeçalho é a ID do novo modelo que está sendo treinado. 

## <a name="get-training-results"></a>Obter os resultados do treinamento

Depois de iniciar a operação de treinamento, você usa uma nova operação, **[Obter Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** , para verificar o status de treinamento. Passe a ID do modelo para esta chamada à API para verificar o status de treinamento:

1. Substitua `<Endpoint>` pelo ponto de extremidade que você obteve com a chave de assinatura do Reconhecimento de Formulários.
1. Substitua `<subscription key>` por sua chave de assinatura
1. Substitua `<model ID>` pela ID do modelo que você recebeu na seção anterior

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Você receberá a resposta `200 (Success)` com um corpo JSON no formato a seguir. Observe o campo `"status"`. Ele terá o valor `"ready"` quando o treinamento for concluído. Se o modelo não tiver terminado o treinamento, você precisará consultar o serviço novamente, executando o comando mais uma vez. Recomendamos dar um intervalo de um segundo ou mais entre chamadas.

O campo `"modelId"` contém a ID do modelo que você está treinando. Você precisará dela para a próxima etapa.
    
```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares chave-valor e tabelas

Em seguida, você usará seu modelo treinado recentemente para analisar um documento e extrair pares chave-valor e tabelas dele. Chame a API **[Analisar Formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** executando o comando cURL a seguir. Antes de executar o comando, faça essas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `<model ID>` pela ID do modelo recebida na seção anterior.
1. Substitua `<SAS URL>` por uma URL SAS em seu arquivo no armazenamento do Azure. Siga as etapas na seção Treinamento, mas, em vez de obter uma URL SAS para todo o contêiner de blobs, obtenha uma para o arquivo específico que você deseja analisar.
1. Substitua `<subscription key>` por sua chave de assinatura.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho inclui uma ID de resultados que pode ser usada para rastrear os resultados da operação de análise. Salve essa ID de resultados para a próxima etapa.

## <a name="get-the-analyze-results"></a>Obter os resultados da análise

Use a API a seguir para consultar os resultados da operação de análise.

1. Substitua `<Endpoint>` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `<result ID>` pela ID recebida na seção anterior.
1. Substitua `<subscription key>` por sua chave de assinatura.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Você receberá a resposta `200 (Success)` com um corpo JSON no formato a seguir. A saída foi abreviada para maior simplicidade. Observe o campo `"status"` próximo à parte inferior. Ele terá o valor `"succeeded"` quando a operação de análise for concluída. Se a operação de análise não tiver sido concluída, você precisará consultar o serviço novamente, executando o comando mais uma vez. Recomendamos dar um intervalo de um segundo ou mais entre chamadas.

As principais associações de tabelas e pares chave-valor estão no nó `"pageResults"`. Se você também especificou a extração de texto sem formatação por meio do parâmetro de URL *includeTextDetails*, o nó `"readResults"` mostrará o conteúdo e as posições de todo o texto no documento.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Aprimorar os resultados

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou a API REST do Reconhecimento de Formulários com o cURL para treinar em um modelo e executá-lo em um caso de exemplo. Em seguida, confira a documentação de referência para explorar a API de Reconhecimento de Formulários de forma mais aprofundada.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
