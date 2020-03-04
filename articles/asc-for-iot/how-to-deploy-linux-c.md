---
title: Guia para instalar e implantar o agente C do Linux da central de segurança do Azure para o agente de IoT | Microsoft Docs
description: Saiba como instalar a central de segurança do Azure para agente de IoT em Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812736"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar a central de segurança do Azure para o agente de segurança baseado em IoT C para Linux

Este guia explica como instalar e implantar a central de segurança do Azure para o agente de segurança baseado em IoT C no Linux.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solução de problemas 

## <a name="prerequisites"></a>Prerequisites

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar o (sudo).

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar e implantar o agente de segurança, use o seguinte fluxo de trabalho:


1. Baixe a versão mais recente em seu computador do [GitHub](https://aka.ms/iot-security-github-c).

1. Extraia o conteúdo do pacote e navegue até a pasta _/src/Installation_

1. Adicione permissões em execução ao **script InstallSecurityAgent** executando o seguinte comando:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Veja [Como configurar a autenticação](concept-security-agent-authentication-methods.md) para mais informações sobre parâmetros de autenticação.

Esse script executa a seguinte função:

1. Instala pré-requisitos.

2. Adiciona um usuário de serviço (com logon interativo desabilitado).

3. Instala o agente como um **Daemon** – pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

4. Configura o agente com os parâmetros de autenticação fornecidos. 

Para obter ajuda adicional, execute o script com o parâmetro –ajuda: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –-Uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Solução de problemas
Verifique o status da implantação executando:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Próximas etapas
- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [Perguntas frequentes](resources-frequently-asked-questions.md)
- Entenda os [Alertas de Segurança](concept-security-alerts.md)
