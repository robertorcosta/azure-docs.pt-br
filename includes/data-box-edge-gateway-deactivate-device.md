---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129104"
---
Para redefinir seu dispositivo, você precisa eliminar com segurança todos os dados do disco de dados e do disco de inicialização do seu dispositivo. 

Use `Reset-HcsAppliance` o cmdlet para eliminar os discos de dados e o disco de inicialização ou apenas os discos de dados. Os `ClearData` `BootDisk` switches permitem que você limpe os discos de dados e o disco de inicialização, respectivamente.

O `BootDisk` interruptor limpa o disco de inicialização e torna o dispositivo inutilizável. Ele deve ser usado apenas quando o dispositivo precisar ser devolvido à Microsoft. Para obter mais informações, consulte [Retornar o dispositivo à Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se você usar o dispositivo redefinido na ui web local, apenas os discos de dados serão apagados com segurança, mas o disco de inicialização é mantido intacto. O disco de inicialização contém a configuração do dispositivo.

1. [Conecte-se à interface PowerShell](#connect-to-the-powershell-interface).
2. No prompt de comando, digite:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O exemplo a seguir mostra como usar este cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
