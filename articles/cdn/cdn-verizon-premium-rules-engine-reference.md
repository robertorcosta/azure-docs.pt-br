---
title: Referência do mecanismo de regras CDN do Azure | Microsoft Docs
description: Documentação de referência para recursos e condições de correspondência do mecanismo de regras da CDN do Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996765"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>CDN azure da Verizon Premium rege referência do motor

Este artigo lista as descrições detalhadas dos recursos e condições de correspondência disponíveis para o mecanismo de regras da [CDN (Rede de Distribuição de Conteúdo)](cdn-verizon-premium-rules-engine.md).

O mecanismo de regras foi projetado para ser a autoridade final sobre como os tipos específicos de solicitações são processados pela CDN.

**Usos comuns**:

- Substituir ou definir uma política de cache personalizada.
- Proteger ou negar solicitações de conteúdo confidencial.
- Solicitações de redirecionamento.
- Armazenar dados de log personalizados.

## <a name="terminology"></a>Terminologia

Uma regra é definida através do uso de [**expressões condicionais**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**condições de correspondência**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) e [**recursos**](cdn-verizon-premium-rules-engine-reference-features.md). Esses elementos são destacados na ilustração a seguir:

 ![Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxe

A maneira como os caracteres especiais são tratados varia de acordo com a forma como um recurso ou condição de correspondência manipula valores de texto. Uma condição de correspondência ou recurso pode interpretar o texto de uma das seguintes maneiras:

1. [**Valores literais**](#literal-values)
2. [**Valores curinga**](#wildcard-values)
3. [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como parte do valor que deve ser correspondido. Em outras palavras, uma condição de correspondência literal definida para `\'*'\` será satisfeita somente quando esse valor exato (ou seja, `\'*'\`) for localizado.

Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores de caractere curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como o seguinte conjunto de caracteres é interpretado:

Caractere | Descrição
----------|------------
\ | Uma barra invertida é usada para funcionar como escape para qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape.<br/>Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
Space | Um caractere de espaço indica que uma condição de correspondência pode ser atendida por qualquer um dos valores especificados ou padrões.
'valor' | As aspas simples não têm significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes maneiras:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Permite que um caractere especial seja especificado como um caractere literal. Por exemplo, é possível especificar um caractere de espaço literal ao incluir um caractere de espaço dentro de um conjunto de aspas simples (ou seja, `' '` ou `'sample value'`).<br/>- Permite a especificação de um valor em branco. Especifique um valor em branco, especificando um conjunto de aspas simples (ou seja, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um curinga, então ele será automaticamente considerado como um valor literal, o que significa que não será necessário especificar um conjunto de aspas simples.<br/>- Se uma barra invertida não funcionar como escape para outro caractere nesta tabela, ela será ignorada quando for especificada dentro de um conjunto de aspas simples.<br/>- Outra maneira de especificar um caractere especial como um caractere literal é isolá-lo, usando uma barra invertida (ou seja, `\`).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é pesquisado dentro de um valor de texto. A notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela a seguir indica como os caracteres especiais são tratados por condições de correspondência e recursos que dão suporte a expressões regulares.

Caractere especial | Descrição
------------------|------------
\ | Uma barra invertida escapa ao caractere que o segue, fazendo com que esse caractere seja tratado como um valor literal em vez de assumir o significado de expressão regular. Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | O significado de um símbolo de porcentagem depende de seu uso.<br/><br/> `%{HTTPVariable}`: essa sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: essa sintaxe usa um símbolo de porcentagem para identificar uma variável HTTP e como um delimitador.<br />`\%`: escapar de um símbolo de porcentagem permite que ele seja usado como um valor literal, ou para indicar codificação de URL (por exemplo, `\%20`).
\* | Um asterisco permite que o caractere precedente corresponda a zero ou mais vezes.
Space | Normalmente, um caractere de espaço é tratado como um caractere literal.
'valor' | Aspas simples são tratadas como caracteres literais. Um conjunto de aspas simples não tem significado especial.

Condições de correspondência e recursos que suportam expressões regulares aceitam padrões definidos por Expressões Regulares Compatíveis perl (PCRE).

## <a name="next-steps"></a>Próximas etapas

- [Regras condições de correspondência do motor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regras de expressões condicionais do motor](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substitua o comportamento HTTP usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
