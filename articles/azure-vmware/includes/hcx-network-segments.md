---
title: Segmentos de rede do HCX da VMware
description: Há quatro redes necessárias para o HCX da VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173636"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Quatro redes são necessárias para o VMware HCX:

- **Rede de gerenciamento:** Normalmente é a mesma rede de gerenciamento usada no cluster vSphere. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. (Talvez você precise de números maiores, dependendo da implantação.)

- **Rede do vMotion:** Normalmente é a mesma rede de usada para o vMotion no cluster vSphere.  No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. (Talvez você precise de números maiores, dependendo da implantação.)  

   A rede do vMotion deve ser exposta em um comutador virtual distribuído ou no vSwitch0. Se não estiver, modifique o ambiente.

   > [!NOTE]
   > Se essa rede não estiver roteada (privada), isso não será um problema.

- **Rede de uplink:** É interessante criar uma rede para o uplink do HCX da VMware e estendê-la para o cluster vSphere por meio de um grupo de portas. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. (Talvez você precise de números maiores, dependendo da implantação.)  

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede resolverá o problema.

- **Rede de replicação:** É interessante criar uma rede para a Replicação do HCX da VMware e estender essa rede para o cluster vSphere por meio de um grupo de portas. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. (Talvez você precise de números maiores, dependendo da implantação.)

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede resolverá o problema.