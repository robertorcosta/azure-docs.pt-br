---
title: incluir arquivo
description: incluir arquivo
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205701"
---
Adicione suporte em ambiente de desenvolvimento preferido usando os seguintes métodos.

| Ambiente de desenvolvimento  | Tipo de aplicativo      | Para adicionar suporte |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Biblioteca de classe C#      | [Instale o pacote NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Baseado em [ferramentas principais](../articles/azure-functions/functions-run-local.md) | [Registre o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Recomenda-se instalar [a extensão Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) |
| Qualquer outro editor/IDE     | Baseado em [ferramentas principais](../articles/azure-functions/functions-run-local.md) | [Registre o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portal do Azure             | Online apenas no portal | Instala ao adicionar uma vinculação<br /><br /> Consulte [Atualize suas extensões](../articles/azure-functions/install-update-binding-extensions-manual.md) para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função. |
