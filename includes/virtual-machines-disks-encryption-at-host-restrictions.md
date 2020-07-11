---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230940"
---
- O não oferece suporte a ultra discos.
- Não poderá ser habilitado se Azure Disk Encryption (criptografia de VM convidada usando BitLocker/VM-Decrypt) estiver habilitada em seus conjuntos de dimensionamento de VMs/máquinas virtuais.
- Azure Disk Encryption não pode ser habilitada em discos que têm criptografia no host habilitado.
- A criptografia pode ser habilitada no conjunto de dimensionamento de máquinas virtuais existente. No entanto, somente as novas VMs criadas após a habilitação da criptografia são criptografadas automaticamente.
- As VMs existentes devem ser desalocadas e realocadas para serem criptografadas.