---
title: Visão geral do Cônsul
description: Obtenha uma visão geral do Cônsul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594202"
---
# <a name="consul"></a>Cônsul

## <a name="overview"></a>Visão geral

[O Cônsul][consul] é uma solução de rede de serviços de reconhecimento de vários data centers para conectar e proteger serviços em plataformas de tempo de execução. [Connect][consul-features] é o componente que fornece recursos de malha de serviço.

## <a name="architecture"></a>Arquitetura

O Cônsul fornece um plano de dados que é composto por [sidecars baseados][consul-sidecar] em [Envoy][envoy-proxy]por padrão. O cônsul tem uma arquitetura proxy plugável. Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho.

O plano de controle gerencia a configuração e a diretiva através dos [seguintes componentes:][consul-architecture]

- **Servidor** - Um agente cônsul em execução no modo Servidor que mantém o estado de cluster do Cônsul.

- **Cliente** - Um agente cônsul em execução no modo cliente leve. Cada nó de computação deve ter um agente cliente funcionando. Este cliente intermedia configuração e política entre as cargas de trabalho e a configuração do Cônsul. 

O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e plano de controle interagem.

![Visão geral dos componentes e arquitetura do Cônsul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar o Cônsul para suas cargas de trabalho:

- [Princípios cônsul](#consul-principles)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="consul-principles"></a>Princípios cônsul

Os seguintes princípios [orientam][consul-principles] o projeto cônsul:

- **API-Driven** - Codificar toda a configuração e política.

- **Executar e conectar em qualquer lugar** - Conecte cargas de trabalho em plataformas de tempo de execução (Kubernetes, VMs, Serverless).

- **Estender e integrar** - conecte com segurança cargas de trabalho em infra-estrutura.


### <a name="capabilities"></a>Funcionalidades

O Cônsul fornece o seguinte conjunto de capacidades:

- **Malha** – gateway (multi data center), máquinas virtuais (fora dos nós de cluster), sincronização de serviço, opção de depuração incorporada

- **Proxies** – Proxy embutido, proxy pluggável, l4 disponível para cargas de trabalho do Windows

- **Gerenciamento de Tráfego** – roteamento, divisão, resolução

- **Política** – intenções, ACLs

- **Segurança** – autorização, autenticação, criptografia, identidades baseadas em SPIFFE, CA externa (Vault), gerenciamento de certificados e rotação

- **Observabilidade** – métricas, painel de ui, prometeu, grafana


### <a name="scenarios"></a>Cenários

O Cônsul é adequado e sugerido para os seguintes cenários:

- Ampliação das cargas de trabalho conectadas do Cônsul existentes

- Requisitos de conformidade em torno do gerenciamento de certificados

- Malha de serviço de vários clusters

- Cargas de trabalho baseadas em VM a serem incluídas na malha de serviço



## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o Cônsul no Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instale cônsul no Azure Kubernetes Service (AKS)][consul-install]

Você também pode explorar ainda mais os recursos e arquitetura do Cônsul:

- [Características do Cônsul][consul-features]
- [Arquitetura Cônsul][consul-architecture]
- [Cônsul - Como funciona o Connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
