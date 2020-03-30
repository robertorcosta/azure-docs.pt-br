---
title: Incidentes no Azure Security Center - correlações inteligentes de alertas
description: Este tópico explica como a fusion usa correlação de alerta inteligente em nuvem para gerar incidentes de segurança no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686480"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alerta inteligente na nuvem no Azure Security Center (incidentes)

O Azure Security Center analisa continuamente as cargas de trabalho híbridas em nuvem usando análises avançadas e inteligência de ameaças para alertá-lo sobre atividades maliciosas.

A amplitude da cobertura de ameaças está crescendo. A necessidade de detectar até mesmo o menor compromisso é importante, e pode ser um desafio para os analistas de segurança triagem dos diferentes alertas e identificar um ataque real. O Security Center ajuda os analistas a lidar com essa fadiga de alerta. Ele ajuda a diagnosticar ataques à medida que ocorrem, correlacionando diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

A análise de fusão é a tecnologia e o back-end analítico que alimenta os incidentes do Security Center, permitindo que ele correlaciona diferentes alertas e sinais contextuais juntos. Fusion analisa os diferentes sinais relatados em uma assinatura através dos recursos. Fusion encontra padrões que revelam progressão de ataque ou sinais com informações contextuais compartilhadas, indicando que você deve usar um procedimento de resposta unificado para eles.

A análise de fusão combina o conhecimento do domínio de segurança com a IA para analisar alertas, descobrindo novos padrões de ataque à medida que ocorrem. 

O Security Center aproveita a Matriz de Ataque MITRE para associar alertas com suas intenções percebidas, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando as informações coletadas para cada passo de um ataque, o Security Center pode descartar atividades que parecem ser etapas de um ataque, mas na verdade não é.

Como os ataques ocorrem frequentemente em diferentes inquilinos, o Security Center pode combinar algoritmos de IA para analisar seqüências de ataque que são relatadas em cada assinatura. Esta técnica identifica as seqüências de ataque como padrões de alerta prevalentes, em vez de apenas serem incidentalmente associadas umas com as outras.

Durante uma investigação de um incidente, os analistas muitas vezes precisam de um contexto extra para chegar a um veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detectada, sem entender o que mais está acontecendo na rede ou no que diz respeito ao recurso direcionado, é difícil entender quais ações tomar a seguir. Para ajudar, um incidente de segurança pode incluir artefatos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam, dependendo do tipo de ameaça detectada e da configuração do seu ambiente. 

![Captura de tela do incidente de segurança detectado relatório](./media/security-center-alerts-cloud-smart/security-incident.png)

Para entender melhor os incidentes de segurança, consulte [Como lidar com incidentes de segurança no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

