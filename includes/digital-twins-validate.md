---
author: baanders
description: arquivo de inclusão descrevendo como validar modelos de gêmeos digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: da42f847bdd57c4dae0cde673c9c9e08d51a758d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998308"
---
> [!TIP]
> Depois de criar um modelo, é recomendável validar seus modelos offline antes de carregá-los em sua instância de gêmeos digital do Azure.

Há um exemplo independente de linguagem disponível para validar documentos de modelo para verificar se o DTDL está correto antes de carregá-lo em sua instância. Ele está localizado aqui: [**exemplo de validador de DTDL**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* O exemplo do validador DTDL é criado em uma biblioteca do analisador do .NET DTDL, que está disponível no NuGet como uma biblioteca do lado do cliente: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Você também pode usar a biblioteca diretamente para criar sua própria solução de validação. Ao usar a biblioteca do analisador, certifique-se de usar uma versão compatível com a versão que o Azure digital gêmeos está executando. Durante a visualização, esta é a versão *3.12.4*.

Você pode aprender mais sobre a amostra do validador e a biblioteca do analisador, incluindo exemplos de uso, em [*instruções: analisar e validar modelos*](../articles/digital-twins/how-to-parse-models.md).