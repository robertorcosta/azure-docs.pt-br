---
title: Atualize o agente Microsoft Azure Recovery Services (MARS)
description: Saiba como atualizar o agente Mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672840"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Atualize o agente Microsoft Azure Recovery Services (MARS)

Neste artigo, você aprenderá a:

* Identificar servidores com versões anteriores do agente MARS
* Atualizar instalações MARS nesses servidores

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identificar servidores com versões anteriores do agente MARS

Para instalações do agente de backup do Azure e do servidor de backup do Azure:

1. Navegue até o Cofre de Serviços de Recuperação, onde você tem servidores registrados que estão potencialmente sendo apoiados por versões mais antigas do agente. Você pode encontrar uma lista representativa de cofres com agentes de backup mais antigos do Azure nos alertas de atualização de backup do Azure do Azure.
1. Na seção **Configurações** do lado esquerdo do Cofre de Serviços de Recuperação, selecione **Infra-estrutura de backup** na seção **Gerenciar.**
1. Para descobrir os agentes de backup do Azure instalados como parte das instalações do servidor de backup do Azure, vá para **servidores de gerenciamento de backup** em servidores de **gerenciamento**. Isso listará os servidores que possuem instalações de servidores de backup do Azure, juntamente com o número da versão do agente de backup azure associado.

    ![Lista de agentes MARS instalados como parte das instalações do servidor Azure Backup](./media/upgrade-mars-agent/backup-management-servers.png)

1. Para verificar as versões do agente para as instalações do agente Mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft) ou para o agente de backup do Azure, vá para **servidores protegidos** em **servidores de gerenciamento**. Em seguida, selecione **o agente de backup do Azure** em Tipo de gerenciamento de backup. Isso listará os servidores que possuem instalações de agentes de backup do Azure, juntamente com o número da versão para a instalação.

    ![Lista de servidores com o agente MARS instalado](./media/upgrade-mars-agent/protected-servers.png)

1. Classifique a coluna de versão do Azure Backup Agent clicando na coluna **Versão do agente** para instalações do AGENTE MARS ou na coluna **Azure Backup Agent Version** para instalações do servidor de backup do Azure.

1. A etapa anterior lhe dará a lista de servidores com agentes de backup do Azure que tenham versões inferiores a 2.0.9083.0 ou versões do agente listadas como em branco. Estes são os servidores onde os agentes de backup do Azure precisam ser atualizados.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Atualize a instalação do agente MARS no servidor

Depois de identificar os servidores que precisam de uma atualização do agente de backup do Azure, execute as seguintes etapas para cada servidor identificado (usando o servidor de backup do Azure ou o Agente MARS). [Baixe a versão mais recente do agente de backup do Azure](https://aka.ms/azurebackup_agent) antes de seguir as etapas abaixo.

1. Clique em uma linha que tenha o agente de backup do Azure menor que 2.0.9083.0 ou em branco. Isso abrirá a tela de detalhes do servidor.

    ![Servidores protegidos com versões de agentes desatualizadas](./media/upgrade-mars-agent/old-agent-version.png)

    ![Servidores de backup do Azure com versões deagentes desatualizadas](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Clique em **Conectar-se** para receber um arquivo de conexão de desktop remoto para se conectar com o servidor ou conectar-se diretamente ao servidor através da conexão de desktop remoto em seu servidor.

    ![Conecte-se ao servidor através de conexão remota da área de trabalho](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se o servidor listado não existir ou tiver sido desativado, você pode ignorar as etapas restantes abaixo e pular para o próximo servidor.

1. Insira seus dados de login administrativo e faça login.

1. Se o proxy do servidor ou servidor tiver acesso limitado à Internet, certifique-se de que as configurações de firewall no servidor/proxy sejam configuradas para permitir que a URL seja apropriada à nuvem DoZure que você está usando:

    Nuvem do Azure | URL
    -- | ---
    Nuvem Azure (Público) |   `https://login.windows.net`
    Nuvem Azure China 21Vianet   | `https://login.chinacloudapi.cn`
    Nuvem do governo dos EUA |   `https://login.microsoftonline.us`
    Nuvem Alemã do Azure  |  `https://login.microsoftonline.de`

1. Copie o instalador de atualização do agente de backup do Azure para o servidor.

    ![Copiar instalador de atualização do agente de backup do Azure para o servidor](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Execute o instalador. O assistente de atualização do agente de serviços de recuperação do Microsoft Azure é aberto.

    ![O assistente de atualização do agente de recuperação do Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Clique em **Avançar**.

1. Clique em **Atualizar**.

    ![Instalação do Microsoft Azure Recovery Services Agent](./media/upgrade-mars-agent/upgrade-installation.png)

1. A tela de confirmação final indica que o agente de backup do Azure foi atualizado com sucesso.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Para clientes do System Center Data Protection Manager (SC DPM)

Se você instalou agentes de backup do Azure nos servidores SC DPM (System Center Data Protection Manager, gerenciador de dados) do System Center, então você precisa seguir as etapas abaixo para identificar se seus servidores DPM precisam de uma atualização do agente de backup do Azure:

1. Faça login no servidor SC DPM como administrador.
2. Abra o Console DPM.
3. Clique em **Gerenciamento** na navegação inferior esquerda do console.
4. Dentro das informações que aparecem na navegação à esquerda, procure as informações da versão do agente de backup do Azure.
5. Se a versão for inferior a 2.0.9083.0, baixe o instalador de agente de backup mais recente do Azure e execute o instalador no servidor DPM para atualizar o agente de backup do Azure.

Repita as etapas acima para todos os servidores DPM em seu ambiente.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [fazer backup de máquinas Windows usando o agente MARS do Azure Backup](backup-windows-with-mars-agent.md)
