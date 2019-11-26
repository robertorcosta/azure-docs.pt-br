---
title: Integração de produtos de segurança do Azure com a central de segurança do Azure
description: Este tópico apresenta os produtos de segurança do Azure que foram integrados à central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 693e7d35a0bb4c7dfbb3e033690a5e86e2c398a8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278343"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integração dos produtos de segurança do Azure na central de segurança do Azure

A central de segurança do Azure fornece licenças adicionais da Microsoft para trabalhar com os seguintes produtos de segurança:

* [WAF do Azure](#azure-waf)
* [Proteção contra DDoS do Azure](#azure-ddos)

## WAF do Azure<a name="azure-waf"></a>

O Gateway de Aplicativo do Azure oferece um WAF (firewall do aplicativo Web) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O WAF do gateway de aplicativo baseia-se no conjunto de regras principais 3,0 ou 2.2.9 do projeto de segurança de aplicativo Web aberto. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades, sem a necessidade de configuração adicional. Os alertas do WAF são transmitidos para a central de segurança. Para obter mais informações sobre os alertas gerados pelo WAF, consulte [regras e grupos de regras CRS do firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Proteção contra DDoS do Azure<a name="azure-ddos"></a>

Ataques de DDoS (negação de serviço distribuído) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo não fique disponível para usuários legítimos. Os ataques de DDoS podem ter como destino qualquer ponto de extremidade que possa ser acessado pela Internet.

A proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, fornece uma defesa contra ataques de DDoS. A proteção contra DDoS fornece diferentes camadas de serviço. Para obter mais informações, consulte [visão geral da proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

A Proteção contra DDoS Standard pode mitigar os tipos de ataques a seguir:

> [!div class="mx-tableFixed"]

|Alerta|DESCRIÇÃO|
|---|---|
|**Ataque de volumétricos detectado**|O objetivo desse ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A proteção contra DDoS Standard reduz esses ataques potenciais de vários gigabytes, exportando-os e Depurando-os com escala de rede global, automaticamente.|
|**Ataque de protocolo detectado**|Esses ataques renderizam um destino inacessível, explorando um ponto fraco nas pilhas de protocolo da camada 3 e da camada 4. Ele inclui ataques de inundação de SYN, ataques de reflexo e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado.|
|**Ataque de camada de recurso (aplicativo) detectado**|Esses ataques têm como alvo pacotes de aplicativos Web, para interromper a transmissão de dados entre hosts. Esses ataques incluem violações de protocolo HTTP, injeção de SQL, scripts intersites e outros ataques de camada 7. Use o Aplicativo Azure gateway WAF, com a proteção contra DDoS Standard, para se defender contra esses ataques. Também há ofertas de WAF de terceiros disponíveis no Azure Marketplace.|
