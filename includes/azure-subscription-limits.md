---
title: arquivo de inclusão
description: incluir arquivo
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: db822722b12921ab98b3e5cae67e28f4ca7ede04
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298718"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| vCPUs por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10.000 |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |200 |200 |
| [Contas de armazenamento](../articles/storage/common/storage-create-storage-account.md) por assinatura<sup>2</sup> |100 |100 |
| [Serviços de nuvem](../articles/cloud-services/cloud-services-choose-me.md) por assinatura |20 |200 |
| [Redes locais](/previous-versions/azure/reference/jj157100(v=azure.100)) por assinatura |10 |500 |
| Servidores DNS por assinatura |9 |100 |
| IPs reservados por assinatura |20 |100 |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por assinatura |256 |256 |
| Comprimento do nome da assinatura (caracteres) | 64 | 64 |

<sup>1</sup>Instâncias muito pequenas contam como um vCPU para o limite de vCPU, apesar de usarem um núcleo de CPU parcial.

<sup>2</sup>O limite da conta de armazenamento inclui contas de armazenamento Standard e Premium. 

