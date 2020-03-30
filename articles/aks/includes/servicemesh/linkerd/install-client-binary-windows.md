---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593693"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixe e instale o linkerd linkerd client binário

Em um shell baseado em PowerShell no Windows, use `Invoke-WebRequest` para baixar a versão do Linkerd da seguinte forma:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

O `linkerd` binário do cliente é executado na sua máquina cliente e permite que você interaja com a malha de serviço linkerd. Use os seguintes comandos para `linkerd` instalar o binário do cliente Linkerd em um shell baseado em PowerShell no Windows. Esses comandos `linkerd` copiam o binário do cliente para uma pasta Linkerd e, em seguida, disponibilizam-no imediatamente (no shell atual) e permanentemente (através de reinicializações de shell) através do seu `PATH`. Você não precisa de privilégios elevados (admin) para executar esses comandos e você não precisa reiniciar seu shell.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```