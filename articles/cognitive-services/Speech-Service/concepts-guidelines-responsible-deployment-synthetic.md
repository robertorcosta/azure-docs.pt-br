---
title: Diretrizes para a implantação responsável da tecnologia de voz sintética
titleSuffix: Azure Cognitive Services
description: Diretrizes de design geral da Microsoft para o uso da tecnologia de voz sintética. Elas foram desenvolvidas em estudos que a Microsoft realizou com talento de voz, consumidores, bem como indivíduos com fala autoimunes para guiar o desenvolvimento responsável da voz sintética.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 715c09ef65358b21e78cfde204b4819db0c7875d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428412"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Diretrizes para a implantação responsável da tecnologia de voz sintética

## <a name="general-considerations-to-keep-in-mind-when-implementing-ai-systems"></a>Considerações gerais para ter em mente ao implementar sistemas de ia 

Este artigo aborda especificamente a fala sintética e a voz neural personalizada e as principais considerações para tornar o uso dessa tecnologia com responsabilidade. Em geral, no entanto, há várias coisas que você precisa considerar cuidadosamente ao decidir como usar e implementar produtos e recursos com o ia: 

* Este produto ou recurso terá um bom desempenho no meu cenário? Antes de implantar o ia em seu cenário, teste como ele é executado usando dados reais e certifique-se de que ele possa fornecer a exatidão de que você precisa. 
* Estamos equipados para identificar e responder a erros? Os produtos e recursos baseados em ia nem sempre serão de 100% precisos, portanto, considere como você identificará e responderá a quaisquer erros que possam ocorrer. 

## <a name="general-guidelines-for-using-synthetic-voice-technology"></a>Diretrizes gerais para o uso da tecnologia de voz sintética 
Aqui estão as diretrizes de design geral da Microsoft para o uso da tecnologia de voz sintética. Elas foram desenvolvidas em estudos que a Microsoft realizou com talento de voz, consumidores, bem como indivíduos com autoimunes de fala para guiar o desenvolvimento responsável da voz sintética.

Para a implantação da tecnologia de fala sintética, as diretrizes a seguir se aplicam na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulgar quando a voz for sintética
A divulgação de uma voz é gerada pelo computador não apenas minimiza o risco de resultados prejudiciais de deconcepção, mas também aumenta a confiança na organização que fornece a voz. Saiba mais sobre [como divulgar](concepts-disclosure-guidelines.md).

A Microsoft exige que seus clientes divulguem a natureza sintética da voz neural personalizada para seus usuários. 
* Certifique-se de fornecer uma divulgação adequada para as audiências, especialmente ao usar a voz de uma pessoa bem conhecida-as pessoas fazem seu julgamento sobre informações com base na pessoa que a entrega, seja de forma consciente ou inconsciente.  Por exemplo, a divulgação pode ser compartilhada verbalmente no início de uma difusão. Para obter mais informações, visite os [padrões de divulgação](concepts-disclosure-patterns.md).   
* Considere a divulgação adequada para os pais ou outras partes com casos de uso projetados para pequenos e filhos – se o seu caso de uso for destinado a pequenos ou filhos, você precisará garantir que os pais ou tutores jurídicos sejam capazes de entender a divulgação sobre o uso de mídia sintética e tomar a decisão certa para os pequenos ou filhos se usarem a experiência. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione os tipos de voz apropriados para seu cenário
Considere cuidadosamente o contexto de uso e os possíveis danos associados ao uso de voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser apropriadas em cenários de alto risco, como para mensagens pessoais, transações financeiras ou situações complexas que exigem adaptação humana ou empatia. Os usuários também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao escutar notícias confidenciais sendo lidas por uma voz sintética, alguns usuários preferem uma leitura mais empática e humana das notícias, enquanto outros preferiram uma voz mais monotônico e sem tendência. Considere testar seu aplicativo para entender melhor as preferências do usuário.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre recursos e limitações
É mais provável que os usuários tenham mais expectativas ao interagir com agentes de voz sintéticos de alta fidelidade. Consequentemente, quando os recursos do sistema não atendem a essas expectativas, a confiança pode sofrer e pode resultar em experiências desagradáveis ou ainda prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer suporte humano opcional
Em cenários ambíguos e transacionais (por exemplo, um Call Center de suporte), os usuários nem sempre confiam em um agente de computador para responder adequadamente às suas solicitações. O suporte humano pode ser necessário nessas situações, independentemente da qualidade realista da voz ou do recurso do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações sobre talento de voz
Ao trabalhar com talento de voz, como atores de voz, para criar vozes sintéticas, a diretriz abaixo se aplica.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha um consentimento significativo do talento de voz
Os talentos de voz devem ter controle sobre seu modelo de voz (como e onde serão usados) e ser compensado por seu uso. A Microsoft exige que os clientes de voz personalizados obtenham permissão explícita por escrito de seus talentos de voz para criar uma voz sintética e seu contrato com os talentos de voz contemplarndo a duração, o uso e as limitações de conteúdo.  Se você estiver criando uma voz sintética de uma pessoa bem conhecida, deverá fornecer uma maneira para a pessoa por trás da voz para editar ou aprovar o conteúdo.

Alguns talentos de voz não sabem dos possíveis usos mal-intencionados da tecnologia e devem ser instruídos por proprietários do sistema sobre os recursos da tecnologia. A Microsoft exige que os clientes compartilhem a divulgação da Microsoft [para talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent) com talento de voz diretamente ou por meio do representante autorizado de talentos de voz que descreve como as vozes sintéticas são desenvolvidas e operam em conjunto com os serviços de texto para fala.

## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com autoimunes de fala
Ao trabalhar com indivíduos com o Speech autoimunes, para criar ou implantar a tecnologia de voz sintética, as diretrizes a seguir se aplicam.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer diretrizes para estabelecer contratos
Forneça diretrizes para estabelecer contratos com indivíduos que usam voz sintética para assistência em termos de fala. O contrato deve considerar a especificação das partes que possuem a voz, a duração de uso, os critérios de transferência de propriedade, os procedimentos para excluir a fonte de voz e como impedir o acesso não autorizado. Além disso, habilite a transferência contratuais da propriedade da fonte de voz após a morte para os membros da família se essa pessoa tiver concedido permissão.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Considerar inconsistências em padrões de fala
Para pessoas com autoimunes de fala que registram suas próprias fontes de voz, inconsistências no seu padrão de fala (slurring ou incapacidade de pronunciar determinadas palavras) podem complicar o processo de gravação. Nesses casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (ou seja, fornecer quebras e número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificação ao longo do tempo
Indivíduos com autoimunes de fala para fazer atualizações em sua voz sintética para refletir a expiração (por exemplo, um filho que atinja Puberty). Os indivíduos também podem ter preferências estilísticos que mudam ao longo do tempo e talvez queiram fazer alterações de Tom, acentuação ou outras características de voz.


## <a name="reference-docs"></a>Documentos de Referência

* [Divulgação para talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Visão geral da retenção](concepts-gating-overview.md)
* [Como divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Próximas etapas

* [Divulgação para talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Como divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
