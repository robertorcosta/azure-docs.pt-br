---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/17/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9528fa3b434a0bc09d191fe6808214feb8245ae4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509172"
---
> [!NOTE]
> A central de segurança do Azure não pode executar a [verificação de vulnerabilidade de imagem](../articles/security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) no momento em um registro que restringe o acesso a pontos de extremidade privados, sub-redes selecionadas ou endereços IP. Instâncias de determinados serviços do Azure, incluindo Azure DevOps Services e instâncias de contêiner do Azure, também não conseguem acessar um registro de contêiner de rede restrita.