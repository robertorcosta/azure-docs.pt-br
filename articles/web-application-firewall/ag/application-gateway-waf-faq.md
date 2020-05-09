---
title: Firewall do aplicativo Web do Azure no gateway de aplicativo-perguntas frequentes
description: Este artigo fornece respostas para perguntas frequentes sobre o Firewall do aplicativo Web no gateway de aplicativo
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 57081cd948bcee1261415eae31f91b3c61f08c9f
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842845"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Perguntas frequentes sobre o Firewall do aplicativo Web do Azure no gateway de aplicativo

Este artigo responde a perguntas comuns sobre o WAF (firewall do aplicativo Web) do Azure em recursos e funcionalidades do gateway de aplicativo. 

## <a name="what-is-azure-waf"></a>O que é o Azure WAF?

O Azure WAF é um firewall do aplicativo Web que ajuda a proteger seus aplicativos Web contra ameaças comuns, como injeção de SQL, scripts entre sites e outras explorações da Web. Você pode definir uma política de WAF que consiste em uma combinação de regras personalizadas e gerenciadas para controlar o acesso aos seus aplicativos Web.

Uma política do Azure WAF pode ser aplicada a aplicativos Web hospedados no gateway de aplicativo ou nas portas de front-end do Azure.

## <a name="what-features-does-the-waf-sku-support"></a>A quais recursos o WAF SKU dá suporte?

A SKU do WAF dá suporte a todos os recursos disponíveis no SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

Monitore o WAF por meio do log de diagnóstico. Para obter mais informações, consulte [log de diagnóstico e métricas para o gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Não. O modo de detecção registra somente o tráfego que dispara uma regra WAF.

## <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim. Para obter mais informações, consulte [Personalizar regras e grupos de regras de WAF](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quais regras estão disponíveis no momento para WAF?

O WAF atualmente dá suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)e [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). Essas regras fornecem segurança de linha de base contra a maioria das 10 principais vulnerabilidades que abrem o OWASP (projeto de segurança de aplicativos Web) identifica: 

* Proteção contra injeção de SQL
* Proteção de scripts entre sites
* Proteção contra ataques comuns da Web, como injeção de comandos, indesejada de solicitação HTTP, divisão de resposta HTTP e ataque de inclusão de arquivo remoto
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação
* Prevenção contra bots, rastreadores e scanners
* Detecção de incorretas configurações de aplicativo comuns (isto é, Apache, IIS e assim por diante)

Para obter mais informações, consulte [OWASP Top-10 vulnerabilidades](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

## <a name="does-waf-support-ddos-protection"></a>O WAF dá suporte à proteção contra DDoS?

Sim. Você pode habilitar a proteção contra DDoS na rede virtual em que o gateway de aplicativo é implantado. Essa configuração garante que o serviço de proteção contra DDoS do Azure também proteja o VIP (IP virtual) do gateway de aplicativo.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Firewall do aplicativo Web do Azure](../overview.md).
- Saiba mais sobre a [porta frontal do Azure](../../frontdoor/front-door-overview.md).
