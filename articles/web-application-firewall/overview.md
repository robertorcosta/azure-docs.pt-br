---
title: Introdução ao Firewall do Aplicativo Web do Azure
description: Este artigo fornece uma visão geral do Azure WAF (Firewall do Aplicativo Web)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488282"
---
# <a name="what-is-azure-web-application-firewall"></a>O que é o Firewall do Aplicativo Web do Azure?

O WAF (Firewall do Aplicativo Web) fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. A injeção de SQL e o script entre sites estão entre os ataques mais comuns.

![Visão geral do WAF](media/overview/wafoverview.png)

A prevenção de tais ataques no código do aplicativo é desafiadora. Isso pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a tornar o gerenciamento de segurança muito mais simples. Um WAF também oferece aos administradores de aplicativos melhor garantia de proteção contra ameaças e invasões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rapidamente, corrigindo uma vulnerabilidade conhecida de modo central, em vez de proteger cada um dos aplicativos Web individuais.

O WAF pode ser implantado com Gateway de Aplicativo do Azure e o Azure Front Door Service. Atualmente, o WAF tem recursos que são personalizados para cada serviço específico. Para obter mais informações sobre os recursos do WAF para cada serviço, confira a visão geral de cada serviço.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o Firewall do Aplicativo Web no Gateway de Aplicativo, confira [Firewall de Aplicativo Web no Gateway de Aplicativo do Azure](./ag/ag-overview.md).
- Para obter mais informações sobre o Firewall do Aplicativo Web no Azure Front Door Service, confira [Firewall de Aplicativo Web no Azure Front Door Service](./afds/afds-overview.md).
