---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129104"
---
Para redefinir o dispositivo, você precisa apagar com segurança todos os dados no disco de dados e no disco de inicialização do dispositivo. 

Use o cmdlet `Reset-HcsAppliance` para apagar os discos de dados e o disco de inicialização ou apenas os discos de dados. As opções `ClearData` e `BootDisk` permitem apagar os discos de dados e o disco de inicialização, respectivamente.

A opção `BootDisk` apaga o disco de inicialização e torna o dispositivo inutilizável. Ela deverá ser usada somente quando o dispositivo precisar ser devolvido à Microsoft. Para obter mais informações, confira [Devolver o dispositivo à Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se você usar a redefinição do dispositivo na IU da Web local, somente os discos de dados serão apagados com segurança, mas o disco de inicialização permanecerá intacto. O disco de inicialização contém a configuração do dispositivo.

1. [Conectar-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. No prompt de comando, digite:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O seguinte exemplo mostra como usar este cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
