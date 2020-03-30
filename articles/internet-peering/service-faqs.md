---
title: Serviço de peering - FAQ
titleSuffix: Azure
description: Serviço de peering - FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775466"
---
# <a name="peering-service---faqs"></a>Serviço de peering - PERGUNTAS FREQUENTES

Você pode revisar as informações abaixo para perguntas gerais.

**Uma operadora pode usar o seu peering direct existente com a Microsoft para suportar o Peering Service?**

Sim, uma operadora pode aproveitar seu PNI existente para suportar o Peering Service. Um PNI de serviço de peering requer diversidade para suportar ha. Se o PNI existente já tiver diversidade, então nenhuma nova infra-estrutura será necessária. Se o PNI existente precisa de diversidade, então ele pode ser aumentado.

**Uma operadora pode usar um novo peering direto com a Microsoft para suportar o Peering Service?**

Sim, isso também é possível. A Microsoft trabalhará com a Carrier para criar um novo peering direto para suportar o peering service.  

**Por que o direct peering é um requisito para suportar o peering Service?**

Um dos principais drivers por trás do Peering Service é fornecer conectividade aos serviços on-line da Microsoft através de uma SP bem conectada. O PNI está sempre na faixa gbps e, portanto, um bloco de construção fundamental para a conectividade de alto throughput entre a operadora e a Microsoft.

**Quais são os requisitos de diversidade em um peering direto para apoiar o Peering Service?**

Um PNI deve suportar redundância local e geo-redundância. A redundância local é definida como dois conjuntos diversos de caminhos em um determinado local de peering. A geo-redundância exige que a Carrier tenha conectividade adicional em um local de borda da Microsoft diferente, caso o site principal falhe. Para a curta duração de falha, a transportadora pode direcionar o tráfego através do site de backup.

**A operadora já oferece SLA e Internet de grau empresarial, como essa oferta é diferente?**

Algumas operadoras oferecem Internet de nível empresarial e SLA por parte da rede. No Peering Service, a Microsoft oferecerá tráfego de oferta de SLA na parte microsoft da rede. Ao selecionar o peering service, o cliente receberá SLA de ponta a ponta. O SLA de seu site para a borda da Microsoft na rede ISP pode ser coberto pelo ISP. O SLA na Microsoft Global Network da Microsoft edge para o aplicativo de usuários finais agora é coberto pela Microsoft.

**Se um provedor de serviços já fizer par com a Microsoft usando o PNI, então que tipo de alterações são necessárias para suportar o Peering Service?**

* Alterações de software para identificar um usuário do Peering Service e seu tráfego. Pode exigir alterações na diretiva de roteamento para trocar o tráfego de um usuário na borda microsoft mais próxima através da conexão Peering Service.
* Certifique-se de que a conectividade tenha redundância local e geo-redundância.
