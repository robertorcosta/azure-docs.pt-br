---
title: Visão geral de Istio
description: Obtenha uma visão geral de Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593893"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Visão geral

[Istio][istio] é uma malha de serviço completa, personalizável e extensível.

## <a name="architecture"></a>Arquitetura

Istio fornece um plano de dados que é composto por sidecars baseados em [Envoy.][envoy-proxy] Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho.

O plano de controle gerencia a configuração, a política e a telemetria através dos [seguintes componentes:][what-is-istio]

- **Mixer** - Impõe políticas de controle de acesso e uso. Coleta telemetria dos proxies que são empurrados [para Prometheus][prometheus].

- **Piloto** - Fornece política/configuração de detecção de serviços e gerenciamento de tráfego para os proxies.

- **Citadel** - Fornece recursos de identidade e segurança que permitem o mTLS entre os serviços.

- **Galera** - Abstratos e fornece configuração para componentes.

O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e plano de controle interagem.


![Visão geral dos componentes e arquitetura do Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar istio para suas cargas de trabalho:

- [Metas de design](#design-goals)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-goals"></a>Metas de design

As seguintes metas de design [guiam][design-goals] o projeto Istio:

- **Maximizar a transparência** - Permitir a adoção com a quantidade mínima de trabalho para obter valor real do sistema.

- **Extensibilidade** - Deve ser capaz de crescer e se adaptar com as necessidades em mudança.

- **Portabilidade** - Funcionar facilmente em diferentes tipos de ambientes - nuvem, no local.

- **Uniformidade da Política** - Consistência na definição de políticas entre a variedade de recursos.


### <a name="capabilities"></a>Funcionalidades

A Istio fornece o seguinte conjunto de recursos:

- **Malha** – gateways (multicluster), máquinas virtuais (expansão de malha)

- **Gerenciamento de Tráfego** – roteamento, divisão, intervalos, disjuntores, repetições, ingress, saída

- **Política** – controle de acesso, limite de taxa, cota, adaptadores de políticas personalizadas

- **Segurança** – autenticação (jwt), autorização, criptografia (mTLS), CA externa (HashiCorp Vault)

- **Observabilidade** – métricas douradas, espelho, rastreamento, adaptadores personalizados, prometeu, grafana

### <a name="scenarios"></a>Cenários

Istio é bem adequado e sugerido para os seguintes cenários:

- Requerem extensibilidade e rico conjunto de capacidades

- Expansão de malha para incluir cargas de trabalho baseadas em VM

- Malha de serviço de vários clusters

## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o Istio no Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalar Istio no AKS (Serviço de Kubernetes do Azure)][istio-install]

Você também pode explorar ainda mais os conceitos de Istio e modelos adicionais de implantação:

- [Conceitos istio][what-is-istio]
- [Modelos de implantação de Istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
