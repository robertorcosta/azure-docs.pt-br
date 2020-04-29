---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67172382"
---
1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsApplianceInfo` para obter as informações do seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Aqui está uma tabela que resume algumas das informações importantes do dispositivo:
    
    | Parâmetro                             | Descrição                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | O nome amigável do dispositivo, conforme configurado por meio da interface do usuário da Web local durante a implantação do dispositivo. O nome amigável padrão é o número de série do dispositivo.  |   |
    | SerialNumber                   | O número de série do dispositivo é um número exclusivo atribuído na fábrica.                                                                             |   |
    | Modelo                          | O modelo para seu Data Box Edge ou Gateway do Data Box dispositivo. O modelo é virtual para Gateway do Data Box e físico para Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | A cadeia de caracteres amigável que corresponde à versão do software do dispositivo. Para um sistema executando a visualização, a versão de software amigável seria Data Box Edge 1902. |   |
    | HcsVersion                     | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão do software HCS correspondente ao Data Box Edge 1902 é 1.4.771.324.            |   |
    | LocalCapacityInMb              | A capacidade local total do dispositivo em megabits.                                                                                                        |   |
    | IsRegistered                   | Esse valor indica se o dispositivo está ativado com o serviço.                                                                                         |   |


