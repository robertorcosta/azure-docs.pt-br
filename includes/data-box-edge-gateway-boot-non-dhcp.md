---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67172590"
---
Se você inicializar em um ambiente não-DHCP, siga essas etapas para implantar a máquina virtual para o Gateway da caixa de dados.

1. [Conecte-se à interface Windows PowerShell do dispositivo](#connect-to-the-powershell-interface).
2. Use `Get-HcsIpAddress` o cmdlet para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

    O exemplo a seguir mostra o uso deste cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Consulte o seguinte exemplo:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

