---
title: Diretrizes de design de divulgação
titleSuffix: Azure Cognitive Services
description: Introdução às diretrizes de design de divulgação e avaliação do nível de divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776378"
---
# <a name="disclosure-design-guidelines"></a>Diretrizes de design de divulgação
Aprenda a construir e manter a confiança com os clientes sendo transparente sobre a natureza sintética de sua experiência de voz.

## <a name="what-is-disclosure"></a>O que é divulgação?

A divulgação é um meio de deixar as pessoas saberem que&#39;estar interagindo ou ouvindo uma voz que é gerada sinteticamente.

## <a name="why-is-disclosure-necessary"></a>Por que a divulgação é necessária?

A necessidade de divulgar as origens sintéticas de uma voz gerada por computador é relativamente nova. No passado, as vozes geradas por computador eram obviamente isso — ninguém jamais as confundiria com uma pessoa real. A cada dia, no entanto, o realismo das vozes sintéticas melhora, e elas se tornam cada vez mais indistinguíveis das vozes humanas.

## <a name="goals"></a>Metas
Estes são os princípios a ter em mente ao projetar experiências de voz sintéticas:

**Reforce a confiança**
<br>Design com a intenção de falhar no Teste de Turing sem degradar a experiência. Deixe os usuários entrarem no fato de que eles estão interagindo com uma voz sintética, permitindo que eles se envolvam perfeitamente com a experiência.

**Adapte-se ao contexto de uso**
<br>Entenda quando, onde e como seus usuários interagirão com a voz sintética para fornecer o tipo certo de divulgação no momento certo.

**Definir expectativas claras**
<br>Permitir que os usuários descubram e entendam facilmente as capacidades do agente. Ofereça oportunidades para aprender mais sobre tecnologia de voz sintética mediante solicitação.

**Abrace o fracasso**
<br>Use momentos de falha para reforçar as capacidades do agente.

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia ajuda você a determinar quais padrões de divulgação são mais adequados para sua experiência de voz sintética. Em seguida, oferecemos exemplos de como e quando usá-los. Cada um desses padrões é projetado para maximizar a transparência com os usuários sobre a fala sintética, mantendo-se fiel ao design centrado no homem.

Considerando o vasto corpo de orientação de design sobre experiências de voz, focamos aqui especificamente em:

1. [**Avaliação de divulgação**](#disclosure-assessment): Um processo para determinar o tipo de divulgação recomendada para sua experiência de voz sintética

2. [**Como divulgar**](concepts-disclosure-patterns.md): Exemplos de padrões de divulgação que podem ser aplicados à sua experiência de voz sintética

3. [**Quando divulgar**](concepts-disclosure-patterns.md#when-to-disclose): Momentos ideais para divulgar ao longo da jornada do usuário

## <a name="disclosure-assessment"></a>Avaliação de divulgação
Considere seus usuários&#39; expectativas sobre uma interação e o contexto em que eles experimentarão a voz. Se o contexto deixar claro que &quot;uma&quot; voz sintética está falando, a divulgação pode ser mínima, momentânea ou até mesmo desnecessária. Os principais tipos de contexto que afetam a divulgação incluem tipo de persona, tipo de cenário e nível de exposição. Também ajuda a considerar quem pode estar ouvindo.

### <a name="understand-context"></a>Entenda o contexto

Use esta planilha para determinar o contexto de sua experiência de voz sintética. Você aplicará isso na próxima etapa onde determinará seu nível de divulgação.

|                                    | Contexto de uso                                                                                                                                                                                                                                                                                                                                                       | Desafios potenciais & riscos                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Tipo persona**               | **Se alguma das seguintes se aplicar, sua persona se encaixa na categoria 'Persona humana':**<br><br><ul><li> Persona encarna um humano real, quer seja uma representação fictícia ou não. (por exemplo, fotografia ou uma renderização gerada por computador de uma pessoa real)<br><br><li> A voz sintética é baseada na voz de uma pessoa real amplamente reconhecível (por exemplo, celebridade, figura política) | Quanto mais representações humanas você dá à sua persona, mais provável é que um usuário a associe a uma pessoa real, ou faça com que acredite que o conteúdo é falado por uma pessoa real em vez de gerado por computador. </ul>                                                                                                                                                                      |
| **2. Tipo de cenário**            | **Se alguma das seguintes se aplicar, sua experiência de voz se encaixa na categoria 'Sensível':**<br><br><ul><li> Obtém ou exibe informações pessoais do usuário <br><br> <li> Transmite notícias/informações confidenciais de tempo (por exemplo, alerta de emergência)<br><br><li> Tem como objetivo ajudar pessoas reais a se comunicarem entre si (por exemplo, lê e-mails/textos pessoais)<br><br> <li> Presta assistência médica/saúde </ul>            | O uso de voz sintética pode não parecer apropriado ou confiável para as pessoas que a utilizam quando os tópicos estão relacionados a assuntos sensíveis, pessoais ou urgentes. Eles também podem esperar o mesmo nível de empatia e consciência contextual como um humano real. |
| **3. Nível de exposição** |**Sua experiência de voz provavelmente se encaixa na categoria 'Alto' se:** <br><br><ul><li>O usuário ouvirá ou interagirá com a voz sintética com freqüência ou por uma longa duração </ul>                                                                                                                                                                             | A importância de ser transparente e construir confiança com os usuários é ainda maior ao estabelecer relacionamentos de longo prazo.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determine o nível de divulgação

Use o diagrama a seguir para determinar se sua experiência de voz sintética requer alta ou baixa divulgação com base no seu contexto de uso.

  ![Diagrama de avaliação de divulgação](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Documentos de Referência

* [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Diretrizes para implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Visão geral de Gating](concepts-gating-overview.md)

## <a name="next-steps"></a>Próximas etapas

* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
