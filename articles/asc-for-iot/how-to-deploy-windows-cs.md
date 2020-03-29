---
title: Instalação do Windows do Azure Security Center para agente de IoT | Microsoft Docs
description: Saiba como instalar o Azure Security Center para agente IoT em dispositivos Windows de 32 ou 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597220"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Implantar um agente de segurança baseado em C# da Central de Segurança do Azure para IoT para Windows

Este guia explica como instalar o Azure Security Center para agente de segurança baseado em IoT C#no Windows.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar o
> * Verificar implantação
> * Desinstalar o agente
> * Solução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo](how-to-deploy-agent.md).

1. Direitos de admin locais na máquina que você deseja instalar. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar o agente de segurança, use o seguinte fluxo de trabalho:

1. Instale o Azure Security Center para o agente IoT Windows C# no dispositivo. Baixe a versão mais recente para sua máquina no Azure Security Center para [o repositório IoT GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Extraia o conteúdo do pacote e navegue até a pasta /Install.

1. Abra o Windows PowerShell como administrador. 
1. Adicione permissões em execução ao script InstallSecurityAgent executando:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    em seguida, executar:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Por exemplo: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Para obter mais informações sobre parâmetros de autenticação, consulte [Como configurar a autenticação](concept-security-agent-authentication-methods.md).

Este script faz as seguintes ações:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com sinal interativo desativado).

- Instala o agente como um **Serviço do Sistema**.

- Configura o agente com os parâmetros de autenticação fornecidos.


Para obter ajuda adicional, use o comando Get-Help no PowerShell <br>Exemplo de Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o status da implantação

- Verifique o status da implantação do agente executando:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script do PowerShell com o parâmetro **-mode** definido como **Uninstall**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Solução de problemas

Se o agente não conseguir iniciar, ative o log (por padrão, o log fica *desativado*) para obter mais informações.

Para ativar o log:

1. Abra o arquivo de configuração (General.config) para edição usando um editor de arquivos padrão.

1. Edite os valores a seguir:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > É recomendável deixar o registro em log **desativado** após a conclusão da solução de problemas. Deixar o log **ativado** aumenta o tamanho do arquivo de log e o uso de dados. 

1. Reinicie o agente executando o seguinte PowerShell ou a linha de comando:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   ou

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Examine o arquivo de log para obter mais informações sobre a falha.

   Localização do arquivo de log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Próximas etapas
- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia a [FAQ](resources-frequently-asked-questions.md)
- Entenda os [alertas](concept-security-alerts.md)