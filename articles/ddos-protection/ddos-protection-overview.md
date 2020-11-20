---
title: Visão geral da proteção contra DDoS do Azure Standard
description: Saiba como a Proteção contra DDoS do Azure Standard, quando combinada com as melhores práticas de design do aplicativo, fornece defesa contra ataque de negação de serviço distribuído.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: e3ded2fc286117da1438b0bb28298632532c4329
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992532"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

Cada propriedade no Azure é protegida pela proteção de DDoS (básica) de infraestrutura do Azure sem custo adicional. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A Proteção contra DDoS Básica não exige nenhuma alteração de aplicativo ou de configuração do usuário. A Proteção contra DDoS Básica ajuda a proteger todos os serviços do Azure, incluindo serviços PaaS, como o DNS do Azure.

A proteção contra DDoS do Azure Standard, combinada com as práticas recomendadas de design de aplicativos, fornece recursos aprimorados de mitigação de DDoS para se defender contra ataques de DDoS. Ele é ajustado automaticamente para ajudar a proteger seus recursos específicos do Azure em uma rede virtual. É muito simples habilitar a proteção em qualquer rede virtual nova ou existente, e ela não exige nenhum aplicativo ou alterações de recursos. Ela tem várias vantagens em comparação com o serviço básico, incluindo registro em log, alertas e telemetria. 

![Comparação do serviço de proteção contra DDoS do Azure](./media/ddos-protection-overview/ddos-comparison.png)

A proteção contra DDoS do Azure não armazena dados do cliente.

## <a name="features"></a>Recursos

- **Integração de plataforma nativa:** nativamente integrado ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Proteção completa:** A configuração simplificada protege imediatamente todos os recursos em uma rede virtual assim que a proteção contra DDoS Standard está habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Monitoramento de tráfego AlwaysOn:** Os padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Ajuste adaptativo:** A criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção multicamada:** fornece proteção contra DDoS de pilha completa, quando usado com um firewall do aplicativo Web.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Análise de ataque:** obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, além de um resumo completo após o término dele. Transmita logs de fluxo de mitigação para o [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) ou um sistema de gerenciamento de eventos e informações de segurança (Siem) offline para monitoramento quase em tempo real durante um ataque.
- **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** Os alertas podem ser configurados no início e na interrupção de um ataque e na duração do ataque, usando métricas de ataque internas. Os alertas integram-se ao seu software operacional, como Microsoft Azure logs de monitor, Splunk, armazenamento do Azure, email e o portal do Azure.
- **Resposta rápida a DDoS**: envolva a equipe de resposta rápida de proteção contra DDoS (DRR) para obter ajuda com a investigação e análise de ataques. Para saber mais, confira [resposta rápida a DDoS](ddos-rapid-response.md).
- **Garantia de custo:** Créditos de serviço de transferência de dados e de expansão de aplicativos para ataques de DDoS documentados.

## <a name="pricing"></a>Preços

Para saber mais sobre os preços padrão da proteção contra DDoS do Azure, confira [preços padrão da proteção contra DDoS do Azure](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um plano de proteção contra DDoS](manage-ddos-protection.md)