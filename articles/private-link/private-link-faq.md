---
title: Azure Private Link perguntas freqüentemente feitas (FAQ)
description: Saiba mais sobre o Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349931"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link perguntas freqüentemente feitas (FAQ)

## <a name="private-link"></a>Link Privado

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>O que é o Azure Private Endpoint e o Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint é uma interface de rede que o conecta de forma privada e segura a um serviço alimentado pelo Azure Private Link. Você pode usar pontos finais privados para se conectar a um serviço Azure PaaS que suporta o Private Link ou ao seu próprio Serviço de Link Privado.
- **[Azure Private Link Service](private-link-service-overview.md)**: O serviço Azure Private Link é um serviço criado por um provedor de serviços. Atualmente, um serviço de Private Link pode ser anexado à configuração IP frontend de um Balancer de Carga Padrão. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Como o tráfego está sendo enviado ao usar o Private Link?
O tráfego é enviado privadamente usando o backbone da Microsoft. Não atravessa a internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Qual é a diferença entre um Service Endpoints e um Private Endpoints?
- Ao usar o Private Endpoints, o acesso à rede é concedido a recursos específicos por trás de um determinado serviço que fornece segmentação granular, também o tráfego pode chegar ao recurso de serviço a partir de instalações sem usar pontos finais públicos.
- Um ponto final de serviço continua sendo um endereço IP publicamente roteável.  Um ponto final privado é um IP privado no espaço de endereço da rede virtual onde o ponto final privado está configurado.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço Private Link e o Private Endpoint?
O Private Endpoint fornece acesso a vários tipos de recursos de link privado, incluindo serviços Do Azure PaaS e seu próprio Serviço de Link Privado. É uma relação de um para muitos. Um serviço de Private Link pode receber conexões de vários pontos finais privados. Por outro lado, um ponto final privado só pode se conectar a um serviço de Private Link.    

## <a name="private-endpoint"></a>Ponto de extremidade privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Posso criar vários pontos finais privados no mesmo VNet? Eles podem se conectar a diferentes serviços? 
Sim. Você pode ter vários pontos finais privados na mesma VNet ou sub-rede. Eles podem se conectar a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Preciso de uma sub-rede dedicada para pontos finais privados? 
Não. Você não precisa de uma sub-rede dedicada para pontos finais privados. Você pode escolher um IP de ponto final privado de qualquer sub-rede da VNet onde seu serviço está implantado.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>O Private Endpoint pode se conectar ao serviço Private Link entre os inquilinos ativos do Azure? 
Sim. Os pontos finais privados podem se conectar aos serviços private link ou ao Azure PaaS em inquilinos aD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto final privado pode se conectar aos recursos do Azure PaaS em todas as regiões do Azure?
Sim. Os pontos finais privados podem se conectar aos recursos do Azure PaaS em todas as regiões do Azure.

## <a name="private-link-service"></a>Serviço de Link Privado
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para criar um serviço de Private Link? 
Seus backends de serviço devem estar em uma rede Virtual e atrás de um Balanceador de Carga Padrão.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso dimensionar meu serviço de Private Link? 
Você pode dimensionar seu serviço de Private Link de algumas maneiras diferentes: 
- Adicione VMs Backend à piscina atrás do balanceador de carga padrão 
- Adicione um IP ao serviço Private Link. Permitimos até 8 IPs por serviço Private Link.  
- Adicione o novo serviço de Link Privado ao Balanceador de Carga Padrão. Permitimos até oito serviços de Private Link por balanceador de carga.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso conectar meu serviço a vários pontos finais privados?
Sim. Um serviço de Private Link pode receber conexões de vários pontos finais privados. No entanto, um Ponto Final Privado só pode se conectar a um serviço de Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço Private Link?
Você pode controlar a exposição usando a configuração de visibilidade no serviço Private Link. A visibilidade suporta três configurações:

- **Nenhum** - Somente assinaturas com acesso ao RBAC podem localizar o serviço. 
- **Restritivo** - Somente assinaturas que estejam na lista branca e com acesso ao RBAC podem localizar o serviço. 
- **Todos** - Todos podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de Link Privado com balanceador de carga básico? 
Não. O serviço Private Link sobre um Balanceador de Carga Básica não é suportado.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Uma sub-rede dedicada é necessária para o serviço Private Link? 
Não. O serviço Private Link não requer uma sub-rede dedicada. Você pode escolher qualquer sub-rede em seu VNet onde seu serviço está implantado.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um provedor de serviços usando o Azure Private Link. Preciso ter certeza de que todos os meus clientes têm espaço IP exclusivo e não se sobrepõem ao meu espaço IP? 
Não. O Azure Private Link fornece essa funcionalidade para você. Portanto, você não é obrigado a ter espaço de endereço não sobreposto com o espaço de endereço do seu cliente. 

##  <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Azure Private Link](private-link-overview.md)
