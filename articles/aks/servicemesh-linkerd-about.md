---
title: Visão geral do Linkerd
description: Obtenha uma visão geral do Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593760"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Visão geral

[Linkerd][linkerd] é uma malha de serviço fácil de usar e leve.

## <a name="architecture"></a>Arquitetura

O Linkerd fornece um plano de dados composto por sidecars proxy especializados ultraleves [Linkerd.][linkerd-proxy] Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho. Os proxies também expõem métricas através de pontos finais de métricas [Prometheus.][prometheus]

O plano de controle gerencia a configuração e a telemetria agregada através dos [seguintes componentes:][linkerd-architecture]

- **Controlador** - Fornece api que aciona o Linkerd CLI e o Dashboard. Fornece configuração para proxies.

- **Toque** - Estabeleça relógios em tempo real em solicitações e respostas.

- **Identidade** - Fornece recursos de identidade e segurança que permitem o mTLS entre os serviços.

- **Web** - Fornece o painel Linkerd.


O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e plano de controle interagem.


![Visão geral dos componentes e arquitetura do Linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar o Linkerd para suas cargas de trabalho:

- [Princípios de Design](#design-principles)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-principles"></a>Princípios de design

Os seguintes princípios de design [guiam][design-principles] o projeto Linkerd:

- **Mantenha-o simples** - Deve ser fácil de usar e entender.

- **Minimizar os requisitos de recursos** - Impor desempenho mínimo e custo de recursos.

- **Apenas Trabalhe** - Não quebre os aplicativos existentes e não exija configuração complexa.


### <a name="capabilities"></a>Funcionalidades

O Linkerd fornece o seguinte conjunto de recursos:

- **Malha** – opção de depuração construída

- **Gerenciamento de Tráfego** – divisão, intervalos, repetições, ingress

- **Segurança** – criptografia (mTLS), certificados rodados automaticamente a cada 24 horas

- **Observabilidade** – métricas douradas, toque, rastreamento, perfis de serviço e métricas por rota, painel web com gráficos de topologia, prometeu, grafana


### <a name="scenarios"></a>Cenários

Linkerd é bem adequado e sugerido para os seguintes cenários:

- Simples de usar com apenas o conjunto essencial de requisitos de capacidade

- Baixa latência, baixa sobrecarga, com foco em observância e gestão simples do tráfego


## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o Linkerd no Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instale linkerd no Azure Kubernetes Service (AKS)][linkerd-install]

Você também pode explorar ainda mais os recursos e arquitetura do Linkerd:

- [Características do Linkerd][linkerd-features]
- [Arquitetura Linkerd][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md