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
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376747"
---
- Somente uma rede virtual pode ser vinculada a um objeto de acesso a disco.
- Sua rede virtual precisa estar na mesma assinatura do objeto de acesso a disco para que o vínculo seja estabelecido.
- Até dez discos ou instantâneos podem ser importados ou exportados simultaneamente com o mesmo objeto de acesso a disco.
- Não é possível solicitar a aprovação manual para vincular uma rede virtual a um objeto de acesso a disco.
- Instantâneos incrementais não podem ser exportados quando estão associados a um objeto de acesso ao disco.
- Você não pode usar o AzCopy para baixar o VHD de um disco/instantâneo que é protegido por meio de links privados para uma conta de armazenamento. No entanto, você pode usar o AzCopy para baixar o VHD para as suas VMs.
