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
ms.openlocfilehash: 8e4c1c05b133f85b7473c3ce97c9f509ac8f7d37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299135"
---
- Os instantâneos incrementais atualmente não podem ser movidos entre assinaturas.
- Atualmente, você só pode gerar URIs SAS de até cinco instantâneos de uma família de instantâneos em particular a qualquer momento.
- Não é possível criar um instantâneo incremental para um determinado disco fora da assinatura desse disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.