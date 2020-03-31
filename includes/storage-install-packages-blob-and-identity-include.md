---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806568"
---
## <a name="install-client-library-packages"></a>Instale pacotes de biblioteca de clientes

> [!NOTE]
> Os exemplos mostrados aqui usam a biblioteca cliente do Azure Storage versão 12. A biblioteca de clientes versão 12 faz parte do Azure SDK. Para obter mais informações sobre o Azure SDK, consulte o repositório Azure SDK no [GitHub](https://github.com/Azure/azure-sdk).

Para instalar o pacote de armazenamento Blob, execute o seguinte comando do console do gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Os exemplos mostrados aqui também usam a versão mais recente da [biblioteca de clientes Azure Identity para .NET](https://www.nuget.org/packages/Azure.Identity/) para autenticar com credenciais Azure AD. Para instalar o pacote, execute o seguinte comando no console do gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Identity
```
