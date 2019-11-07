---
title: Inteligência contra ameaças & mapa de alertas de segurança – central de segurança do Azure
description: Saiba como usar a funcionalidade de mapa de alerta de segurança e inteligência contra ameaças na Central de Segurança do Azure para identificar possíveis ameaças em VMs e computadores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 97975d72214426907a2ab91f0d3cd98d0ce6734b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693482"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Inteligência contra ameaças e mapa de alertas de segurança
Este artigo ajuda você a usar o mapa de alertas da Central de Segurança do Azure e o mapa de inteligência de ameaças baseada em eventos de segurança para solucionar problemas relacionados à segurança.

> [!NOTE]
> O botão mapa de *eventos* de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Como mapear os trabalhos do mapa de alertas
A Central de Segurança fornece a você com um mapa que ajuda você a identifica ameaças de segurança no ambiente. Por exemplo, você pode identificar se determinado computador faz parte de um botnet, e onde a ameaça é proveniente. Computadores podem se tornar nós em um botnet quando os invasores instalam de forma ilícita malware que secretamente interage com o comando e controle que gerencia o botnet. 

Para criar esse mapa, a Central de Segurança usa dados que vêm de várias fontes da Microsoft. Central de Segurança do Azure usa esses dados para mapear as ameaças potenciais ao seu ambiente. 

Uma das etapas de um processo de [resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, você deverá realizar as seguintes tarefas:

- Determinar a natureza do ataque.
- Determine o ponto de origem do ataque.
- Determinar o objetivo do ataque. O ataque foi direcionado à sua organização para adquirir informações específicas ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os arquivos que foram acessados e determinar a confidencialidade desses arquivos.

Você pode usar o mapa de alertas de Segurança na Central de Segurança do Azure para ajudar com essas tarefas.

## <a name="access-the-security-alerts-map"></a>Mapa de alertas de segurança de acesso
Para visualizar as ameaças atuais em seu ambiente, abra o mapa de alertas de segurança:

1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, em **proteção contra ameaças** selecionar **Mapa de alertas de segurança**. O mapa abre.
3. Para obter mais informações sobre o alerta e receber as etapas de correção, clique no ponto do Alerta no mapa e siga as instruções. 
 
O mapa de alertas de segurança é baseado em alertas. Esses alertas são baseados nas atividades de para qual rede de comunicação foi associada a um endereço IP que foi resolvido com êxito, se o endereço IP é um endereço IP arriscado conhecido (por exemplo, um cryptominer conhecido) ou um endereço IP que não é reconhecido anteriormente como arriscado. O mapa fornece alertas em todas as assinaturas que você selecionou anteriormente no Azure. 

Os alertas no mapa são exibidos de acordo com a localização geográfica onde elas forem detectadas como sendo proveniente e são codificadas por severidade. 
    ![Informações de inteligência contra ameaças](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Confira também
Neste artigo, você aprendeu a usar a inteligência contra ameaças na Central de Segurança para ajudar a identificar atividades suspeitas. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
