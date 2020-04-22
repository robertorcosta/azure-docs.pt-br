---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734102"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```