---
title: Referência padrão do motor de regras para Azure CDN | Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no mecanismo de regras padrão para a Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259894"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referência do mecanismo de regras Standard para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a Azure Content Delivery Network (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência e recursos disponíveis no mecanismo de regras padrão para CDN Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pelo CDN Standard Azure.

**Usos comuns para as regras:**

- Substituir ou definir uma política de cache personalizada.
- Solicitações de redirecionamento.
- Modifique os cabeçalhos de solicitação e resposta HTTP.

## <a name="terminology"></a>Terminologia

Para definir uma regra no mecanismo de regras, defina [condições](cdn-standard-rules-engine-match-conditions.md) de correspondência e [ações:](cdn-standard-rules-engine-actions.md)

 ![Azure CDN rege estrutura](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até quatro condições de jogo e três ações. Cada ponto final do CDN do Azure pode ter até cinco regras. 

Incluído no limite atual de cinco regras para um ponto final do CDN Do Azure é uma *regra global*padrão . A regra global não tem condições de correspondência, e ações que são definidas em uma regra global sempre disparam.

## <a name="syntax"></a>Sintaxe

A forma como caracteres especiais são tratados em uma regra varia de acordo com a forma como diferentes condições de correspondência e ações lidam com valores de texto. Uma condição de correspondência ou ação pode interpretar o texto de uma das seguintes maneiras:

- [Valores literais](#literal-values)
- [Valores curinga](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto interpretado como um valor literal trata todos os caracteres *especiais, exceto o símbolo %* como parte do valor que deve ser correspondido em uma regra. Por exemplo, uma condição `'*'` de correspondência literal definida `'*'` para é satisfeita somente quando o valor exato é encontrado.

Um sinal por cento é usado para `%20`indicar codificação de URL (por exemplo, ).

### <a name="wildcard-values"></a>Valores de caractere curinga

Texto que é interpretado como um valor curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como caracteres especiais específicos são interpretados no mecanismo de regras padrão:

Caractere | Descrição
----------|------------
\ | Uma barra invertida é usada para funcionar como escape para qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape. Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | Um sinal por cento é usado para `%20`indicar codificação de URL (por exemplo, ).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
espaço | Um caractere espacial indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
aspas únicas | Uma única marca de cotação não tem significado especial. No entanto, um conjunto de aspas simples indica que um valor deve ser tratado como um valor literal. Aspas simples podem ser usadas das seguintes maneiras:<ul><li>Para permitir que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>Para permitir que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere espaço literal envolvendo um`' '` `'<sample value>'`caractere espacial em um conjunto de aspas simples (ou ).</li><li>Para permitir que um valor em branco seja especificado. Especifique um valor em branco especificando um conjunto de aspas simples **(').**</li></ul>**Importante**:<br /><ul><li>Se o valor especificado não contiver um curinga, o valor será automaticamente considerado um valor literal. Você não precisa especificar um conjunto de aspas simples para um valor literal.</li><li>Se uma barra invertida não for usada para escapar de outro caractere nesta tabela, a barra invertida será ignorada quando especificada em um conjunto de aspas simples.</li><li>Outra maneira de especificar um caractere especial como um caractere`\`literal é escapar dele usando uma barra invertida ().</li></ul>

## <a name="next-steps"></a>Próximas etapas

- [Condições de correspondência no motor de regras Padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras Padrão](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
