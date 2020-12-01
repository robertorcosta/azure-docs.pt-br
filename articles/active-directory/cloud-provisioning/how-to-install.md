---
title: Instalar o agente de provisionamento em nuvem do Azure AD Connect
description: Este artigo descreve como instalar o agente de provisionamento em nuvem do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4ba11dabc8e805f3604afc2fc4388840ac67cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348527"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente de provisionamento em nuvem do Azure AD Connect
Este documento mostra o processo de instalação do agente de provisionamento do Azure AD (Azure Active Directory) Connect e como configurá-lo inicialmente no portal do Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação assumem que todos os [Pré-requisitos](how-to-prerequisites.md) foram atendidos.

A instalação e configuração do provisionamento do Azure AD Connect são realizadas nas seguintes etapas:

- [Contas de serviço gerenciado de grupo](#group-managed-service-accounts) 
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Uma conta de serviço gerenciado de grupo é uma conta de domínio gerenciado que fornece gerenciamento automático de senhas, gerenciamento de SPN (nome da entidade de serviço) simplificado, a capacidade de delegar o gerenciamento a outros administradores e também estende essa funcionalidade em vários servidores.  Azure AD Connect a sincronização de nuvem dá suporte e recomenda o uso de uma conta de serviço gerenciado de grupo para executar o agente.  Para obter mais informações sobre um gMSA, consulte [contas de serviço gerenciado de grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Atualizando um agente existente para usar a conta gMSA
Para atualizar um agente existente para usar a conta do gMSA criada durante a instalação, basta atualizar o serviço do Agent para a versão mais recente executando o AADConnectProvisioningAgent.msi.  Isso atualizará o serviço para a versão mais recente.  Agora, execute o assistente de instalação novamente e forneça as credenciais para criar a conta quando solicitado.



## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estas etapas.

 1. Entre no servidor que você usará com as permissões de administrador corporativo.
 2. Entre no portal do Azure e, em seguida, vá para **Azure Active Directory**.
 3. No menu à esquerda, selecione **Azure ad Connect**.
 4. Selecione **Gerenciar aprovisionamento (versão prévia)**  > **Examinar todos os agentes**.
 5. Baixe o agente de provisionamento do Azure AD Connect no portal do Azure.
   ![Baixe o agente local](media/how-to-install/install-9.png)</br>
 6. Execute o AADConnectProvisioningAgent.msi do instalador de provisionamento de Azure AD Connect.
 7. Na tela **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, aceite os termos de licença e selecione **Instalar**.
   ![Tela Pacote do agente de provisionamento do Microsoft Azure AD Connect](media/how-to-install/install-1.png)</br>
 8. Após a conclusão desta operação, o assistente de configuração é iniciado. Entre com sua conta de administrador global do Azure AD.
 9. Na **tela configurar conta de serviço** , selecione **criar gMSA** ou **usar o gMSA personalizado**.  Se você permitir que o agente crie a conta, ele será nomeado provAgentgMSA $. Se você especificar **usar gMSA personalizado** , será solicitado a fornecer essa conta.
 10. Insira as credenciais de administrador de domínio para criar a conta de serviço gerenciado de grupo que será usada para executar o serviço do Agent. Clique em **Próximo**.  
   ![Criar gMSA](media/how-to-install/install-12.png)</br>
 11. Na tela **Active Directory Connect**, selecione **Adicionar diretório**. Em seguida, entre com sua conta de administrador do Active Directory. Esta operação adiciona seu diretório local. 
 12. Opcionalmente, você pode gerenciar a preferência de controladores de domínio que o agente usará selecionando **selecionar prioridade do controlador de domínio** e ordenando a lista de controladores de domínio.   Clique em **OK**.
  ![Ordenar controladores de domínio](media/how-to-install/install-2a.png)</br>
 13. Selecione **Avançar**.
  ![Tela Active Directory Connect](media/how-to-install/install-3a.png)</br>
 14.  Na tela de **instalação do agente** , confirme as configurações e a conta que será criada e clique em **confirmar**.
  ![Confirmar configurações](media/how-to-install/install-11.png)</br>
 15. Após a conclusão dessa operação, você verá que **a instalação do agente está concluída.** Selecione **Sair**.
  ![Tela Configuração concluída](media/how-to-install/install-4a.png)</br>
1. Se você ainda vir a tela inicial **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, selecione **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas:

1. Entre no portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure AD Connect**. No centro, selecione **Gerenciar aprovisionamento (versão prévia)** .

   ![Portal do Azure](media/how-to-install/install-6.png)</br>

1.  Na tela **Provisionamento do Azure AD (versão prévia)** , selecione **Examinar todos os agentes**.

    ![Opção Examinar todos os agentes](media/how-to-install/install-7.png)</br>
 
1. Na tela **Agentes de provisionamento local**, você vê os agentes que instalou. Verifique se o agente em questão está lá e se está marcado como *ativo*.

   ![Tela Agentes de provisionamento local](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas.

1.  Entre no servidor com uma conta de administrador.
1.  Abra **Serviços** navegando até ele ou acessando **Iniciar** > **Executar** > **Services.msc**.
1.  Em **Serviços** certifique-se de que o **Atualizador do Agente do Microsoft Azure AD Connect** e o **Agente de Provisionamento do Microsoft Azure AD Connect** estão lá e se o status é *Em execução*.

    ![Tela de serviços](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e habilitado antes de iniciar a sincronização de usuários. Para configurar um novo agente, consulte [Crie uma nova configuração para o provisionamento baseado em nuvem do Azure AD Connect](how-to-configure.md).




## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
