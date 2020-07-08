---
title: Referência do mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 6260a4b78197329e020bebaa3bc08db5ad792086
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559305"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referência do mecanismo de regras Standard para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência e recursos que estão disponíveis no mecanismo de regras padrão para a CDN do Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN do Azure padrão.

**Usos comuns para as regras**:

- Substituir ou definir uma política de cache personalizada.
- Solicitações de redirecionamento.
- Modificar cabeçalhos de solicitação e resposta HTTP.

## <a name="terminology"></a>Terminologia

Para definir uma regra no mecanismo de regras, defina [condições de correspondência](cdn-standard-rules-engine-match-conditions.md) e [ações](cdn-standard-rules-engine-actions.md):

 ![Estrutura de regras da CDN do Azure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até dez condições de correspondência e cinco ações. Cada ponto de extremidade da CDN do Azure pode ter até 25 regras. 

O incluído nesse limite é uma *regra global*padrão. A regra global não tem condições de correspondência; as ações definidas em uma regra global sempre são disparadas.

## <a name="limits-and-pricing"></a>Limites e preços 

Cada ponto de extremidade da CDN do Azure pode ter até 25 regras. Cada regra pode ter até dez condições de correspondência e cinco ações. O preço do mecanismo de regras segue as dimensões abaixo: 
- Regras: $1 por regra por mês 
- Solicitações processadas: $0.60 por milhão de requets
- As primeiras 5 regras permanecerão livres

## <a name="syntax"></a>Syntax

A forma como os caracteres especiais são tratados em uma regra varia de acordo com a forma como as condições de correspondência e as ações diferentes manipulam valores de texto. Uma condição de correspondência ou ação pode interpretar o texto de uma das seguintes maneiras:

- [Valores literais](#literal-values)
- [Valores de caractere curinga](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais *, exceto o símbolo%* , como parte do valor que deve ser correspondido em uma regra. Por exemplo, uma condição de correspondência literal definida como `'*'` é satisfeita somente quando o valor exato `'*'` é encontrado.

Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores de caractere curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como os caracteres especiais específicos são interpretados no mecanismo de regras padrão:

Caractere | Descrição
----------|------------
\ | Uma barra invertida é usada para funcionar como escape para qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape. Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20` ).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
espaço | Um caractere de espaço indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
aspas simples | Uma aspa simples não tem significado especial. No entanto, um conjunto de aspas simples indica que um valor deve ser tratado como um valor literal. Aspas simples podem ser usadas das seguintes maneiras:<ul><li>Para permitir que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>Para permitir que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere de espaço literal ao colocar um caractere de espaço em um conjunto de aspas simples ( `' '` ou `'<sample value>'` ).</li><li>Para permitir que um valor em branco seja especificado. Especifique um valor em branco especificando um conjunto de aspas simples (**' '**).</li></ul>**Importante**:<br /><ul><li>Se o valor especificado não contiver um caractere curinga, o valor será automaticamente considerado um valor literal. Você não precisa especificar um conjunto de aspas simples para um valor literal.</li><li>Se uma barra invertida não for usada para escapar de outro caractere nesta tabela, a barra invertida será ignorada quando for especificada em um conjunto de aspas simples.</li><li>Outra maneira de especificar um caractere especial como um caractere literal é fazer o escape dele usando uma barra invertida ( `\` ).</li></ul>

## <a name="next-steps"></a>Próximas etapas

- [Condições de correspondência do mecanismo de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
