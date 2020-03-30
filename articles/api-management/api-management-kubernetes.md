---
title: Use o gerenciamento de API do Azure com microserviços implantados no serviço Azure Kubernetes | Microsoft Docs
description: Este artigo descreve as opções de implantação do Gerenciamento de API com AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480990"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Use o Gerenciamento de API do Azure com microserviços implantados no Serviço Azure Kubernetes

Microserviços são perfeitos para construir APIs. Com [o Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), você pode implantar e operar rapidamente uma arquitetura baseada em [microsserviços](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) na nuvem. Em seguida, você pode aproveitar [o Azure API Management](https://aka.ms/apimrocks) (API Management) para publicar seus microserviços como APIs para consumo interno e externo. Este artigo descreve as opções de implantação do Gerenciamento de API com AKS. Ele assume o conhecimento básico da Kubernetes, Gerenciamento de API e rede Azure. 

## <a name="background"></a>Segundo plano

Ao publicar microsserviços como APIs para consumo, pode ser desafiador gerenciar a comunicação entre os microsserviços e os clientes que os consomem. Há uma infinidade de preocupações transversais, como autenticação, autorização, estrangulamento, cache, transformação e monitoramento. Essas preocupações são válidas independentemente de os microserviços estarem expostos a clientes internos ou externos. 

O [padrão API Gateway](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) aborda essas preocupações. Um gateway de API serve como porta de entrada para os microsserviços, desacopla clientes de seus microsserviços, adiciona uma camada adicional de segurança e diminui a complexidade de seus microsserviços, removendo a carga de lidar com preocupações de corte transversal. 

[O Azure API Management](https://aka.ms/apimrocks) é uma solução chave para resolver as necessidades de gateway da API. Você pode criar rapidamente um gateway consistente e moderno para seus microsserviços e publicá-los como APIs. Como uma solução de gerenciamento de API de ciclo de vida completo, ele também fornece recursos adicionais, incluindo um portal de desenvolvedor de autoatendimento para detecção de API, gerenciamento do ciclo de vida da API e análise de API.

Quando usados em conjunto, a AKS e o API Management fornecem uma plataforma para implantar, publicar, proteger, monitorar e gerenciar suas APIs baseadas em microsserviços. Neste artigo, passaremos por algumas opções de implantação de AKS em conjunto com a API Management. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Serviços e APIs

Em um cluster Kubernetes, os contêineres são implantados em [Pods,](https://kubernetes.io/docs/concepts/workloads/pods/pod/)que são efêmeros e têm um ciclo de vida. Quando um nó de trabalhador morre, os Pods correndo no nó são perdidos. Portanto, o endereço IP de um Pod pode ser alterado a qualquer momento. Não podemos confiar nele para nos comunicarmos com a cápsula. 

Para resolver esse problema, a Kubernetes introduziu o conceito de [Serviços.](https://kubernetes.io/docs/concepts/services-networking/service/) Um Kubernetes Service é uma camada de abstração que define um grupo lógico de Pods e permite exposição externa ao tráfego, balanceamento de carga e descoberta de serviços para esses Pods. 

Quando estamos prontos para publicar nossos microserviços como APIs através do Gerenciamento de API, precisamos pensar em como mapear nossos serviços em Kubernetes para APIs em Gerenciamento de API. Não há regras definidas. Depende de como você projetou e dividiu seus recursos ou domínios de negócios em microsserviços no início. Por exemplo, se os pods por trás de um Serviço forem responsáveis por todas as operações em um determinado recurso (por exemplo, Cliente), o Serviço pode ser mapeado para uma API. Se as operações em um recurso forem divididas em vários microserviços (por exemplo, GetOrder, PlaceOrder), então vários Serviços podem ser logicamente agregados em uma única API no gerenciamento de API (Ver Fig. 1). 

Os mapeamentos também podem evoluir. Como a API Management cria uma fachada em frente aos microsserviços, ela nos permite refatorar e dimensionar corretamente nossos microsserviços ao longo do tempo. 

![Mapear serviços para APIs](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Implantar gerenciamento de API em frente à AKS

Existem algumas opções de implantação do Gerenciamento de API em frente a um cluster AKS. 

Embora um cluster AKS esteja sempre implantado em uma rede virtual (VNet), uma instância de gerenciamento de API não é necessária para ser implantada em um VNet. Quando o Gerenciamento de API não reside no vnet do cluster, o cluster AKS deve publicar pontos finais públicos para que o Gerenciamento de API se conecte. Nesse caso, há a necessidade de garantir a conexão entre o Gerenciamento de API e a AKS. Em outras palavras, precisamos garantir que o cluster só possa ser acessado exclusivamente através da Gestão de API. Vamos ver as opções. 

### <a name="option-1-expose-services-publicly"></a>Opção 1: Expor serviços publicamente

Os serviços em um cluster AKS podem ser expostos publicamente usando tipos de [serviço](https://docs.microsoft.com/azure/aks/concepts-network) de NodePort, LoadBalancer ou ExternalName. Neste caso, os serviços são acessíveis diretamente da internet pública. Depois de implantar o Gerenciamento de API em frente ao cluster, precisamos garantir que todo o tráfego de entrada passe pelo Gerenciamento de API, aplicando autenticação nos microserviços. Por exemplo, o Gerenciamento de API pode incluir um token de acesso em cada solicitação feita ao cluster. Cada microserviço é responsável por validar o token antes de processar a solicitação. 


Essa pode ser a opção mais fácil de implantar o Gerenciamento de API na frente do AKS, especialmente se você já tiver a lógica de autenticação implementada em seus microsserviços. 

![Publicar serviços diretamente](./media/api-management-aks/direct.png)

Prós:
* Configuração fácil no lado de Gerenciamento de API porque não precisa ser injetada no cluster VNet
* Nenhuma alteração no lado AKS se os Serviços já estiverem expostos publicamente e a lógica de autenticação já existir em microserviços

Contras:
* Risco potencial de segurança devido à visibilidade pública dos pontos finais do Serviço
* Nenhum ponto de entrada única para tráfego de cluster de entrada
* Complica microserviços com lógica de autenticação duplicada

### <a name="option-2-install-an-ingress-controller"></a>Opção 2: Instalar um controlador de ingressa

Embora a Opção 1 possa ser mais fácil, ela tem desvantagens notáveis como mencionado acima. Se uma instância de gerenciamento de API não residir no cluster VNet, a autenticação Mutual TLS (mTLS) é uma maneira robusta de garantir que o tráfego seja seguro e confiável em ambas as direções entre uma instância de gerenciamento de API e um cluster AKS. 

A autenticação TLS mútua é [suportada nativamente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) pelo Gerenciamento de API e pode ser habilitada em Kubernetes [instalando um Controlador de Ingress](https://docs.microsoft.com/azure/aks/ingress-own-tls) (Fig. 3). Como resultado, a autenticação será realizada no Controlador ingress, que simplifica os microserviços. Além disso, você pode adicionar os endereços IP do Gerenciamento de API à lista permitida pela Ingress para garantir que apenas o Gerenciamento de API tenha acesso ao cluster.  

 
![Publicar através de um controlador de ingress](./media/api-management-aks/ingress-controller.png)


Prós:
* Configuração fácil no lado de Gerenciamento de API porque não precisa ser injetada no cluster VNet e mTLS é suportado nativamente
* Centraliza a proteção para tráfego de cluster de entrada na camada do Controlador de Entrada
* Reduz o risco de segurança minimizando os pontos finais de cluster susgênicos publicamente

Contras:
* Aumenta a complexidade da configuração de cluster devido ao trabalho extra para instalar, configurar e manter o Controlador de Ingress e gerenciar certificados usados para mTLS
* Risco de segurança devido à visibilidade pública do ponto final do Controlador de Ingress


Quando você publica APIs por meio do Gerenciamento de API, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP ao fazer chamadas para essas APIs. Caso contrário, as chamadas serão imediatamente rejeitadas pelo gateway de Gerenciamento de API. Elas não são encaminhadas para os serviços de back-end.

É necessária uma assinatura para obter uma chave de assinatura para acessar APIs. A assinatura é essencialmente um contêiner nomeado para um par de chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas podem obter as assinaturas. E eles não precisam da aprovação dos editores das APIs. Os editores de API também podem criar assinaturas diretamente para os consumidores da API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opção 3: Implantar APIM dentro do cluster VNet

Em alguns casos, clientes com restrições regulatórias ou rigorosos requisitos de segurança podem encontrar soluções não viáveis das Opções 1 e 2 devido a pontos finais expostos publicamente. Em outros, o cluster AKS e os aplicativos que consomem os microsserviços podem residir dentro do mesmo VNet, portanto não há razão para expor o cluster publicamente, pois todo o tráfego de API permanecerá dentro do VNet. Para esses cenários, você pode implantar o Gerenciamento de API no vnet do cluster. [O nível API Management Premium](https://aka.ms/apimpricing) suporta a implantação do VNet. 

Existem dois modos de implantação do Gerenciamento de [API em um VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) – Externo e Interno. 

Se os consumidores de API não residirem no cluster VNet, o modo Externo (Fig. 4) deve ser usado. Neste modo, o gateway API Management é injetado no cluster VNet, mas acessível a partir da internet pública através de um balanceador de carga externo. Ajuda a ocultar o cluster completamente enquanto ainda permite que clientes externos consumam os microserviços. Além disso, você pode usar os recursos de rede do Azure, como o NSG (Network Security Groups, grupos de segurança de rede) para restringir o tráfego de rede.

![Modo VNet externo](./media/api-management-aks/vnet-external.png)

Se todos os consumidores de API residirem no cluster VNet, então o modo Interno (Fig. 5) poderá ser usado. Neste modo, o gateway De Gerenciamento de API é injetado no cluster VNET e acessível apenas a partir deste VNet através de um balanceador de carga interno. Não há como acessar o gateway de Gerenciamento de API ou o cluster AKS a partir da internet pública. 

![Modo VNet interno](./media/api-management-aks/vnet-internal.png)

 Em ambos os casos, o cluster AKS não é visível publicamente. Em comparação com a Opção 2, o Controlador de Ingress pode não ser necessário. Dependendo do seu cenário e configuração, a autenticação ainda pode ser necessária entre o Gerenciamento de API e seus microserviços. Por exemplo, se uma malha de serviço for adotada, ela sempre requer autenticação TLS mútua. 

Prós:
* A opção mais segura porque o cluster AKS não tem ponto final público
* Simplifica a configuração do cluster, uma vez que não tem ponto final público
* Capacidade de ocultar tanto o gerenciamento de API quanto o AKS dentro do VNet usando o modo Interno
* Capacidade de controlar o tráfego de rede usando recursos de rede do Azure, como o NSG (Network Security Groups, grupos de segurança de rede)

Contras:
* Aumenta a complexidade da implantação e configuração do gerenciamento de API para trabalhar dentro do VNet

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conceitos de rede para aplicações em AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Saiba mais sobre [como usar o Gerenciamento de API com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





