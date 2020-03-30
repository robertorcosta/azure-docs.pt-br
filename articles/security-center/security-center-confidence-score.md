---
title: Pontuação de confiança na Central de Segurança do Azure | Microsoft Docs
description: O Security Center gera pontuações de confiança para ajudar sua equipe a determinar se uma ameaça é legítima e como triagem e priorizar alertas.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604374"
---
# <a name="alert-confidence-score-preview"></a>Pontuação de confiança de alerta (Visualização)

A Central de Segurança do Azure fornece visibilidade dos recursos que você executa no Azure e emite alertas quando detecta possíveis problemas. Com o aumento do volume de alertas, tratar os alertas individualmente pode ser um desafio para a equipe de operações de segurança. Nesse caso, torna-se necessário priorizar quais alertas devem ser investigados. A investigação dos alertas pode ser complexa e demorada, assim, como resultado, alguns alertas podem ser ignorados.

A pontuação de confiança (atualmente em pré-visualização) no Security Center pode ajudar sua equipe a triagem e priorizar alertas. A Central de Segurança aplica automaticamente as práticas recomendadas do setor, algoritmos inteligentes e processos usados por analistas para determinar se uma ameaça é legítima e fornece insights significativos na forma de uma pontuação de confiança.

## <a name="how-the-confidence-score-is-triggered"></a>Como a pontuação de confiança é disparada

Os alertas são gerados quando processos suspeitos são detectados em execução em suas máquinas virtuais. A Central de Segurança examina e analisa esses alertas nas máquinas virtuais do Windows em execução no Azure. Ela executa verificações e correlações automatizadas usando algoritmos avançados em várias entidades e fontes de dados em toda a organização e em todos os recursos do Azure e apresenta uma pontuação de confiança, que é uma medida de quanto a Central de Segurança confia que o alerta é original e precisa ser investigado.

## <a name="understanding-the-confidence-score"></a>Noções básicas sobre a pontuação de confiança

A pontuação de confiança varia entre 1 e 100 e representa a confiança que a Central de Segurança tem de que o alerta precisa ser investigado. Quanto maior é a pontuação, mais a Central de Segurança confia que o alerta indica atividade mal-intencionada original. A pontuação de confiança inclui uma lista das principais razões pelas quais o alerta recebeu sua pontuação de confiança. A pontuação de confiança facilita para que os analistas de segurança priorizem suas respostas a alertas e lidem com os ataques mais urgentes primeiro, resultando na redução do tempo necessário para responder a ataques e a violações.

Para exibir a pontuação de confiança:
- No portal da Central de Segurança, abra a folha Alertas de segurança.
-  Os alertas e incidentes são organizados do mais alto para o mais baixo, o que significa que quanto mais a Central de Segurança confia que um alerta representa uma ameaça, mais próximo ele está do topo da página. 


 ![Pontuação de confiança][1]

Para exibir os dados que contribuíram para a confiança da Central de Segurança em um alerta:
- Na folha Alerta de segurança, em **Confiança**, exiba as observações que contribuíram para a pontuação de confiança e obtenha insights relacionados ao alerta. Isso fornece mais insights sobre a natureza das atividades que causaram o alerta.

  ![Pontuação de confiança suspeita][2]

Use a pontuação de confiança da Central de Segurança para priorizar a triagem de alertas em seu ambiente. A pontuação de confiança economiza tempo e esforço por investigar automaticamente os alertas, aplicando as práticas recomendadas do setor e algoritmos inteligentes e atuando como um analista virtual para determinar quais ameaças são reais e onde você precisa concentrar sua atenção.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
