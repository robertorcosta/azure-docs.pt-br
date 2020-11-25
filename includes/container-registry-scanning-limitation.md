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
ms.openlocfilehash: a1a94c841632b576eccdfd93545c652b30975203
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028243"
---
> [!NOTE]
> A central de segurança do Azure não pode executar a [verificação de vulnerabilidade de imagem](../articles/security-center/defender-for-container-registries-introduction.md?bc=%252fazure%252fcontainer-registry%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fcontainer-registry%252ftoc.json) no momento em um registro que restringe o acesso a pontos de extremidade privados, sub-redes selecionadas ou endereços IP. Instâncias de determinados serviços do Azure, incluindo Azure DevOps Services e instâncias de contêiner do Azure, também não conseguem acessar um registro de contêiner de rede restrita.