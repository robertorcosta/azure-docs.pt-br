---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – sem introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329571"
---
Com `DefaultAzureCredential`, o exemplo pesquisará as credenciais no ambiente local, como uma conexão do Azure em uma [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou no Visual Studio ou Visual Studio Code. Por isso, será necessário *entrar no Azure localmente* por meio de um desses mecanismos para configurar as credenciais do exemplo.

Caso esteja usando o Visual Studio ou o Visual Studio Code para executar um exemplo de código, verifique se você está conectado nesse editor com as mesmas credenciais do Azure que deseja usar para acessar a instância dos Gêmeos Digitais do Azure.

Caso contrário, você poderá [instalar a CLI do Azure local](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), iniciar um prompt de comando no computador e executar o comando `az login` para entrar em sua conta do Azure. Depois de entrar, ao executar o exemplo de código, ele deverá conectar de modo automático.