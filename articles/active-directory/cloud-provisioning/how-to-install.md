---
title: Instalar o agente de provisionamento em nuvem do Azure AD Connect
description: Este artigo descreve como instalar o agente de provisionamento em nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263342"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente de provisionamento em nuvem do Azure AD Connect
Este documento orienta você através do processo de instalação do agente de provisionamento Azure Active Directory (Azure AD) Connect e como configurá-lo inicialmente no portal Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação supõem que todos os [pré-requisitos foram cumpridos.](how-to-prerequisites.md)

A instalação e configuração do provisionamento do Azure AD Connect é realizada nas seguintes etapas:
    
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estas etapas.

1. Faça login no servidor que você usará com permissões de admin corporativo.
1. Vá para o portal do Azure. À esquerda, **selecione Azure Active Directory**.
1. Selecione **Gerenciar provisionamento (visualização)** > **Revisar todos os agentes**.
1. Baixe o agente de provisionamento Azure AD Connect no portal Azure.

   ![Baixar agente no local](media/how-to-install/install9.png)</br>
1. Execute o instalador de provisionamento Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Na tela do pacote do agente de provisionamento de **conexão AD do Microsoft Azure,** aceite os termos de licenciamento e selecione **Instalar**.

   ![Tela do pacote do agente de provisionamento de conexão ad do Microsoft Azure AD](media/how-to-install/install1.png)</br>

1. Após o término desta operação, o assistente de configuração é iniciado. Entre com sua conta de administrador global do Azure AD.
1. Na tela **Conectar diretório ativo,** selecione **Adicionar diretório**. Em seguida, faça login com sua conta de administrador do Diretório Ativo. Esta operação adiciona seu diretório local. Selecione **Avançar**.

   ![Conectar tela de diretório ativo](media/how-to-install/install3.png)</br>

1. Na **tela completa de configuração,** **selecione Confirmar**. Esta operação registra e reinicia o agente.

   ![Tela de configuração concluída](media/how-to-install/install4.png)</br>

1. Após o término desta operação, você deve ver o aviso de que a **configuração do seu agente foi verificada com sucesso.** Selecione **Saída**.

   ![Botão Sair](media/how-to-install/install5.png)</br>
1. Se você ainda vir a tela inicial do pacote do agente de provisionamento de **conexão AD do Microsoft Azure,** selecione **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas.

1. Entre no portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure AD Connect**. No centro, selecione **Gerenciar provisionamento (visualização)**.

   ![Portal do Azure](media/how-to-install/install6.png)</br>

1.  Na tela **azure AD Provisioning (preview),** selecione **Revisar todos os agentes**.

    ![Revise todas as opções de agentes](media/how-to-install/install7.png)</br>
 
1. Na tela dos **agentes de provisionamento no local,** você vê os agentes que você instalou. Verifique se o agente em questão está lá e se está marcado como *ativo*.

   ![Tela de agentes de provisionamento no local](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verifique a porta
Para verificar se o Azure está ouvindo na porta 443 e se seu agente pode se comunicar com ele, siga estas etapas.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Este teste verifica se seus agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor onde o agente está instalado.

![Verificação da acessibilidade da porta](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas.

1.  Faça login no servidor com uma conta de administrador.
1.  Abrir **serviços** navegando para ele ou indo para **Iniciar Serviços** > **de Execução.msc** > **Services.msc**.
1.  Em **Serviços,** certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estejam lá e seu status esteja em *execução*.

    ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e ativado antes de começar a sincronizar os usuários. Para configurar um novo agente, consulte [Criar uma nova configuração para provisionamento baseado em nuvem do Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
 
