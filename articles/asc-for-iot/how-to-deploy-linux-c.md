---
title: Instale & implantar o agente Linux C
description: Aprenda a instalar o Azure Security Center para agente IoT em Linux de 32 bits e 64 bits.
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
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311199"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar um agente de segurança baseado em C da Central de Segurança do Azure para IoT para Linux

Este guia explica como instalar e implantar o Azure Security Center para agente de segurança baseado em IoT C no Linux.

Neste guia, você aprenderá a:

> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de admin locais na máquina que você deseja instalar (sudo).

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar e implantar o agente de segurança, use o seguinte fluxo de trabalho:

1. Baixe a versão mais recente para sua máquina no [GitHub](https://aka.ms/iot-security-github-c).

1. Extrair o conteúdo do pacote e navegar até a pasta _/src/instalação._

1. Adicione permissões em execução ao **script InstallSecurityAgent** executando o seguinte comando:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Veja [Como configurar a autenticação](concept-security-agent-authentication-methods.md) para mais informações sobre parâmetros de autenticação.

Este script executa a seguinte função:

1. Instala pré-requisitos.

1. Adiciona um usuário de serviço (com sinal interativo desativado).

1. Instala o agente como um **Daemon** – pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

1. Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro –ajuda:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –-desinstalar:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Solução de problemas

Verifique o status da implantação executando:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Próximas etapas

- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia a [FAQ](resources-frequently-asked-questions.md)
- Entenda os [Alertas de Segurança](concept-security-alerts.md)
