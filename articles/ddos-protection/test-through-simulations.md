---
title: Teste de simulação da proteção contra DDoS do Azure
description: Saiba mais sobre como testar por meio de simulações
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905091"
---
# <a name="test-through-simulations"></a>Testar por meio de simulações

É uma prática recomendada testar suas suposições sobre como os serviços responderão a um ataque ao conduzir simulações periódicas. Durante o teste, valide se os serviços ou aplicativos continuam funcionando conforme esperado e se não há interrupções na experiência do usuário. Identifique lacunas do ponto de vista de tecnologia e de processo, e incorpore-as à estratégia de resposta de DDoS. Recomendamos que realize esses testes em ambientes de preparo ou fora do horário de pico para minimizar o impacto no ambiente de produção.

Fizemos uma parceria com o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), um gerador de tráfego de autoatendimento, para criar uma interface em que os clientes do Azure possam gerar tráfego em relação aos pontos de extremidade públicos habilitados para proteção contra DDoS para simulações. Você pode usar a simulação para:

- Validar como a Proteção contra DDoS do Azure ajuda a proteger seus recursos do Azure contra ataques de DDoS.
- Otimize o processo de resposta a incidentes durante ataques de DDoS.
- Documente a conformidade de DDoS.
- Treine suas equipes de segurança de rede.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md) com endereços IP públicos protegidos.
- Você deve primeiro criar uma conta com o [BreakingPoint Cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Configurar um ataque de teste de DDoS

1. Insira ou selecione os valores a seguir e selecione **Iniciar teste** :

    |Configuração        |Valor                                              |
    |---------      |---------                                          |
    |Endereço IP de destino           | Insira um endereço IP público que você deseja testar.                     |
    |Número da porta   | Inserir _443_ .                       |
    |Perfil de DDoS | Selecione **TCP SYN Flood** .|
    |Tamanho do teste       | Selecione **200 PPS, 100 Mbps e 8 IPS de origem.**                                  |
    |Duração do teste | Selecione **10 minutos** .|

Agora deve aparecer assim:

![Exemplo de simulação de ataque de DDoS: nuvem BreakingPoint](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorar e validar

1. Faça logon no https://portal.azure.com e acesse sua assinatura.
1. Selecione o endereço IP público no qual você testou o ataque.
1. Em **Monitoramento** , selecione **Métricas** .
1. Para **métrica** , selecione _sob ataque de DDoS ou não_ .

Depois que o recurso estiver sob ataque, você verá que o valor muda de **0** para **1** , como a imagem a seguir:

![Exemplo de simulação de ataque de DDoS: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Exibir e configurar a telemetria de proteção contra DDoS](telemetry-monitoring-alerting.md).
- Saiba como [configurar relatórios de mitigação de ataque de DDoS e logs de fluxo](reports-and-flow-logs.md).
- Saiba como [envolver uma resposta rápida de DDoS](ddos-rapid-response.md).