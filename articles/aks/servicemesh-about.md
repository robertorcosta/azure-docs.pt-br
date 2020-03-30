---
title: Sobre as metas de serviço
description: Obtenha uma visão geral das corredeiras de serviço, sua arquitetura e recursos e quais critérios você deve considerar ao selecionar um para implantar.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594304"
---
# <a name="about-service-meshes"></a>Sobre as metas de serviço

Uma malha de serviço fornece recursos como gerenciamento de tráfego, resiliência, política, segurança, identidade forte e observância às suas cargas de trabalho. Seu aplicativo é dissociado desses recursos operacionais e a malha de serviço os move para fora da camada de aplicativos e para a camada de infra-estrutura.

## <a name="scenarios"></a>Cenários

Estes são alguns dos cenários que podem ser habilitados para suas cargas de trabalho quando você usa uma malha de serviço:

- **Criptografe todo o tráfego em cluster** - Habilite TLS mútuos entre serviços especificados no cluster. Isso pode ser estendido à ingestão e saída no perímetro da rede. Fornece uma opção segura por padrão sem alterações necessárias para o código do aplicativo e a infra-estrutura.

- **Lançamentos canários e em fases** - Especifique as condições para que um subconjunto de tráfego seja encaminhado para um conjunto de novos serviços no cluster. No teste bem-sucedido da liberação canária, remova o roteamento condicional e a fase gradualmente aumentando % de todo o tráfego para o novo serviço. Eventualmente, todo o tráfego será direcionado para um novo serviço.

- **Gerenciamento e manipulação** de tráfego - Crie uma política sobre um serviço que limitará todo o tráfego a uma versão de um serviço de origem específica. Ou uma política que aplique uma estratégia de repetição às classes de falhas entre serviços especificados. Espelho de tráfego ao vivo para novas versões de serviços durante uma migração ou para depurar problemas. Injete falhas entre os serviços em um ambiente de teste para testar a resiliência.

- **Observabilidade** - Obtenha uma visão de como seus serviços estão conectados ao tráfego que flui entre eles. Obtenha métricas, registros e vestígios para todo o tráfego em cluster, e entrada/saída. Adicione habilidades de rastreamento distribuídas às suas aplicações.

## <a name="architecture"></a>Arquitetura

Uma malha de serviço é tipicamente composta por um plano de controle e o plano de dados.

O **plano de controle** tem uma série de componentes que suportam o gerenciamento da malha de serviço. Isso normalmente incluirá uma interface de gerenciamento que pode ser uma Interface de Interface ou Uma API. Também haverá componentes que gerenciam as definições de regras e políticas que definem como a malha de serviço deve implementar recursos específicos. Há também componentes que gerenciam aspectos de segurança como identidade forte e certificados para mTLS. As mecas de serviço também normalmente terão um componente de métricas ou observabilidade que coleta e agrega métricas e telemetria das cargas de trabalho.

O plano de **dados** normalmente consiste em um proxy que é injetado de forma transparente como um sidecar para suas cargas de trabalho. Este proxy está configurado para controlar todo o tráfego de rede dentro e fora do pod que contém sua carga de trabalho. Isso permite que o proxy seja configurado para proteger o tráfego via mTLS, direcionar dinamicamente o tráfego, aplicar políticas ao tráfego e coletar métricas e rastrear informações. 

![Arquitetura típica de malha de serviço](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Funcionalidades

Cada uma das difusas de serviço tem um ajuste natural e se concentra em suportar cenários específicos, mas você normalmente descobrirá que a maioria implementará um número, se não todos, das seguintes capacidades.

### <a name="traffic-management"></a>Gerenciamento de tráfego 

- **Protocolo** – camada 7 (http, grpc)
- **Roteamento Dinâmico** – condicional, ponderação, espelhamento
- **Resiliência** - tempoouts, repetições, disjuntores
- **Política** – controle de acesso, limites de tarifas, cotas
- **Teste** - injeção de falha

### <a name="security"></a>Segurança

- **Criptografia** – mTLS, gerenciamento de certificados, CA externa
- **Identidade Forte** – SPIFFE ou similar
- **Auth** - autenticação, autorização

### <a name="observability"></a>Observabilidade

- **Métricas** – métricas douradas, prometeu, grafana
- **Rastreamento** - traços através de cargas de trabalho
- **Tráfego** – cluster, ingress/egresso

### <a name="mesh"></a>Malha

- **Computação suportada** - Kubernetes, máquinas virtuais
- **Multi-cluster** - gateways, federação

## <a name="selection-criteria"></a>Critérios de seleção

Antes de selecionar uma malha de serviço, certifique-se de entender seus requisitos e as razões para instalar uma malha de serviço. Tente fazer as seguintes perguntas.

- **Um controlador de ingress é suficiente para minhas necessidades?** - Às vezes, ter uma capacidade como teste a/b ou divisão de tráfego na entrada é suficiente para suportar o cenário necessário. Não adicione complexidade ao seu ambiente sem nenhum lado positivo.

- **Minhas cargas de trabalho e ambiente podem tolerar as despesas adicionais?** - Todos os componentes adicionais necessários para suportar a malha de serviço requerem recursos adicionais, como cpu e memória. Além disso, todos os proxies e suas verificações de política associadas adicionam latência ao seu tráfego. Se você tiver cargas de trabalho muito sensíveis à latência ou não puder fornecer os recursos adicionais para cobrir os componentes da malha de serviço, então reconsidere.

- **Isso está adicionando complexidade adicional desnecessariamente?** - Se a razão para instalar uma malha de serviço é para obter um recurso que não seja necessariamente crítico para as equipes de negócios ou operacionais, então considere se a complexidade adicional de instalação, manutenção e configuração vale a pena.

- **Isso pode ser adotado em uma abordagem incremental?** - Algumas das metas de serviço que fornecem muitas capacidades podem ser adotadas em uma abordagem mais incremental. Instale apenas os componentes necessários para garantir o seu sucesso. Uma vez que você esteja mais confiante e recursos adicionais são necessários, então explore-os. Resista à vontade de instalar *tudo* desde o início.

Se, após cuidadosa consideração, você decidir que precisa de uma malha de serviço para fornecer os recursos necessários, então sua próxima decisão é *qual malha de serviço?*

Considere as seguintes áreas e quais delas estão mais alinhadas com suas necessidades. Isso irá guiá-lo para o melhor ajuste para o seu ambiente e cargas de trabalho. A seção [Próximas passos](#next-steps) o levará a informações mais detalhadas sobre as formas específicas de serviço e como elas mapeiam para essas áreas.

- **Técnico** - gerenciamento de tráfego, política, segurança, observância

- **Negócios** - suporte comercial, fundação (CNCF), licença oss, governança

- **Operacional** – instalação/upgrades, requisitos de recursos, requisitos de desempenho, integrações (métricas, telemetria, dashboards, ferramentas, SMI), cargas de trabalho mistas (pools de nós Linux e Windows), computação (Kubernetes, máquinas virtuais), multicluster

- **Segurança** - auth, identidade, gerenciamento de certificados e rotação, CA externa plugável


## <a name="next-steps"></a>Próximas etapas

A documentação a seguir fornece mais informações sobre malhas de serviço que você pode testar no Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Saiba mais sobre Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre cônsul...][consul-about]

Você também pode querer explorar o Service Mesh Interface (SMI), uma interface padrão para malhas de serviço em Kubernetes:

- [Interface de malha de serviço (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md