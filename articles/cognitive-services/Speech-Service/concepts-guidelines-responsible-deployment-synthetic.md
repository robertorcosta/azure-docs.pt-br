---
title: Diretrizes para implantação responsável de tecnologia de voz sintética
titleSuffix: Azure Cognitive Services
description: As diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft realizou com talento de voz, consumidores, bem como indivíduos com distúrbios de fala para orientar o desenvolvimento responsável da voz sintética.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836773"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Diretrizes para implantação responsável de tecnologia de voz sintética
Aqui estão as diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft realizou com talentos de voz, consumidores, bem como indivíduos com distúrbios de fala para orientar o desenvolvimento responsável da voz sintética.

## <a name="general-considerations"></a>Considerações gerais
Para a implantação da tecnologia de fala sintética, as seguintes diretrizes se aplicam na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulgar quando a voz é sintética
Divulgar que uma voz é gerada por computador não só minimiza o risco de desfechos prejudiciais de engano, mas também aumenta a confiança na organização que entrega a voz. Saiba mais sobre [como divulgar](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione tipos de voz apropriados para o seu cenário
Considere cuidadosamente o contexto de uso e os potenciais danos associados ao uso de voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser apropriadas em cenários de alto risco, como mensagens pessoais, transações financeiras ou situações complexas que requerem adaptabilidade humana ou empatia. Os usuários também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir notícias sensíveis sendo lidas por uma voz sintética, alguns usuários preferem uma leitura mais empática e humana das notícias, enquanto outros preferem uma voz mais monótona e imparcial. Considere testar seu aplicativo para entender melhor as preferências do usuário.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre capacidades e limitações
Os usuários são mais propensos a ter expectativas mais altas ao interagir com agentes de voz sintéticos de alta fidelidade. Consequentemente, quando as capacidades do sistema não atendem a essas expectativas, a confiança pode sofrer, podendo resultar em experiências desagradáveis ou até mesmo prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer suporte humano opcional
Em cenários ambíguos e transacionais (por exemplo, um centro de suporte de chamadas), os usuários nem sempre confiam em um agente de computador para responder adequadamente às suas solicitações. O apoio humano pode ser necessário nessas situações, independentemente da qualidade realista da voz ou capacidade do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações para o talento da voz
Ao trabalhar com o talento da voz, como dubladores, para criar vozes sintéticas, aplica-se a diretriz abaixo.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha consentimento significativo do talento de voz
O talento de voz espera ter controle sobre sua fonte de voz (como e onde ela será usada) e ser compensado sempre que for usado. Os proprietários do sistema devem, portanto, obter permissão por escrito explícita de talentos de voz, e ter especificações contratuais claras sobre casos de uso, duração de uso, compensação e assim por diante. Alguns talentos de voz desconhecem os potenciais usos maliciosos da tecnologia e devem ser educados pelos proprietários do sistema sobre as capacidades da tecnologia. Para mais sobre talento de voz e consentimento, leia nossa [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com distúrbios de fala
Ao trabalhar com indivíduos com distúrbios de fala, para criar ou implantar tecnologia de voz sintética, as seguintes diretrizes se aplicam.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer diretrizes para estabelecer contratos
Fornecer diretrizes para estabelecer contratos com indivíduos que usam voz sintética para assistência na fala. O contrato deve considerar especificar as partes que possuem a voz, duração do uso, critérios de transferência de propriedade, procedimentos para excluir a fonte de voz e como impedir o acesso não autorizado. Além disso, permitir a transferência contratual da propriedade da fonte de voz após a morte para os membros da família, se essa pessoa tiver dado permissão.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Contabilize inconsistências nos padrões de fala
Para indivíduos com distúrbios de fala que registram suas próprias fontes de voz, inconsistências em seu padrão de fala (chorão ou incapacidade de pronunciar determinadas palavras) podem complicar o processo de gravação. Nestes casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (ou seja, fornecer pausas e número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificação ao longo do tempo
Indivíduos com distúrbios da fala desejam fazer atualizações em sua voz sintética para refletir o envelhecimento (por exemplo, uma criança chegando à puberdade). Os indivíduos também podem ter preferências estilísticas que mudam com o tempo, e podem querer fazer alterações no tom, sotaque ou outras características de voz.


## <a name="reference-docs"></a>Documentos de Referência

* [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Visão geral de Gating](concepts-gating-overview.md)
* [Como divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Próximas etapas

* [Divulgação para O Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Como divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
