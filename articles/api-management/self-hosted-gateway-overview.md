---
title: Visão geral do gateway de gerenciamento de API do Azure auto-hospedado | Microsoft Docs
description: Saiba como o gateway de gerenciamento de API do Azure autohospedado ajuda as organizações a gerenciar APIs nos ambientes híbrido e multicloud.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513712"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Visão geral do gateway de gerenciamento de API auto-hospedado

Este artigo explica como o recurso de gateway auto-hospedado permite o gerenciamento de API híbrido e multi-nuvem, apresenta sua arquitetura de alto nível e destaca suas capacidades fundamentais.

> [!NOTE]
> O recurso gateway auto-hospedado está em visualização. Durante a visualização, o gateway auto-hospedado está disponível apenas nos níveis Desenvolvedor e Premium sem nenhum custo adicional. O nível do desenvolvedor está limitado a uma única implantação de gateway auto-hospedado.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gerenciamento de API híbrido e multi-nuvem

O recurso de gateway auto-hospedado expande o suporte à API Management para ambientes híbridos e multi-nuvem e permite que as organizações gerenciem de forma eficiente e segura as APIs hospedadas no local e em frente às nuvens de um único serviço de Gerenciamento de API no Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade de implantar uma versão contêiner do componente gateway de gerenciamento de API para os mesmos ambientes onde hospedam suas APIs. Todos os gateways auto-hospedados são gerenciados a partir do serviço de Gerenciamento de API com o que são federados, proporcionando aos clientes a visibilidade e a experiência de gerenciamento unificada em todas as APIs internas e externas. A colocação dos gateways perto das APIs permite que os clientes otimizem os fluxos de tráfego da API e acendam os requisitos de segurança e conformidade.

Cada serviço de gerenciamento de API é composto pelos seguintes componentes-chave:

-   O plano de gerenciamento, exposto como uma API, usado para configurar o serviço através do portal Azure, PowerShell e outros mecanismos suportados.
-   O Gateway (ou plano de dados) é responsável por proxy de solicitações de API, aplicação de políticas e coleta de telemetria
-   Portal de desenvolvedores usado por desenvolvedores para descobrir, aprender e a bordo para usar as APIs

Por padrão, todos esses componentes são implantados no Azure, fazendo com que todo o tráfego de API (mostrado como setas pretas sólidas na imagem abaixo) flua pelo Azure, independentemente de onde os backends que implementam as APIs estejam hospedados. A simplicidade operacional desse modelo vem ao custo de aumento da latência, problemas de conformidade e, em alguns casos, taxas adicionais de transferência de dados.

![Fluxo de tráfego de API sem gateways auto-hospedados](media/self-hosted-gateway-overview/without-gateways.png)

A implantação de gateways auto-hospedados nos mesmos ambientes que as implementações de API back-end e a adição ao serviço de gerenciamento de API permite que o tráfego de API flua diretamente para as APIs de back-end, o que melhora a latência, otimiza os custos de transferência de dados e permite conformidade, mantendo os benefícios de ter um único ponto de gerenciamento e descoberta de todas as APIs dentro da organização, independentemente de onde suas implementações estão hospedadas.

![Fluxo de tráfego de API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Embalagem e recursos

O gateway auto-hospedado é uma versão contêiner e funcionalmente equivalente do gateway gerenciado implantado no Azure como parte de todos os serviços de Gerenciamento de API. O gateway auto-hospedado está disponível como um contêiner Docker baseado em Linux no Microsoft Container Registry. Ele pode ser implantado no Docker, Kubernetes ou em qualquer outra solução de orquestração de contêineres em execução em uma infra-estrutura de desktop, servidor ou nuvem.

> [!IMPORTANT]
> Algumas funcionalidades disponíveis no gateway gerenciado ainda não estão disponíveis na pré-visualização. Mais notavelmente: Política de log to Event Hub, integração de malha de serviço, http/2 a jusante. Não há nenhum plano para disponibilizar um cache incorporado no gateway auto-hospedado.

## <a name="connectivity-to-azure"></a>Conectividade com o Azure

O gateway auto-hospedado requer conectividade TCP/IP de saída para o Azure na porta 443. Cada gateway auto-hospedado deve ser associado a um único serviço de Gerenciamento de API e é configurado através de seu plano de gerenciamento. O gateway auto-hospedado usa conectividade ao Azure para:

-   Relatando seu status enviando mensagens de batimentos cardíacos a cada minuto
-   Verificando regularmente (a cada 10 segundos) e aplicando atualizações de configuração sempre que estiverem disponíveis
-   Envio de registros de solicitação e métricas para o Azure Monitor, se configurado para fazê-lo
-   Envio de eventos para insights de aplicativos, se definido para fazê-lo

Quando a conectividade com o Azure for perdida, o gateway auto-hospedado não poderá receber atualizações de configuração, relatar seu status ou fazer o upload da telemetria.

O gateway auto-hospedado foi projetado para "falhar estático" e pode sobreviver à perda temporária de conectividade ao Azure. Ele pode ser implantado com ou sem backup de configuração local ligado. No primeiro caso, os gateways auto-hospedados salvarão regularmente uma cópia de backup da configuração em um volume persistente conectado ao contêiner ou pod.

Quando o backup de configuração é desligado e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados que estão sendo executados continuarão a funcionar usando uma cópia na memória da configuração
-   Gateways auto-hospedados parados não serão capazes de iniciar

Quando o backup de configuração é ligado e a conectividade com o Azure é interrompida:

-   Os gateways auto-hospedados que estão sendo executados continuarão a funcionar usando uma cópia na memória da configuração
-   Gateways auto-hospedados parados começarão a usar uma cópia de backup da configuração

Quando a conectividade for restaurada, cada gateway auto-hospedado afetado pela paralisação se reconectará automaticamente com seu serviço de gerenciamento de API associado e baixará todas as atualizações de configuração que ocorreram enquanto o gateway estava "off-line".

## <a name="next-steps"></a>Próximas etapas

-   [Leia um whitepaper para obter informações adicionais sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Implantar gateway auto-hospedado no Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Implantar gateway auto-hospedado no Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
