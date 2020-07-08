---
title: Conceitos-gerenciamento de API
description: Saiba como o gerenciamento de API protege as APIs em execução em VMs (máquinas virtuais) da AVS (solução VMware) do Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306676"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>Gerenciamento de API para publicar e proteger APIs em execução em VMs baseadas em AVS

Microsoft Azure [Gerenciamento de API](https://azure.microsoft.com/services/api-management/) permite que desenvolvedores e equipes DevOpss publiquem com segurança para consumidores internos ou externos.

Embora oferecido em várias SKUs, somente o desenvolvedor e os SKUs Premium permitem a integração de rede virtual do Azure para publicar APIs em execução em cargas de trabalho da solução VMware do Azure (AVS). Esses dois SKUs habilitam com segurança a conectividade entre o serviço de gerenciamento de API e o back-end. A SKU do desenvolvedor destina-se ao desenvolvimento e ao teste enquanto o SKU Premium é para implantações de produção.

Para serviços de back-end que são executados sobre VMs (máquinas virtuais) de sincronização automática, a configuração no gerenciamento de API, por padrão, é igual aos serviços de back-end local. Para implantações internas e externas, o gerenciamento de API configura o VIP (IP virtual) do balanceador de carga como o ponto de extremidade de back-end quando o servidor back-end é colocado atrás de um Load Balancer NSX no lado da AVS.

## <a name="external-deployment"></a>Implantação externa

Uma implantação externa publica APIs consumidas por usuários externos usando um ponto de extremidade público. Desenvolvedores e engenheiros de DevOps podem gerenciar APIs por meio do portal do Azure ou do PowerShell e do portal do desenvolvedor de gerenciamento de API.

O diagrama de implantação externa mostra todo o processo e os atores envolvidos (mostrados na parte superior). Os atores são:

- **Administrador (es):** Representa a equipe administrador ou DevOps, que gerencia a AVS por meio dos mecanismos de portal do Azure e automação como PowerShell ou DevOps do Azure.

- **Usuários:**  Representa os consumidores das APIs expostas e representa os usuários e os serviços que consomem as APIs.

O fluxo de tráfego passa pela instância de gerenciamento de API, que abstrai os serviços de back-end, conectados à rede virtual do Hub. O gateway de ExpressRoute roteia o tráfego para o ExpressRoute Alcance Global Channel e atinge um NSX Load Balancer distribuindo o tráfego de entrada para as diferentes instâncias de serviços de back-end.

O gerenciamento de API tem uma API pública do Azure e a ativação do serviço de proteção contra DDOS do Azure é recomendada. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Implantação externa-gerenciamento de API para AVS":::


## <a name="internal-deployment"></a>Implantação interna

Uma implantação interna publica APIs consumidas por usuários ou sistemas internos. A equipe DevOps e os desenvolvedores de API usam as mesmas ferramentas de gerenciamento e portal do desenvolvedor que a implantação externa.

As implantações internas podem ser [com aplicativo Azure gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) para criar um ponto de extremidade público e seguro para a API, aproveitando seus recursos e criando uma implantação híbrida que permite cenários diferentes.  A API aproveita seus recursos e cria uma implantação híbrida que permite cenários diferentes.

* Use o mesmo recurso de gerenciamento de API para consumo por consumidores internos e externos.

* Ter um único recurso de gerenciamento de API com um subconjunto de APIs definido e disponível para consumidores externos.

* Forneça uma maneira fácil de ativar e desativar o acesso ao gerenciamento de API a partir da Internet pública.

O diagrama de implantação abaixo mostra os consumidores que podem ser internos ou externos, com cada tipo acessando as mesmas ou diferentes APIs.

Em uma implantação interna, as APIs são expostas para a mesma instância de gerenciamento de API. Na frente do gerenciamento de API, o gateway de aplicativo é implantado com a capacidade do WAF (firewall do aplicativo Web) do Azure ativada e um conjunto de ouvintes e regras HTTP para filtrar o tráfego, expondo apenas um subconjunto dos serviços de back-end em execução no AVS.

* O tráfego interno é roteado pelo gateway de ExpressRoute para o Firewall do Azure e, em seguida, para o gerenciamento de API, se as regras de tráfego forem estabelecidas ou diretamente para o gerenciamento  

* O tráfego externo entra no Azure por meio do gateway de aplicativo, que usa a camada de proteção externa para o gerenciamento de API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Implantação interna-gerenciamento de API para AVS":::