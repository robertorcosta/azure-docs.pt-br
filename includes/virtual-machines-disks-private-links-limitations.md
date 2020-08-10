---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420271"
---
- Somente uma rede virtual pode ser vinculada a um objeto de acesso a disco.
- Sua rede virtual precisa estar na mesma assinatura do objeto de acesso a disco para que o vínculo seja estabelecido.
- Até dez discos ou instantâneos podem ser importados ou exportados simultaneamente com o mesmo objeto de acesso a disco.
- Não é possível solicitar a aprovação manual para vincular uma rede virtual a um objeto de acesso a disco.
- Não há suporte para a capacidade diferencial em instantâneos incrementais associados a um objeto de acesso a disco.
- Você não pode usar o AzCopy para baixar o VHD de um disco/instantâneo que é protegido por meio de links privados para uma conta de armazenamento. No entanto, você pode usar o AzCopy para baixar o VHD para as suas VMs.
