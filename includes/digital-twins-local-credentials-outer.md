---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – com introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329599"
---
### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

Quando você o executa em seu computador local, este exemplo usa o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da biblioteca `Azure.Identity`) para autenticar usuários com uma instância dos Gêmeos Digitais do Azure. Para obter mais informações sobre diferentes maneiras que um aplicativo cliente pode se autenticar com os Gêmeos Digitais do Azure, confira [*Instruções: como gravar um código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]