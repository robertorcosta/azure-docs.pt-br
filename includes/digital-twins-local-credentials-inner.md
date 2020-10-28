---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – sem introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494658"
---
Com `DefaultAzureCredential`, o exemplo pesquisará as credenciais no ambiente local, como um logon do Azure em uma [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou no Visual Studio/Visual Studio Code. Isso significa que será necessário **fazer logon no Azure localmente** por meio de um desses mecanismos para configurar as credenciais do exemplo.

Caso esteja usando o Visual Studio ou o Visual Studio Code para executar um exemplo de código, verifique se você está conectado nesse editor com as mesmas credenciais do Azure que deseja usar para acessar a instância dos Gêmeos Digitais do Azure.

Caso contrário, será possível [instalar a **CLI do Azure** local](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), iniciar um prompt de comando no computador e executar o comando `az login` para fazer logon em sua conta do Azure. Depois disso, ao executar o exemplo de código, ele deverá fazer logon de modo automático.