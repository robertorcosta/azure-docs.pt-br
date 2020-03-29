---
title: Padrões de design de divulgação
titleSuffix: Azure Cognitive Services
description: Padrões de design e práticas recomendadas para divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776615"
---
# <a name="disclosure-design-patterns"></a>Padrões de design de divulgação
Agora que você&#39;determinou o nível certo [de divulgação](concepts-disclosure-guidelines.md#disclosure-assessment) para sua experiência de voz sintética,&#39;é uma boa hora para explorar padrões potenciais de design.
## <a name="overview"></a>Visão geral
Há um espectro de padrões de design de divulgação que você pode aplicar à sua experiência de voz sintética. Se o resultado de sua avaliação de divulgação foi 'Alta Divulgação', recomendamos [**a divulgação explícita,**](#explicit-disclosure)o que significa comunicar as origens da voz sintética. [**A divulgação implícita**](#implicit-disclosure) inclui pistas e padrões de interação que beneficiam as experiências de voz, sendo ou não necessários níveis de divulgação altos ou baixos.
![Espectro de padrões de divulgação](media/responsible-ai/disclosure-patterns/affordances.png)






| Padrões explícitos de divulgação                                                                                                                                                                                    | Padrões de divulgação implícitos                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introdução Transparente](#transparent-introduction)<br> [Introdução transparente verbal](#verbal-transparent-introduction)<br>  [Byline explícito](#explicit-byline)<br>  [Personalização e Calibração](#customization-and-calibration)<br> [Divulgação parental](#parental-disclosure)<br> [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgação de recursos](#capability-disclosure)<br>[Sugestões implícitas e feedback](#implicit-cues--feedback)<br> [Transparência conversacional](#conversational-transparency) |



Use o gráfico a seguir para se referir diretamente aos padrões aplicáveis à sua voz sintética. Algumas das outras condições deste gráfico também podem se aplicar ao seu cenário:<br/>



| Se sua experiência de voz sintética... | Recomendações | Padrões de design |
| --- | --- | --- |
| Requer alta divulgação  | Use pelo menos um padrão explícito e pistas implícitas na frente para ajudar os usuários a construir associações. |[Divulgação Explícita](#explicit-disclosure)<br>[Divulgação Implícita](#implicit-disclosure)  |
| Requer baixa divulgação | A divulgação pode ser mínima ou desnecessária, mas pode se beneficiar de alguns padrões implícitos. | [Divulgação de recursos](#capability-disclosure)<br>[Transparência conversacional](#conversational-transparency)  |
| Tem um alto nível de engajamento | Construa para o longo prazo e ofereça vários pontos de entrada para divulgação ao longo da jornada do usuário. É altamente recomendável ter uma experiência de onboarding. | [Introdução Transparente](#transparent-introduction)<br>[Personalização e Calibração](#customization-and-calibration)<br>[Divulgação de recursos](#capability-disclosure) |
| Inclui crianças como o público principal | Segmentar os pais como o público principal da divulgação e garantir que eles possam comunicar efetivamente a divulgação às crianças.  | [Divulgação parental](#parental-disclosure)<br>[Introdução transparente verbal](#verbal-transparent-introduction)<br> [Divulgação Implícita](#implicit-disclosure)<br> [Transparência conversacional](#conversational-transparency)  |
| Inclui usuários cegos ou pessoas com baixa visão como o público principal pretendido  | Seja inclusivo de todos os usuários e garanta que qualquer forma de divulgação visual tenha associado efeitos alternativos de texto ou som. Adere aos padrões de acessibilidade para a razão de contraste e o tamanho do display. Use pistas auditivas para comunicar a divulgação.  | [Introdução transparente verbal](#verbal-transparent-introduction) <br>[Pistas auditivas](#implicit-cues--feedback)<br>[Pistas hápticas](#implicit-cues--feedback)<br>[Transparência conversacional](#conversational-transparency)<br>[Padrões de acessibilidade](https://www.microsoft.com/accessibility) |
| É sem tela, sem dispositivo ou usa a voz como o principal ou único modo de interação | Use pistas auditivas para comunicar a divulgação. | [Introdução transparente verbal](#verbal-transparent-introduction) <br> [Pistas auditivas](#implicit-cues--feedback)  |
| Potencialmente inclui vários usuários/ouvintes (por exemplo, assistente pessoal em várias casas)  | Esteja atento a vários contextos e níveis de compreensão do usuário e ofereça múltiplas oportunidades de divulgação na jornada do usuário.  | [Introdução Transparente (Usuário de Retorno)](#transparent-introduction)<br> [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Transparência conversacional](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgação explícita
Se sua experiência de voz sintética requer alta divulgação, é melhor usar pelo menos um dos seguintes padrões explícitos para afirmar claramente a natureza sintética.
### <a name="transparent-introduction"></a>Introdução Transparente

Antes que a experiência de voz comece, apresente o assistente digital sendo totalmente transparente sobre as origens de sua voz e suas capacidades. O momento ideal para usar esse padrão é ao embarcar em um novo usuário ou ao introduzir novos recursos a um usuário que retorna. Implementar pistas implícitas durante uma introdução ajuda os usuários a formar um modelo mental sobre a natureza sintética do agente digital.

#### <a name="first-time-user-experience"></a>Experiência de usuário pela primeira vez

![Introdução transparente durante a experiência de primeira execução](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*A voz sintética é introduzida ao embarcar em um novo usuário.*

Recomendações
- Descreva que a voz é &quot;artificial&quot;(por exemplo, digital)
- Descreva o que o agente é capaz de fazer
- Explicitamente diga a voz&#39;as origens
- Ofereça um ponto de entrada para saber mais sobre a voz sintética

#### <a name="returning-user-experience"></a>Retornando a experiência do usuário

Se um usuário pular a experiência de onboarding, continue a oferecer pontos de entrada para a experiência de Introdução Transparente até que o usuário acione a voz pela primeira vez.
<br/>

![Introdução transparente durante a experiência do usuário de retorno](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Forneça um ponto de entrada consistente para a experiência de voz sintética. Permita que o usuário retorne à experiência de onboarding quando acionar a voz pela primeira vez em qualquer momento da jornada do usuário.*


### <a name="verbal-transparent-introduction"></a>Introdução transparente verbal

Um alerta falado afirmando que as origens da voz do assistente digital&#39;é explícita o suficiente por si só para conseguir a divulgação. Este padrão é melhor para cenários de alta divulgação onde a voz é o único modo de interação disponível.
<br/>

![Introdução transparente falada verbalmente](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Use uma introdução transparente quando houver momentos na experiência do usuário onde você já pode introduzir ou atribuir uma pessoa&#39;voz.*


![Introdução transparente verbalmente falada em primeira pessoa](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Para maior transparência, o dublador pode revelar as origens da voz sintética em primeira pessoa.*

### <a name="explicit-byline"></a>Byline explícito

Use este padrão se o usuário estiver interagindo com um reprodutor de áudio ou componente interativo para acionar a voz.


![Byline explícito em um cenário de mídia de notícias](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Uma linha explícita é a atribuição de onde a voz veio.*

Recomendações

- Ofereça ponto de entrada para saber mais sobre a voz sintetizada

### <a name="customization-and-calibration"></a>Personalização e Calibração

Fornecer aos usuários controle sobre como o assistente digital responde a eles (ou seja, como a voz soa).  Quando um usuário interage com um sistema em seus próprios termos e com objetivos específicos em mente, então, por definição, ele já entendeu que ele não&#39;é uma pessoa real.

#### <a name="user-control"></a>Controle de Usuário

Ofereça opções que tenham um impacto significativo e perceptível na experiência de voz sintética.

![Preferências do usuário](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*As preferências do usuário permitem que os usuários personalizem e melhorem sua experiência.*

Recomendações

- Permitir que os usuários personalizem a voz (por exemplo, selecione o idioma e o tipo de voz)
- Fornecer aos usuários uma maneira de ensinar o sistema a responder à sua voz única (por exemplo, calibração de voz, comandos personalizados)
- Otimizar para interações geradas pelo usuário ou contextuais (por exemplo, lembretes)

#### <a name="persona-customization"></a>Personalização de Persona

Ofereça maneiras de personalizar a voz do assistente digital&#39;. Se a voz for baseada em uma celebridade ou em uma pessoa amplamente reconhecível, considere usar introduções visuais e faladas quando os usuários visualizarem a voz.

![Personalização de voz](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Oferecer a capacidade de selecionar a partir de um conjunto de vozes ajuda a transmitir a natureza artificial.*

Recomendações
- Permitir que os usuários visualizem o som de cada voz
- Use uma introdução autêntica para cada voz
- Ofereça pontos de entrada para saber mais sobre a voz sintetizada

### <a name="parental-disclosure"></a>Divulgação parental

Além de cumprir as normas da COPPA, forneça aos pais se o seu público principal é de crianças pequenas e seu nível de exposição é alto. Para usos sensíveis, considere gating a experiência até que um adulto tenha reconhecido o uso da voz sintética. Incentive os pais a comunicar a mensagem aos seus filhos.

![Divulgação para os pais](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Uma introdução transparente otimizada para os pais garante que um adulto tenha consciência da natureza sintética da voz antes que uma criança interaja com ela.*

Recomendações

- Pais-alvo como o público principal para divulgação
- Encorajar os pais a comunicar a divulgação aos seus filhos
- Ofereça pontos de entrada para saber mais sobre a voz sintetizada
- Gate a experiência, fazendo &quot;&quot; aos pais uma simples pergunta de salvaguarda para mostrar que eles leram a divulgação

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Proporcionando oportunidades para aprender mais sobre como a voz foi feita

Ofereça pontos de entrada sensíveis ao contexto para uma página, pop-up ou site externo que forneça mais informações sobre a tecnologia de voz sintética. Por exemplo, você pode aparecer um link para saber mais durante o onboarding ou quando o usuário solicita mais informações durante a conversação.

![Ponto de entrada para saber mais](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Exemplo de um ponto de entrada para oferecer a oportunidade de aprender mais sobre a voz sintetizada.*

Uma vez que um usuário solicita mais informações sobre a voz sintética, o objetivo principal é educá-los sobre as origens da voz sintética e ser transparente sobre a tecnologia.

![Fornecer aos usuários mais informações sobre voz sintética](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Mais informações podem ser oferecidas em um site de ajuda externo.*

Recomendações

- Simplifique conceitos complexos e evite usar jargões legalese e técnico
- Não enterre este conteúdo em declarações de privacidade e termos de uso
- Mantenha o conteúdo conciso e use imagens quando disponível

## <a name="implicit-disclosure"></a>Divulgação implícita

Consistência é a chave para alcançar a divulgação implicitamente ao longo da jornada do usuário. O uso consistente de pistas visuais e auditivas entre dispositivos e modos de interação pode ajudar a construir associações entre padrões implícitos e divulgação explícita.

![Consistência das pistas implícitas](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Sugestões implícitas & Feedback

O antropomorfismo pode manifestar-se de diferentes formas, desde a representação visual real do agente, até a voz, sons, padrões de luz, formas saltitantes, ou até mesmo a vibração de um dispositivo. Ao definir sua persona, aproveite pistas implícitas e padrões de feedback em vez de apontar para um avatar muito humano. Esta é uma maneira de minimizar a necessidade de uma divulgação mais explícita.

![Sugestões visuais e feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Essas pistas ajudam a antropomorfiar o agente sem ser muito humano. Eles também podem se tornar mecanismos eficazes de divulgação por conta própria quando usados consistentemente ao longo do tempo.*

Considere os diferentes modos de interações de sua experiência ao incorporar os seguintes tipos de dicas:

| Pistas visuais                                                                                                                                                               | Pistas auditivas                                                      | Pistas hápticas |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Sugestões responsivas em tempo real (por exemplo, animações)<br> Pistas não-tela (por exemplo, luzes e padrões em um dispositivo)<br>  | Sonicon (por exemplo, um breve som distinto, série de notas musicais) | Vibração   |

### <a name="capability-disclosure"></a>Divulgação de recursos

A divulgação pode ser alcançada implicitamente estabelecendo expectativas precisas sobre o que o assistente digital é capaz. Forneça comandos de exemplo para que os usuários possam aprender a interagir com o assistente digital e oferecer ajuda contextual para aprender mais sobre a voz sintética durante os estágios iniciais da experiência.

![Sugestões visuais e feedback](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Transparência conversacional

Quando as conversas caem em caminhos inesperados, considere criar respostas padrão que possam ajudar a redefinir expectativas, reforçar a transparência e orientar os usuários para caminhos bem-sucedidos. Há oportunidades para usar divulgação explícita na conversa também.

![Manuseando caminhos inesperados](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Perguntas fora &quot;da&quot; tarefa ou pessoais direcionadas ao agente são um bom momento para lembrar os usuários da natureza sintética do agente e orientá-los a se envolver com ele adequadamente ou redirecioná-los para uma pessoa real.

![Lidar com perguntas de tarefas](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Quando divulgar

Há muitas oportunidades de divulgação ao longo da jornada do usuário. Design para o primeiro uso, segundo uso, enésimo &quot;&quot; uso..., mas também abraçar momentos de falha para destacar a transparência — como quando o sistema comete um erro ou quando o usuário descobre uma limitação dos recursos do agente&#39;.

![Oportunidades de divulgação ao longo de uma jornada do usuário](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Exemplo de uma jornada padrão do usuário assistente digital destacando várias oportunidades de divulgação.

### <a name="up-front"></a>Na frente

O momento ideal para a divulgação é a primeira vez que uma pessoa interage com a voz sintética.Em um cenário de assistente de voz pessoal, isso seria durante o onboarding, ou a primeira vez que o usuário praticamente desencaixe a experiência. Em outros cenários, pode ser a primeira vez que uma voz sintética lê conteúdo em um site ou a primeira vez que um usuário interage com um personagem virtual.

- [Introdução Transparente](#transparent-introduction)
- [Divulgação de recursos](#capability-disclosure)
- [Personalização e Calibração](#customization-and-calibration)
- [Sugestões Implícitas](#implicit-cues--feedback)

### <a name="upon-request"></a>Mediante solicitação

Os usuários devem ser capazes de acessar facilmente informações adicionais, controlar preferências e receber comunicação transparente a qualquer momento durante a jornada do usuário quando solicitado.

- [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalização e Calibração](#customization-and-calibration)
- [Transparência conversacional](#conversational-transparency)

### <a name="continuously"></a>Continuamente

Use os padrões de design implícitos que melhoram a experiência do usuário continuamente.

- [Divulgação de recursos](#capability-disclosure)
- [Sugestões Implícitas](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Quando o sistema falha

Use a divulgação como uma oportunidade para falhar graciosamente.

- [Transparência conversacional](#conversational-transparency)
- [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Entrega para humanos](#conversational-transparency)



## <a name="additional-resources"></a>Recursos adicionais
- [Diretrizes do Microsoft Bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Diretrizes de Design Cortana](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Diretrizes de design de fala do Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Diretrizes de comando de voz de realidade mista do Microsoft Windows](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Documentos de Referência

* [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Diretrizes para implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Visão geral de Gating](concepts-gating-overview.md)
* [Como divulgar](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Próximas etapas

* [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent)
