---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67172384"
---
Dependendo do sistema operacional do cliente, os procedimentos para se conectar remotamente ao dispositivo são diferentes.

### <a name="remotely-connect-from-a-windows-client"></a>Conecte-se remotamente a partir de um cliente Windows

Antes de começar, certifique-se de que seu cliente Windows esteja executando o Windows PowerShell 5.0 ou posterior.

Siga essas etapas para se conectar remotamente a partir de um cliente do Windows.

1. Execute uma sessão do Windows PowerShell como administrador.
2. Certifique-se de que o serviço de gerenciamento remoto do Windows está sendo executado em seu cliente. No prompt de comando, digite:

    `winrm quickconfig`

3. Atribuir uma variável ao endereço IP do dispositivo.

    $ip = "<device_ip>"

    Substitua pelo `<device_ip>` endereço IP do seu dispositivo.

4. Para adicionar o endereço IP do seu dispositivo à lista de hosts confiáveis do cliente, digite o seguinte comando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Inicie uma sessão do Windows PowerShell no dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Forneça a senha quando solicitado. Use a mesma senha usada para entrar na ui local. A senha padrão da Web UI local é *Password1*. Quando você se conecta com sucesso ao dispositivo usando o PowerShell remoto, você verá a seguinte saída de amostra:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Conecte-se remotamente a partir de um cliente Linux

No cliente Linux que você usará para conectar:

- [Instale o mais recente PowerShell Core para Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) do GitHub para obter o recurso de remoção SSH. 
- [Instale `gss-ntlmssp` apenas o pacote do módulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Para clientes Ubuntu, use o seguinte comando:
    - `sudo apt-get install gss-ntlmssp`

Para obter mais informações, vá para [PowerShell remoting over SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Siga essas etapas para se conectar remotamente a partir de um cliente NFS.

1. Para abrir a sessão PowerShell, digite:

    `sudo pwsh`
 
2. Para conectar usando o cliente remoto, digite:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando solicitado, forneça a senha usada para entrar no seu dispositivo.
 
> [!NOTE]
> Este procedimento não funciona no Mac OS.
