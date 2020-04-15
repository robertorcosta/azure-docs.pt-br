---
title: Instalar & implantar o agente Linux C#
description: Aprenda a instalar o Azure Security Center para agente IoT em Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311139"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar um agente de segurança baseado em C# da Central de Segurança do Azure para IoT para Linux

Este guia explica como instalar e implantar o Azure Security Center para agente de segurança baseado em IoT C#no Linux.

Neste guia, você aprenderá a:

> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar.

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para implantar o agente de segurança, use as seguintes etapas:

1. Baixe a versão mais recente para sua máquina no [GitHub](https://aka.ms/iot-security-github-cs).

1. Extrair o conteúdo do pacote e navegar até a pasta _/Install._

1. Adicione permissões de execução para o **script InstallSecurityAgent** executando `chmod +x InstallSecurityAgent.sh`

1. Em seguida, execute o seguinte comando com **privilégios de raiz:**

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   para obter mais informações sobre parâmetros de autenticação, consulte [Como configurar a autenticação](concept-security-agent-authentication-methods.md).

O script executa as ações a seguir:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com sinal interativo desativado).

- Instala o agente como um **Daemon** - assume que o dispositivo usa **sistemapara** modelo clássico de implantação.

- Configura **sudoers** para permitir que o agente faça certas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro –ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –u: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> A desinstalação não remove todos os pré-requisitos ausentes que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Solução de problemas

1. Verifique o status da implantação executando:

    `systemctl status ASCIoTAgent.service`

1. Habilitar o registro em log.
   Se o agente não conseguir iniciar, ative o log para obter mais informações.

   Ative o registro em log por:

   1. Abra o arquivo de configuração para edição em qualquer editor de Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite os valores a seguir:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       O valor **logFilePath** é configurável.

       > [!NOTE]
       > É recomendável deixar o registro em log **desativado** após a conclusão da solução de problemas. Deixar o log **ativado** aumenta o tamanho do arquivo de log e o uso de dados.

   1. Reinicie o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Veja o arquivo de log para obter mais informações sobre a falha.

       O local do arquivo de log é: `/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho do local do arquivo de acordo com o nome que você escolheu para o **logFilePath** na etapa 2.

## <a name="next-steps"></a>Próximas etapas

- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia a [FAQ](resources-frequently-asked-questions.md)
- Entenda os [alertas](concept-security-alerts.md)
