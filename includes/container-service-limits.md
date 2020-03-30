---
title: incluir arquivo
description: incluir arquivo
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335062"
---
| Recurso | Limite |
| --- | :--- |
| Clusters máximos por assinatura | 100 |
| Nómáximo por cluster com conjuntos de disponibilidade de máquina virtual e SKU do balanceador de carga básico  | 100 |
| Nómáximo por cluster com conjuntos de escala de máquina virtual e [Balancer de carga padrão SKU][standard-load-balancer] | 1000 (100 nodos por [piscina de nó)][node-pool] |
| Pods máximos por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Pods máximos por nó: [Rede avançada][advanced-networking] com interface de rede de contêineres Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Azure Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster Azure Kubernetes Service (AKS) com o azure CLI ou um modelo de Gerenciador de recursos, esse valor é configurável até 250 pods por nó. Você não pode configurar pods máximos por nó depois de já ter implantado um cluster AKS ou se você implantar um cluster usando o portal Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
