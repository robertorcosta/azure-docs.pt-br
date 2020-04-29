---
title: Documentação do Swagger-serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação do Swagger pode ser usada para gerar SDKs automaticamente para várias linguagens de programação. Todas as operações em nosso serviço são compatíveis com o Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "77430798"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço de fala oferece uma especificação Swagger para interagir com algumas APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, enfileirar transcrições em lote e gerenciar assinaturas. A maioria das operações disponíveis por meio do portal de Fala Personalizada pode ser concluída de forma programática usando essas APIs.

> [!NOTE]
> Há suporte para operações de conversão de fala em texto e texto em fala disponíveis como APIs REST, que, por vez, estão documentadas na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://cris.ai/swagger/ui/index) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará definir o Swagger para a mesma região que sua assinatura do serviço de fala. Você pode confirmar sua região no portal do Azure em seu recurso de serviço de fala. Para obter uma lista completa das regiões com suporte, consulte [regiões](regions.md).

1. Acesse https://editor.swagger.io
2. Clique em **Arquivo** e, em seguida, em **Importar**
3. Insira a URL do Swagger, incluindo a região da sua assinatura do serviço de fala`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **gerar cliente** e selecione Python
5. Salvar a biblioteca de clientes

Você pode usar a biblioteca do Python gerada com os [exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de Referência

* [REST (Swagger): transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
