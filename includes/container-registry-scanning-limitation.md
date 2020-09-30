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
ms.openlocfilehash: e3234289fe56286cb5ff0791d983dee6ec2aa4d8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544859"
---
> [!NOTE]
> A central de segurança do Azure não pode executar a [verificação de vulnerabilidade de imagem](../articles/security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) no momento em um registro que restringe o acesso a pontos de extremidade privados, sub-redes selecionadas ou endereços IP. Instâncias de determinados serviços do Azure, incluindo Azure DevOps Services e instâncias de contêiner do Azure, também não conseguem acessar um registro de contêiner de rede restrita.