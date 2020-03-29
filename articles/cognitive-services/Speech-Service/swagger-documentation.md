---
title: Documentação swagger - Serviço de fala
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430798"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço Speech oferece uma especificação Swagger para interagir com um punhado de APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos finais personalizados, fazer filas em lotes e gerenciar assinaturas. A maioria das operações disponíveis por meio do portal de Fala Personalizada pode ser concluída de forma programática usando essas APIs.

> [!NOTE]
> Há suporte para operações de conversão de fala em texto e texto em fala disponíveis como APIs REST, que, por vez, estão documentadas na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://cris.ai/swagger/ui/index) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará definir Swagger para a mesma região que sua assinatura do serviço Speech. Você pode confirmar sua região no portal Azure seu recurso de serviço de fala. Para obter uma lista completa das regiões suportadas, consulte [regiões](regions.md).

1. Acesse https://editor.swagger.io
2. Clique em **Arquivo** e, em seguida, em **Importar**
3. Digite a URL de swagger, incluindo a região para sua assinatura do serviço Speech`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **Gerar Cliente** e selecione o Python
5. Salvar a biblioteca de clientes

Você pode usar a biblioteca Python que você gerou com as amostras do [serviço Speech no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de Referência

* [REST (Swagger): Transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)
* [API REST: Fala-a-texto](rest-speech-to-text.md)
* [API REST: Texto-para-fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Amostras do serviço de fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
