---
title: Segmentos de rede do HCX da VMware
description: Há quatro redes necessárias para o HCX da VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578611"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Há quatro redes necessárias para o HCX da VMware:

- **Rede de Gerenciamento:** Normalmente é a mesma rede de gerenciamento usada no cluster vSphere.  No mínimo, identifique dois IPs nesse segmento de rede para o HCX da VMware (você pode precisar de mais IPs, dependendo da sua implantação).

- **Rede do vMotion:** Normalmente é a mesma rede de usada para o vMotion no cluster vSphere.  No mínimo, identifique dois IPs nesse segmento de rede para o HCX da VMware (você pode precisar de mais IPs, dependendo da sua implantação).  

   A rede do vMotion deve ser exposta em um comutador virtual distribuído ou no vSwitch0. Se não estiver, modifique o ambiente.

   > [!NOTE]
   > Se essa rede não está roteada (privada), não tem problema.

- **Rede de Uplink:** É interessante criar uma rede para o uplink do HCX da VMware e estendê-la para o cluster vSphere por meio de um grupo de portas.  No mínimo, identifique dois IPs nesse segmento de rede para o HCX da VMware (você pode precisar de mais IPs, dependendo da sua implantação).  

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede resolverá o problema.

- **Rede de replicação:** É interessante criar uma rede para a Replicação do HCX da VMware e estender essa rede para o cluster vSphere por meio de um grupo de portas.  No mínimo, identifique dois IPs nesse segmento de rede para o HCX da VMware (você pode precisar de mais IPs, dependendo da sua implantação).

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede resolverá o problema.