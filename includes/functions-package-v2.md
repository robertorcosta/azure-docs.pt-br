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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205701"
---
Adicione suporte no ambiente de desenvolvimento preferencial usando os métodos a seguir.

| Ambiente de desenvolvimento  | Tipo de aplicativo      | Para adicionar suporte |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#biblioteca de classes      | [Instalar o pacote NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Com base nas [ferramentas principais](../articles/azure-functions/functions-run-local.md) | [Registrar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>É recomendável instalar a [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Qualquer outro editor/IDE     | Com base nas [ferramentas principais](../articles/azure-functions/functions-run-local.md) | [Registrar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portal do Azure             | Online somente no portal | Instala ao adicionar uma associação<br /><br /> Consulte [atualizar suas extensões](../articles/azure-functions/install-update-binding-extensions-manual.md) para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções. |
