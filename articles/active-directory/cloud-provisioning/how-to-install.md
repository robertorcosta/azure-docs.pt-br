---
title: Instalar o agente de provisionamento em nuvem do Azure AD Connect
description: Este artigo descreve como instalar o agente de provisionamento em nuvem do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764071eeaf368ecf78679632cffbd6964db40aa5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681039"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente de provisionamento em nuvem do Azure AD Connect
Este documento mostra o processo de instalação do agente de provisionamento do Azure AD (Azure Active Directory) Connect e como configurá-lo inicialmente no portal do Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação assumem que todos os [Pré-requisitos](how-to-prerequisites.md) foram atendidos.

A instalação e configuração do provisionamento do Azure AD Connect são realizadas nas seguintes etapas:
    
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estas etapas.

1. Entre no servidor que você usará com as permissões de administrador corporativo.
1. Vá para o portal do Azure. Na esquerda, selecione **Azure Active Directory**.
1. Selecione **Gerenciar aprovisionamento (versão prévia)**  > **Examinar todos os agentes**.
1. Baixe o agente de provisionamento do Azure AD Connect no portal do Azure.

   ![Baixe o agente local](media/how-to-install/install9.png)</br>
1. Execute o instalador de provisionamento do Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Na tela **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, aceite os termos de licença e selecione **Instalar**.

   ![Tela Pacote do agente de provisionamento do Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. Após a conclusão desta operação, o assistente de configuração é iniciado. Entre com sua conta de administrador global do Azure AD.
1. Na tela **Active Directory Connect**, selecione **Adicionar diretório**. Em seguida, entre com sua conta de administrador do Active Directory. Esta operação adiciona seu diretório local. Selecione **Avançar**.

   ![Tela Active Directory Connect](media/how-to-install/install3.png)</br>

1. Na tela **Configuração concluída**, selecione **Confirmar**. Esta operação registra e reinicia o agente.

   ![Tela Configuração concluída](media/how-to-install/install4.png)</br>

1. Após a conclusão desta operação, você verá o aviso **A configuração do agente foi verificada com êxito.** Selecione **Sair**.

   ![Botão Sair](media/how-to-install/install5.png)</br>
1. Se você ainda vir a tela inicial **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, selecione **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas:

1. Entre no portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure AD Connect**. No centro, selecione **Gerenciar aprovisionamento (versão prévia)** .

   ![Portal do Azure](media/how-to-install/install6.png)</br>

1.  Na tela **Provisionamento do Azure AD (versão prévia)** , selecione **Examinar todos os agentes**.

    ![Opção Examinar todos os agentes](media/how-to-install/install7.png)</br>
 
1. Na tela **Agentes de provisionamento local**, você vê os agentes que instalou. Verifique se o agente em questão está lá e se está marcado como *ativo*.

   ![Tela Agentes de provisionamento local](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas.

1.  Entre no servidor com uma conta de administrador.
1.  Abra **Serviços** navegando até ele ou acessando **Iniciar** > **Executar** > **Services.msc**.
1.  Em **Serviços** certifique-se de que o **Atualizador do Agente do Microsoft Azure AD Connect** e o **Agente de Provisionamento do Microsoft Azure AD Connect** estão lá e se o status é *Em execução*.

    ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e habilitado antes de iniciar a sincronização de usuários. Para configurar um novo agente, consulte [Crie uma nova configuração para o provisionamento baseado em nuvem do Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
 
