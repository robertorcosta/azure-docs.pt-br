---
title: Atualizar para a v3.0 da API da Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar a API de Leitura da Pesquisa Visual Computacional v2.0/v2.1 para a v3.0.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214185"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Atualizar a API de Leitura da Pesquisa Visual Computacional v2.0/v2.1 para a v3.0

Este guia mostra como atualizar o código da API REST da Pesquisa Visual Computacional v2.0 ou v2.1 existente para operações de Leitura da v3.0. 

## <a name="upgrade-batch-read-file-to-read"></a>Fazer upgrade de `Batch Read File` para `Read`


1. Altere o caminho da API para `Batch Read File` 2.x, da seguinte maneira:


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    Um novo parâmetro _language_ opcional está disponível. Se não souber o idioma de seu documento, ou se ele puder ser multilíngue, não o inclua. 

2. Altere o caminho da API para `Get Read Results` no 2.x, da seguinte maneira:

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. Altere o código para verificar os resultados JSON de `Get Read Operation Result`. Quando é bem-sucedida, a chamada para `Get Read Operation Result` retorna um campo de cadeia de caracteres de status no corpo JSON. Os valores a seguir do v2.0 foram alterados a fim de se alinharem melhor com as outras APIs REST do Serviço Cognitivo. 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Altere o código para interpretar o JSON do resultado de reconhecimento final de `Get Read Operation Result`. 

    Observe as seguintes alterações no JSON:
    
    - No v2.x, `"Get Read Operation Result"` retornará o JSON de reconhecimento de OCR quando o status for `"Succeeded"`. No v3.0, esse campo é `"succeeded"`.
    - Para obter a raiz da matriz da página, altere a hierarquia JSON de `"recognitionResults"` para `"analyzeResult"`/`"readResults"`. A hierarquia JSON de linhas e de palavras por página permanece inalterada, portanto, nenhuma alteração de código é necessária.
    -   O ângulo da página `"clockwiseOrientation"` foi renomeado para `"angle"` e o intervalo foi alterado de 0 a 360 graus para -180 a 180 graus. Dependendo de seu código, você pode ou não precisar fazer alterações, pois a maioria das funções matemáticas pode lidar com ambos os intervalos.
    -   A API v3.0 também introduz os seguintes aprimoramentos, que são opcionais: -`"createdDateTime"` e `"lastUpdatedDateTime"` foram adicionados para que você possa acompanhar a duração do processamento. Confira a documentação para obter mais detalhes. 
        - `"version"` informa a versão da API usada para gerar os resultados
        - Um `"confidence"` por palavra foi adicionado. Esse valor é calibrado de maneira que um valor de 0.95 indique que há uma chance de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar qual texto deve ser enviado para revisão humana. 
    
    
        Na 2.X, o formato de saída é o seguinte: 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    Na v3.0, ele foi ajustado:
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>Upgrade de `Recognize Text` para `Read`
`Recognize Text` é uma operação de *versão prévia* que está sendo *preterida em todas as versões da API da Pesquisa Visual Computacional*. É necessário migrar da `Recognize Text` para a `Read` (v3.0) ou `Batch Read File` (v2.0, v2.1). A v3.0 de `Read` inclui modelos mais novos e melhores para reconhecimento de texto, além de recursos adicionais e, portanto, é recomendada. Para fazer upgrade de `Recognize Text` para `Read`:

1. Altere o caminho da API para `Recognize Text` no v2.x, da seguinte maneira:


    |Reconhecimento de Texto 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    O parâmetro _mode_ não tem suporte no `Read`. Tanto texto manuscrito quanto impresso terão suporte automaticamente.
    
    Um novo parâmetro _language_ opcional está disponível na v3.0. Se não souber o idioma de seu documento, ou se ele puder ser multilíngue, não o inclua. 

2. Altere o caminho da API para `Get Recognize Text Operation Result` no v2.x, da seguinte maneira:

    |Reconhecimento de Texto 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. Altere o código para verificar os resultados JSON de `Get Recognize Text Operation Result`. Quando é bem-sucedida, a chamada para `Get Read Operation Result` retorna um campo de cadeia de caracteres de status no corpo JSON. 
 
    |Reconhecimento de Texto 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Altere o código para interpretar o JSON do resultado de reconhecimento final de `Get Recognize Text Operation Result` para dar suporte a `Get Read Operation Result`.

    Observe as seguintes alterações no JSON:    

    - No v2.x, `"Get Read Operation Result"` retornará o JSON de reconhecimento de OCR quando o status for `"Succeeded"`. No v3.0, esse campo é `"succeeded"`.
    - Para obter a raiz da matriz da página, altere a hierarquia JSON de `"recognitionResult"` para `"analyzeResult"`/`"readResults"`. A hierarquia JSON de linhas e de palavras por página permanece inalterada, portanto, nenhuma alteração de código é necessária.
    -   A API v3.0 também introduz os aprimoramentos a seguir, cujo uso é opcional. Consulte a referência da API para obter mais detalhes: -`"createdDateTime"` e `"lastUpdatedDateTime"` foram adicionados para que você possa acompanhar a duração do processamento. Confira a documentação para obter mais detalhes. 
        - `"version"` informa a versão da API usada para gerar os resultados
        - Um `"confidence"` por palavra foi adicionado. Esse valor é calibrado de maneira que um valor de 0.95 indique que há uma chance de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar qual texto deve ser enviado para revisão humana. 
        - `"angle"` orientação geral do texto no sentido horário, medida em graus entre (-180, 180).
        -  `"width"` e `"height"` fornecem as dimensões do documento e `"unit"` fornece a unidade dessas dimensões (pixels ou polegadas, dependendo do tipo de documento).
        - `"page"` há suporte para documentos multipágina
        - `"language"` o idioma de entrada do documento (do parâmetro opcional _language_.)


    Na 2.X, o formato de saída é o seguinte: 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    Na v3.0, ele foi ajustado:
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>Todas as outras operações

Não há outras alterações da falha entre a v2.X e a v3.0 da API da Pesquisa Visual Computacional. Você pode apenas modificar o caminho da API e substituir `v2.0` por `v3.0`.
