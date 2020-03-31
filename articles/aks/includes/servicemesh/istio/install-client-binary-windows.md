---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593911"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixe e instale o binário do cliente Istio istioctl

Em um shell baseado em PowerShell no Windows, use `Invoke-WebRequest` para `Expand-Archive` baixar a versão istio e, em seguida, extrair com o seguinte:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

O `istioctl` binário do cliente é executado na sua máquina cliente e permite que você interaja com a malha de serviço istio. Use os seguintes comandos `istioctl` para instalar o binário do cliente Istio em um shell baseado em PowerShell no Windows. Esses comandos `istioctl` copiam o binário do cliente para uma pasta Istio e, em seguida, disponibilizam-no imediatamente (no shell atual) e permanentemente (através de reinicializações de shell) através do seu `PATH`. Você não precisa de privilégios elevados (admin) para executar esses comandos e você não precisa reiniciar seu shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```