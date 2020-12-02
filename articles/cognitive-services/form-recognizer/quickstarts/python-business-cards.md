---
title: 'Início Rápido: Extrair dados de cartões de visita usando o Python – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST de Reconhecimento de Formulários com o Python para extrair dados de imagens de cartões de visita em inglês.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012417"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Início Rápido: Extrair dados de cartões de visita usando a API REST de Reconhecimento de Formulários com o Python

Neste guia de início rápido, você usará a API REST de Reconhecimento de Formulários do Azure com o Python para extrair e identificar informações relevantes em cartões de visita.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, é necessário ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar o exemplo localmente).
- Uma imagem de um cartão de visita. Use uma [imagem de exemplo](../media/business-card-english.jpg) para este início rápido.

> [!NOTE]
> Este guia de início rápido usa um arquivo local. Para usar uma imagem remota de cartão de visita acessada pela URL, confira a [documentação de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync).

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analisar um cartão de visita

Para começar a analisar um cartão de visita, chame a API **[Analisar Cartão de Visita](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** usando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. Substitua `<endpoint>` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `<path to your business card>` pelo caminho local para a imagem do seu cartão de visita ou PDF.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `<file type>` por 'image/jpeg', 'image/png', 'application/pdf' ou 'image/tiff'.

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Salve o código como um arquivo com uma extensão .py. Por exemplo, *form-recognizer-businesscards.py*.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-businesscards.py`.

Você receberá uma resposta `202 (Success)` que inclui um cabeçalho **Operation-Location**, que será impresso pelo script no console. Esse cabeçalho contém uma ID de resultado que você pode usar para consultar o status da operação de execução prolongada e obter os resultados. No valor de exemplo a seguir, a cadeia de caracteres após `operations/` é a ID do resultado.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Obter os resultados do cartão de visita

Depois de chamar a API **Analisar Cartão de Visita**, chame a API **[Obter Resultado da Análise do Cartão de Visita](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** para obter o status da operação e os dados extraídos. Adicione o código a seguir à parte inferior do script Python. Isso usa o valor da ID do resultado em uma nova chamada à API. Esse script chama a API em intervalos regulares, até que os resultados estejam disponíveis. É recomendável fazer um intervalo de um segundo ou mais.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Salve o script.
1. Mais uma vez, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Examinar a resposta
![Um cartão de visita da empresa Contoso](../media/business-card-english.jpg)

Este exemplo ilustra a saída JSON retornada pelo Reconhecimento de Formulários. Ela foi truncada para facilitar a leitura.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

O script imprimirá as respostas no console até que a operação **Analisar Cartão de Visita** seja concluída. O nó `"readResults"` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. O nó `"documentResults"` contém os valores específicos do cartão de visita descobertos pelo modelo. É nele que você encontrará informações de contato úteis, como nome da empresa, nome, sobrenome, número de telefone etc.


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou a API REST de Reconhecimento de Formulários com o Python para extrair o conteúdo de um cartão de visita. Em seguida, confira a documentação de referência para explorar a API de Reconhecimento de Formulários de forma mais aprofundada.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
