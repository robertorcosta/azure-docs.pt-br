---
title: arquivo de inclusão
description: incluir arquivo
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559200"
---
| Recurso | Limite |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Máximo de nós por cluster com conjuntos de disponibilidade de máquinas virtuais e SKU de Load Balancer Básico  | 100 |
| Máximo de nós por cluster com Conjuntos de Dimensionamento de Máquinas Virtuais e [SKU do Standard Load Balancer][standard-load-balancer] | 1000 (100 nós por [pool de nós][node-pool]) |
| Máximo de pods por nó: [rede básica][basic-networking] com Kubenet | 110 |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com a Interface de Rede de Contêiner do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Azure Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup>Quando você implanta um cluster do AKS (Serviço de Kubernetes do Azure) com a CLI do Azure ou com um modelo do Resource Manager esse valor pode ser configurado para até 250 pods por nó. Não será possível configurar o máximo de pods por nó após implantar um cluster do AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest