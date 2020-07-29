---
title: Linguagens com suporte no Azure Functions
description: Saiba quais idiomas têm suporte (GA) e quais estão em versão prévia e maneiras de estender o desenvolvimento de funções para outras linguagens.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994894"
---
# <a name="supported-languages-in-azure-functions"></a>Linguagens com suporte no Azure Functions

Este artigo explica os níveis de suporte oferecido para linguagens que você pode usar com o Azure Functions. Ele também descreve estratégias para a criação de funções usando linguagens que não têm suporte nativo.

## <a name="levels-of-support"></a>Níveis de suporte

Há dois níveis de suporte:

* **Geralmente disponível (GA)** – com suporte total e aprovado para uso em produção.
* **Versão prévia** – ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.

## <a name="languages-by-runtime-version"></a>Idiomas por versão de tempo de execução 

[Três versões do tempo de execução de Azure Functions](functions-versions.md) estão disponíveis. A tabela a seguir indica quais linguagens têm suporte em cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>Manipuladores personalizados (versão prévia) 

Os manipuladores personalizados são servidores Web leves que recebem eventos do host Azure Functions. Qualquer linguagem que ofereça suporte a primitivos HTTP pode implementar um manipulador personalizado. Isso significa que os manipuladores personalizados podem ser usados para criar funções em linguagens que não são oficialmente suportadas. Para saber mais, consulte [Azure Functions manipuladores personalizados (versão prévia)](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Extensibilidade de linguagem

A partir da versão 2. x, o tempo de execução foi projetado para oferecer [extensibilidade de linguagem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). As linguagens JavaScript e Java no runtime 2.x são criadas com essa extensibilidade.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como desenvolver funções nos idiomas com suporte, consulte os seguintes recursos:

+ [Referência do desenvolvedor da biblioteca de classes C#](functions-dotnet-class-library.md)
+ [Referência do desenvolvedor de script C#](functions-reference-csharp.md)
+ [Referência do desenvolvedor Java](functions-reference-java.md)
+ [Referência do desenvolvedor de JavaScript](functions-reference-node.md)
+ [Referência do desenvolvedor do PowerShell](functions-reference-powershell.md)
+ [Referência do desenvolvedor do Python](functions-reference-python.md)
+ [Referência do desenvolvedor TypeScript](functions-reference-node.md#typescript)
