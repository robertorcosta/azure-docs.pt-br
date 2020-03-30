---
title: Firewall de aplicativos web do Azure - Perguntas frequentes
description: Este artigo fornece respostas para perguntas frequentes sobre o Web Application Firewall no Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460629"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Perguntas frequentes para o Firewall de aplicativos web do Azure no Serviço de Porta Frontal do Azure

Este artigo responde a perguntas comuns sobre os recursos e funcionalidades do WAF (Web Application Firewall, firewall de aplicativos web) do Azure. 

## <a name="what-is-azure-waf"></a>O que é o Azure WAF?

O Azure WAF é um firewall de aplicativos da Web que ajuda a proteger seus aplicativos da Web contra ameaças comuns, como injeção de SQL, scripts entre sites e outras explorações da Web. Você pode definir uma política WAF que consiste em uma combinação de regras personalizadas e gerenciadas para controlar o acesso aos seus aplicativos web.

Uma política waf do Azure pode ser aplicada a aplicativos da Web hospedados no Application Gateway ou no Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>O que é WAF na porta da frente do Azure? 

O Azure Front Door é uma rede de entrega de conteúdo e aplicativos altamente escalável e distribuída globalmente. O Azure WAF, quando integrado ao Front Door, interrompe a negação de serviço e ataca aplicativos direcionados na borda da rede Azure, perto de fontes de ataque antes de entrar em sua rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="does-azure-waf-support-https"></a>O Azure WAF suporta HTTPS?

Front Door oferece descarga SSL. O WAF é integrado nativamente com a Porta da Frente e pode inspecionar uma solicitação depois de decodificada.

## <a name="does-azure-waf-support-ipv6"></a>O Azure WAF suporta o IPv6?

Sim. Você pode configurar a restrição ip para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Quão atualizados são os conjuntos de regras gerenciados?

Nós fazemos o nosso melhor para acompanhar a mudança do cenário de ameaças. Uma vez que uma nova regra é atualizada, ela é adicionada ao Conjunto de Regras Padrão com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual é o tempo de propagação se eu fizer uma mudança na minha política do WAF?

A implantação de uma política WAF globalmente leva cerca de 5 minutos e muitas vezes é concluída mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>As políticas do WAF podem ser diferentes para diferentes regiões?

Quando integrado com o Front Door, o WAF é um recurso global. A mesma configuração se aplica em todos os locais da Porta da Frente.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como limitar o acesso ao meu back-end para ser apenas da Porta da Frente?

Você pode configurar a Lista de Controle de Acesso IP no back-end para permitir apenas os intervalos de endereços IP de saída da Porta da Frente e negar qualquer acesso direto da Internet. As tags de serviço são suportadas para você usar em sua rede virtual. Além disso, você pode verificar se o campo de cabeçalho HTTP do Host X-Forwarded-Host é válido para o seu aplicativo web.

## <a name="which-azure-waf-options-should-i-choose"></a>Quais opções do Azure WAF devo escolher?

Existem duas opções ao aplicar políticas WAF no Azure. O WAF com o Azure Front Door é uma solução de segurança de borda distribuída globalmente. O WAF com o Application Gateway é uma solução regional e dedicada. Recomendamos que você escolha uma solução com base em seus requisitos gerais de desempenho e segurança. Para obter mais informações, consulte [O balanceamento de carga com o conjunto de entrega de aplicativos do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Você suporta os mesmos recursos do WAF em todas as plataformas integradas?

Atualmente, as regras ModSec CRS 2.2.9, CRS 3.0 e CRS 3.1 só são suportadas com WAF no Application Gateway. As regras de conjunto de regras padrão, limitantes de taxas e a definição de regras padrão gerenciadas pelo Azure são suportadas apenas com WAF no Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A proteção DDoS é integrada à Porta da Frente? 

Distribuída globalmente nas bordas da rede Azure, a Porta Frontal do Azure pode absorver e isolar geograficamente grandes ataques de grande volume. Você pode criar uma política WAF personalizada para bloquear e classificar automaticamente ataques http(s) de limite de taxas que tenham assinaturas conhecidas. Além disso, você pode ativar o DDoS Protection Standard no VNet onde seus back-ends são implantados. Os clientes do Azure DDoS Protection Standard recebem benefícios adicionais, incluindo proteção de custos, garantia de SLA e acesso a especialistas da Equipe de Resposta Rápida do DDoS para ajuda imediata durante um ataque.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Por que solicitações adicionais acima do limite configurado para a minha regra de limite de taxa são passadas para o meu servidor backend?

Uma regra de limite de taxa pode limitar o tráfego anormalmente alto de qualquer endereço IP do cliente. Você pode configurar um limite no número de solicitações da Web permitidas a partir de um endereço IP do cliente durante uma duração de um minuto ou cinco minutos. Para o controle de taxa granular, a limitação da taxa pode ser combinada com condições adicionais de correspondência, como correspondência de parâmetros HTTP(S). 

As solicitações do mesmo cliente geralmente chegam ao mesmo servidor front door. Nesse caso, você verá solicitações adicionais acima do limite ser bloqueadas imediatamente. 

No entanto, é possível que as solicitações do mesmo cliente possam chegar a um servidor front door diferente que ainda não atualizou o contador de limite de taxa. Por exemplo, o cliente pode abrir uma nova conexão para cada solicitação e o limite é baixo. Neste caso, a primeira solicitação ao novo servidor front door passaria a verificação do limite de tarifa. Um limite de limite de taxa geralmente é definido para se defender contra ataques de negação de serviço de qualquer endereço IP do cliente. Para um limite muito baixo, você pode ver solicitações adicionais acima do limite passar.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Firewall de aplicativos Web do Azure](../overview.md).
- Saiba mais sobre [a Porta da Frente do Azure](../../frontdoor/front-door-overview.md).
