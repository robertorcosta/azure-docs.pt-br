---
title: Formato TSV de teste em lote - Fabricante QnA
titleSuffix: Azure Cognitive Services
description: Entenda o formato TSV para testes em lote
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507820"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV de teste em lote

O teste em lote está disponível no [código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) ou como um [velocível executável](https://aka.ms/qna_btzip)para download . O formato do comando para executar o teste em lote é:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valor Esperado|
|--|--|
|1|nome do arquivo tsv formatado com [campos de entrada TSV](#tsv-input-fields)|
|2|URI para endpoint, com SEU-HOST na página Publicar do portal QnA Maker.|
|3|ENDPOINT-KEY, encontrado na página Publicar do portal QnA Maker.|
|4|nome do arquivo tsv criado por teste de lote para resultados.|

Use as seguintes informações para entender e implementar o formato TSV para testes em lote. 

## <a name="tsv-input-fields"></a>Campos de entrada TSV

|Campos do arquivo de entrada TSV|Observações|
|--|--|
|KBID|Sua KB ID encontrada na página Publicar.|
|Pergunta|A pergunta que um usuário entraria.|
|Marcas de metadados|opcional|
|Parâmetro superior|opcional| 
|ID de resposta esperada|opcional|

![Formato de entrada para arquivo TSV para teste em lote.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campos de saída TSV 

|Parâmetros do arquivo de saída tsv|Observações|
|--|--|
|KBID|Sua KB ID encontrada na página Publicar.|
|Pergunta|A pergunta como inserida a partir do arquivo de entrada.|
|Resposta|Melhor resposta de sua base de conhecimento.|
|Responder iD|Responder iD|
|Pontuação|Pontuação de previsão para resposta. |
|Marcas de metadados|associado com resposta devolvida|
|ID de resposta esperada|opcional (somente quando o ID de resposta esperado é dado)|
|Rótulo de julgamento|opcional, os valores podem ser: correto ou incorreto (somente quando a resposta esperada é dada)|
