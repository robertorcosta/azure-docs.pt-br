---
author: baanders
description: arquivo de inclusão para DefaultAzureCredential no Azure digital gêmeos Samples-Note
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211768"
---
>[!NOTE]
> Quando você o executa em seu computador local, este exemplo usa o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da biblioteca `Azure.Identity`) para autenticar usuários com uma instância dos Gêmeos Digitais do Azure. Com esse tipo de autenticação, o exemplo pesquisará as credenciais do Azure no seu ambiente local, como um logon de um [CLI do Azure](/cli/azure/install-azure-cli) local ou no Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre `DefaultAzureCredential` como usar o e outras opções de autenticação, consulte [*como escrever o código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).