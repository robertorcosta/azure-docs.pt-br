---
title: Habilitar o write-back de senha do Azure Active Directory
description: Neste tutorial, você aprenderá a habilitar o write-back de redefinição de senha por autoatendimento do Azure AD usando o Azure AD Connect para sincronizar as alterações de volta para um ambiente local do Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d383acae83f0f42f9c16fcb5d4ea7efbdf2b5f8
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486474"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Tutorial: Habilitar o write-back da redefinição de senha por autoatendimento do Azure Active Directory para um ambiente local

Com a SSPR (redefinição de senha por autoatendimento) do Azure AD (Azure Active Directory), os usuários podem atualizar as respectivas senhas ou desbloquear as respectivas contas usando um navegador da Web. Em um ambiente híbrido em que o Azure AD está conectado a um ambiente local do AD DS (Active Directory Domain Services), esse cenário pode fazer com que as senhas sejam diferentes entre os dois diretórios.

O write-back de senha pode ser usado para sincronizar alterações de senha no Azure AD de volta para seu ambiente local do AD DS. O Azure AD Connect fornece um mecanismo seguro para enviar essas alterações de senha do Azure AD de volta para um diretório local existente.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar as permissões necessárias para write-back de senha
> * Habilitar a opção de write-back de senha no Azure AD Connect
> * Habilitar write-back de senha no SSPR do Azure AD

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Para obter mais informações, confira [Requisitos de licenciamento para o SSPR do Azure AD](concept-sspr-licensing.md).
* Uma conta com privilégios de *administrador global*.
* Azure AD configurado para redefinição de senha por autoatendimento.
    * Se necessário, [conclua o tutorial anterior para habilitar o SSPR do Azure AD](tutorial-enable-sspr.md).
* Um ambiente local do AD DS existente configurado com uma versão atual do Azure AD Connect.
    * Se necessário, configure o Azure AD Connect usando as configurações [Expressas](../hybrid/how-to-connect-install-express.md) ou [Personalizadas](../hybrid/how-to-connect-install-custom.md).

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Configurar permissões de conta para Azure AD Connect

O Azure AD Connect permite sincronizar usuários, grupos e credenciais entre um ambiente local do AD DS e o Azure AD. Normalmente, você instala o Azure AD Connect em um computador com Windows Server 2012 ou posterior que está ingressado no domínio local do AD DS.

Para trabalhar corretamente com o write-back do SSPR, a conta especificada no Azure AD Connect deve ter as permissões e as opções apropriadas definidas. Se você não tiver certeza de qual conta está em uso no momento, abra o Azure AD Connect e selecione a opção **Exibir a configuração atual**. A conta à qual você precisa adicionar as permissões está listada em **Diretórios Sincronizados**. As seguintes permissões e opções precisam estar definidas na conta:

* **Redefinir senha**
* **Alterar senha**
* **Permissões de gravação** em `lockoutTime`
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** em:
   * O objeto raiz de *cada domínio* na floresta
   * As unidades organizacionais (OUs) do usuário que você deseja que estejam no escopo para SSPR

Se você não atribuir essas permissões, mesmo que o write-back pareça estar configurado corretamente, os usuários verão erros ao tentar gerenciar as respectivas senhas locais na nuvem.

Para configurar as permissões apropriadas para que ocorra o write-back de senha, conclua as etapas a seguir:

1. No ambiente local do AD DS, abra **Usuários e Computadores do Active Directory** com uma conta que tem as permissões de *administrador de domínio* apropriadas.
1. No menu **Exibir**, verifique se os **Recursos avançados** estão ativados.
1. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha **Propriedades** > **Segurança** > **Avançado**.
1. Na guia **Permissões**, selecione **Adicionar**.
1. Para **Entidade de segurança**, selecione a conta à qual as permissões devem ser aplicadas (a conta usada pelo Azure AD Connect).
1. Na lista suspensa **Aplica-se a**, selecione os **objetos de Usuário Descendente**.
1. Em *Permissões*, selecione as caixas para as seguintes opções:
    * **Alterar senha**
    * **Redefinir senha**
1. Em *Permissões*, marque as caixas das opções a seguir. Você precisa percorrer a lista para encontrar essas opções, que podem já estar definidas por padrão:
    * **Gravar lockoutTime**
    * **Gravar pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Quando concluído, selecione **Aplicar/OK** para aplicar as alterações e sair das caixas de diálogo abertas.

Quando você atualizar as permissões, poderá levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.

As políticas de senha no ambiente local do AD DS podem impedir que as redefinições de senha sejam processadas corretamente. Para que o write-back de senha funcione corretamente, a política de grupo para *Tempo de vida mínimo da senha* precisa ser definida como 0. Essa configuração pode ser encontrada em **Configuração do Computador > Políticas > Configurações do Windows > Configurações de Segurança > Políticas de Conta** em `gpedit.msc`.

Se você atualizar a política de grupo, aguarde a política atualizada ser replicada ou use o comando `gpupdate /force`.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Habilitar write-back de senha no Azure AD Connect

Uma das opções de configuração no Azure AD Connect é para o write-back de senha. Quando essa opção é habilitada, os eventos de alteração de senha fazem o Azure AD Connect sincronizar as credenciais atualizadas de volta para o ambiente local do AD DS.

Para habilitar o write-back de redefinição de senha por autoatendimento, primeiro habilite a opção de write-back no Azure AD Connect. Em seu servidor de Azure AD Connect, conclua as seguintes etapas:

1. Entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
1. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global para o locatário do Azure e selecione **Próximo**.
1. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
1. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.

    ![Configurar o Azure AD Connect para write-back de senha](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
1. Quando você vir a configuração terminar, selecione **Sair**.

## <a name="enable-password-writeback-for-sspr"></a>Habilitar o write-back de senha para o SSPR

Com o write-back de senha habilitado no Azure AD Connect, agora configure o SSPR do Azure AD para write-back. Quando você habilita o SSPR para usar o write-back de senha, a senha atualizada dos usuários que alteram ou redefinem as respectivas senhas é sincronizada novamente para o ambiente local do AD DS.

Para habilitar o write-back de senha no SSPR, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Active Directory**, selecione **redefinição de senha**e, em seguida, escolha **Integração local**.
1. Defina a opção de **Fazer write-back de senhas em seu diretório local?** como *Sim*.
1. Defina a opção de **Permitir aos usuários desbloquear contas sem redefinir a senha?** como *Sim*.

    ![Habilitar a redefinição de senha por autoatendimento do Azure AD para write-back de senha](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Quando estiver pronto, selecione **Salvar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não deseja mais usar a funcionalidade de write-back do SSPR configurada como parte deste tutorial, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Active Directory**, selecione **redefinição de senha**e, em seguida, escolha **Integração local**.
1. Defina a opção **Fazer write-back de senhas em seu diretório local?** como *Não*.
1. Defina a opção de **Permitir aos usuários desbloquear contas sem redefinir a senha?** como *Não*.

Se você não quiser mais usar nenhuma funcionalidade de senha, conclua as seguintes etapas do seu servidor do Azure AD Connect:

1. Entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
1. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global para o locatário do Azure e selecione **Próximo**.
1. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
1. Na página **Recursos opcionais**, desmarque a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
1. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
1. Quando você vir a configuração terminar, selecione **Sair**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou o write-back do SSPR do Azure AD para um ambiente local do AD DS. Você aprendeu a:

> [!div class="checklist"]
> * Configurar as permissões necessárias para write-back de senha
> * Habilitar a opção de write-back de senha no Azure AD Connect
> * Habilitar write-back de senha no SSPR do Azure AD

> [!div class="nextstepaction"]
> [Avaliar o risco ao entrar](tutorial-risk-based-sspr-mfa.md)
