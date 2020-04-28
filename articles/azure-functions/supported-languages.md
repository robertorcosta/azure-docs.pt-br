---
title: Linguagens com suporte no Azure Functions
description: Saiba quais linguagens são compatíveis (GA) e quais são experimentais ou estão na versão prévia.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942249"
---
# <a name="supported-languages-in-azure-functions"></a>Linguagens com suporte no Azure Functions

Este artigo explica os níveis de suporte oferecido para linguagens que você pode usar com o Azure Functions.

## <a name="levels-of-support"></a>Níveis de suporte

Há três níveis de suporte:

* **Geralmente disponível (GA)** – com suporte total e aprovado para uso em produção.
* **Versão prévia** – ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.
* **Experimental** – não tem suporte e pode ser abandonado no futuro; nenhuma garantia de status de GA ou de versão prévia eventual.

## <a name="languages-by-runtime-version"></a>Idiomas por versão de tempo de execução 

[Três versões do tempo de execução de Azure Functions](functions-versions.md) estão disponíveis. A tabela a seguir indica quais linguagens têm suporte em cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguagens experimentais

As linguagens experimentais na versão 1.x não escalam bem e não oferecem suporte a todas as associações.

Não use recursos experimentais para algo em que você confia, pois não há suporte oficial para eles. Não devem ser abertos casos de suporte para problemas com linguagens experimentais. 

Versões de tempo de execução posteriores não dão suporte a idiomas experimentais. O suporte para novos idiomas é adicionado somente quando o idioma pode ter suporte em produção. 

### <a name="language-extensibility"></a>Extensibilidade de linguagem

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
