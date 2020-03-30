---
title: Modelos de Tokens blockchain do Azure
description: Os modelos de Tokens de Blockchain do Azure são modelos padronizados e reutilizáveis que simplificam a criação e a implantação de tokens baseados em livros.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252201"
---
# <a name="azure-blockchain-tokens-templates"></a>Modelos de Tokens blockchain do Azure

[!INCLUDE [Preview note](./includes/preview.md)]

Um modelo de Tokens blockchain do Azure é um modelo padronizado e reutilizável que simplifica a criação e a implantação de tokens baseados em livro.. Um modelo consiste em uma fórmula, que é baseada na gramática [Token Taxonomy Framework (TTF).](overview.md#token-taxonomy-framework) A gramática abrange o tipo de token base e o conjunto de comportamentos para o token.  

Por exemplo, o modelo de token **τ(d,m,b,r}** descreve um token base fungível que é subdividable, mintable, burnable, e tem suporte a função.
  
## <a name="base-token-types"></a>Tipos de token base

Ao definir e criar o token baseado em livro-razão para o seu ativo específico, é importante considerar qual token base usar.

### <a name="fungible"></a>Fungível

Os tokens fungíveis (τF) têm valor intercambiável entre si, desde que estejam na mesma classe ou série. Um token tem o mesmo valor que outro token ou uma determinada quantidade de tokens tem o mesmo valor que outra quantidade igual. Por exemplo, um dólar é um token fungível. Se duas pessoas estão segurando uma nota de dólar, eles podem trocar essas notas de dólar sem conseqüências. As notas de dólar têm o mesmo valor. 

### <a name="non-fungible"></a>Não-Fungável

Os tokens não fungíveis (τN) não são intercambiáveis com outros tokens do mesmo tipo, pois normalmente têm valores diferentes. Por exemplo, um título de propriedade é um token não fungível. Títulos de imóveis para dois apartamentos diferentes em um complexo de apartamentos não são necessariamente de igual valor, devido à localização da unidade ou em que andar a unidade está. O valor percebido dos dois tokens de título de propriedade não são iguais.

### <a name="hybrid"></a>Híbrido

Os tokens híbridos são tokens que possuem componentes de tokens fungíveis e tokens não fungíveis. Um token híbrido é um tipo de token base que possui uma classe do outro tipo de token.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base híbrida não fungível com segmentos fungíveis

Uma base híbrida não fungível com token de segmentos fungíveis tem uma base não fungível com segmentos de token fungível.
Por exemplo, um ingresso de concerto é um token híbrido onde a data e a hora do concerto é o token base não fungível. Os ingressos em várias seções de assentos para o concerto são os segmentos com tokens fungíveis. Os ingressos são trocados em suas seções individuais, mas não em seções.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fungível híbrida com segmentos não fungíveis

Uma base fungível híbrida com um token de segmentos não fungíveis tem uma base fungível com segmentos de token não fungíveis. Por exemplo, uma segurança lastreada em hipotecas é um token híbrido onde vários proprietários são a base fungível que é dividida entre muitos proprietários. A segurança é intercambiável. As hipotecas individuais são os segmentos não fungíveis que representam a garantia hipotecária específica.

## <a name="token-behaviors"></a>Comportamentos de token

Um comportamento de token define recursos ou restrições do token. O comportamento inclui propriedades de suporte que fazem parte da definição do token. Os comportamentos podem ser aplicados em todos os tipos de tokens ou apenas um. Os comportamentos podem ser internos ou externos, dependendo dos efeitos comportamentais. Um comportamento interno permite ou restringe propriedades no próprio token. Um comportamento externo permite ou restringe a invocação do comportamento de um ator externo.

Para obter mais informações sobre os comportamentos de tokens do Azure Blockchain Token supported Token Taxonomy Framework (TTF), consulte [a composabilidade do token](composability.md).

## <a name="pre-built-token-templates"></a>Modelos de token pré-construídos

O Azure Blockchain Tokens fornece quatro modelos de token sem modificações pré-construídos que podem ser usados sem modificações. Você pode chamar esses modelos pré-construídos para a maioria dos casos de uso para começar a criar, implantar e gerenciar seus tokens rapidamente.

### <a name="commodity-tokens"></a>Tokens de commodities

Os tokens de commodities têm valor consistente e são transferíveis. Por exemplo, um barril de petróleo ou uma unidade de energia.

**τF{~d,t,m,b,r}** - fungível, inteiro, transferível, minável, queimável e ter suporte de função

Muitos cenários blockchain exigem transparência e visibilidade em toda a cadeia de suprimentos ou várias organizações. Os tokens de commodities são baseados nesses casos de uso comum. Os tokens são intercambiáveis e consistentes. O modelo de token de mercadoria é flexível e personalizável com metadados.

### <a name="qualified-tokens"></a>Tokens qualificados

Os tokens qualificados representam algo ganho e geralmente estão associados a uma entidade e não podem ser transferidos. Por exemplo, um diploma ou uma violação de estacionamento.

**τN{s,~t}** - não fungível, singleton e intransferível

Vários cenários de auditoria e atestado exigem que a propriedade do token não possa ser alterada. Há um conjunto de casos de uso, que têm a necessidade de fornecer um token qualificado se a associação é boa ou ruim.

### <a name="asset-tokens"></a>Tokens de ativos

Os tokens de ativos têm valor único dependente do item e não são commodities. Por exemplo, um artefato de museu ou um título de propriedade.

**τN{s,t}** - não fungível, singleton e transferível

Os tokens de ativos podem ser confundidos com tokens de commodities. A grande diferença entre os dois tokens é que os tokens de ativos são inerentemente únicos, e o valor é independente do tipo de token que é. Por exemplo, uma obra de arte como uma pintura a óleo de um artista estabelecido é um token patrimonial. No entanto, uma impressão artística da Mona Lisa é considerada um token de mercadoria. Da mesma forma, um título de propriedade é um token de ativo, uma vez que o valor existe nas qualidades subjetivas da propriedade.

### <a name="ticket-tokens"></a>Tokens de bilhetes

Os tokens de bilhetes têm um valor consistente, mas normalmente expiram. Por exemplo, uma passagem de avião.

**τN{m,b,r}** - não fungível, minável, queimável e tem suporte de função.

Os tokens de bilhetes normalmente têm uma data de validade que os torna diferentes de um token de mercadoria regular. Por exemplo, uma passagem de avião, bilhete de concerto ou bilhete esportivo têm opções de assentos atribuídos com datas específicas de uso. Você não pode facilmente trocar bilhetes entre datas ou áreas de estar.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais flexibilidade para o seu cenário, aprenda a criar seus próprios modelos de token usando [a composability de token](composability.md).
