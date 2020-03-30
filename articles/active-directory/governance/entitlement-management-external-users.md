---
title: Governe o acesso para usuários externos no gerenciamento de direitos Azure AD - Azure Active Directory
description: Conheça as configurações que você pode especificar para governar o acesso para usuários externos no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128693"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Governe o acesso para usuários externos na gestão de direitos ad do Azure

O gerenciamento de direitos azure AD utiliza [o Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) para colaborar com pessoas fora de sua organização em outro diretório. Com o Azure AD B2B, os usuários externos autenticam em seu diretório inicial, mas têm uma representação em seu diretório. A representação em seu diretório permite que o usuário tenha acesso aos seus recursos.

Este artigo descreve as configurações que você pode especificar para governar o acesso para usuários externos.

## <a name="how-entitlement-management-can-help"></a>Como a gestão de direitos pode ajudar

Ao usar a experiência de convite [Azure AD B2B,](../b2b/what-is-b2b.md) você já deve conhecer os endereços de e-mail dos usuários convidados externos com os quais deseja trazer para o diretório de recursos e trabalhar. Isso funciona muito bem quando você está trabalhando em um projeto menor ou de curto prazo e você já conhece todos os participantes, mas isso é mais difícil de gerenciar se você tem muitos usuários com quem deseja trabalhar ou se os participantes mudam com o tempo.  Por exemplo, você pode estar trabalhando com outra organização e ter um ponto de contato com essa organização, mas com o tempo usuários adicionais dessa organização também precisarão de acesso.

Com o gerenciamento de direitos, você pode definir uma política que permite que usuários de organizações especificadas possam solicitar um pacote de acesso por autor. Você pode especificar se a aprovação é necessária e uma data de validade para o acesso. Se a aprovação for necessária, você também pode convidar um ou mais usuários da organização externa para o seu diretório e designá-los como aprovadores - uma vez que eles provavelmente saberão quais usuários externos de sua organização precisam de acesso. Depois de configurar o pacote de acesso, você pode enviar o link do pacote de acesso para sua pessoa de contato (patrocinador) na organização externa. Esse contato pode compartilhar com outros usuários da organização externa, e eles podem usar este link para solicitar o pacote de acesso. Usuários dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando uma solicitação é aprovada, o gerenciamento de direitos irá provisionar o usuário com o acesso necessário, o que pode incluir convidar o usuário se ele ainda não estiver em seu diretório. O Azure AD criará automaticamente uma conta de hóspedes B2B para eles. Observe que um administrador pode ter limitado anteriormente quais organizações são permitidas para colaboração, definindo uma [lista B2B de permitir ou negar](../b2b/allow-deny-list.md) para permitir ou bloquear convites para outras organizações.  Se o usuário não for permitido pela lista de permissão ou bloqueio, então ele não será convidado.

Como você não deseja que o acesso do usuário externo dure para sempre, você especifica uma data de validade na apólice, como 180 dias. Após 180 dias, se seu acesso não for estendido, a gerência de direitos removerá todo o acesso associado a esse pacote de acesso. Por padrão, se o usuário que foi convidado através do gerenciamento de direitos não tiver outras atribuições de pacote de acesso, então quando perder sua última atribuição, sua conta de hóspedes será bloqueada de fazer login por 30 dias e, posteriormente, removida. Isso evita a proliferação de contas desnecessárias. Como descrito nas seções a seguir, essas configurações são configuráveis.

## <a name="how-access-works-for-external-users"></a>Como funciona o acesso para usuários externos

O diagrama e as etapas a seguir fornecem uma visão geral de como os usuários externos têm acesso a um pacote de acesso.

![Diagrama mostrando o ciclo de vida dos usuários externos](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Você [adiciona uma organização conectada](entitlement-management-organization.md) para o diretório Azure AD ou domínio com o que deseja colaborar.

1. Você cria um pacote de acesso em seu diretório que inclui uma política [Para usuários que não estão em seu diretório](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Você envia um [link do portal Meu Acesso](entitlement-management-access-package-settings.md) para o seu contato na organização externa que eles podem compartilhar com seus usuários para solicitar o pacote de acesso.

1. Um usuário externo (**Solicitante A** neste exemplo) usa o link do portal Meu Acesso para [solicitar acesso](entitlement-management-request-access.md) ao pacote de acesso. Como o usuário entra depende do tipo de autenticação do diretório ou domínio definido na organização conectada.

1. Um aprovador [aprova a solicitação](entitlement-management-request-approve.md) (ou a solicitação é aprovada automaticamente).

1. O pedido vai para o [estado de entrega.](entitlement-management-process.md)

1. Usando o processo de convite B2B, uma conta de usuário convidado é criada em seu diretório (**Solicitante A (Convidado)** neste exemplo). Se uma [lista de dervios ou uma lista de negação](../b2b/allow-deny-list.md) for definida, a configuração da lista será aplicada.

1. O usuário convidado tem acesso a todos os recursos do pacote de acesso. Pode levar algum tempo para que mudanças sejam feitas no Azure AD e em outros Serviços Online da Microsoft ou aplicativos SaaS conectados. Para obter mais informações, [consulte Quando as alterações forem aplicadas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. O usuário externo recebe um e-mail indicando que seu acesso foi [entregue](entitlement-management-process.md).

1. Para acessar os recursos, o usuário externo pode clicar no link no e-mail ou tentar acessar qualquer um dos recursos do diretório diretamente para concluir o processo de convite.

1. Dependendo das configurações da diretiva, com o passar do tempo, a atribuição do pacote de acesso para o usuário externo expira e o acesso do usuário externo é removido.

1. Dependendo do ciclo de vida das configurações dos usuários externos, quando o usuário externo não tem mais nenhuma atribuição de pacote de acesso, o usuário externo é impedido de fazer login e a conta de usuário convidado é removida do seu diretório.

## <a name="settings-for-external-users"></a>Configurações para usuários externos

Para garantir que pessoas fora da sua organização possam solicitar pacotes de acesso e ter acesso aos recursos nesses pacotes de acesso, existem algumas configurações que você deve verificar estão configuradas corretamente.

### <a name="enable-catalog-for-external-users"></a>Habilitar catálogo para usuários externos

- Por padrão, quando você cria um [novo catálogo,](entitlement-management-catalog-create.md)ele é habilitado a permitir que usuários externos solicitem pacotes de acesso no catálogo. Certifique-se **de que a ativação para usuários externos** está definida como **Sim**.

    ![Editar configurações de catálogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configure as configurações de colaboração externa Azure AD B2B

- Permitir que os hóspedes convidem outros convidados para o seu diretório significa que os convites dos convidados podem ocorrer fora da gestão de direitos. Recomendamos a definição **Os hóspedes podem convidar** o **Não** para permitir apenas convites devidamente regidos.
- Se você estiver usando a lista de perde B2B, você deve certificar-se de que qualquer domínio com o qual você deseja fazer parceria usando o gerenciamento de direitos seja adicionado à lista. Alternativamente, se você estiver usando a lista de negação B2B, você deve certificar-se de que qualquer domínio com o qual você queira fazer parceria não seja adicionado à lista.
- Se você criar uma política de gerenciamento de direitos para **todos os usuários** (Todas as organizações conectadas + quaisquer novos usuários externos), qualquer configuração de lista B2B que você tenha terá precedência. Portanto, certifique-se de incluir os domínios que você pretende incluir nesta política à sua lista de permitir se você estiver usando um, e excluí-los de sua lista de negação se você estiver usando uma lista de negação.
- Se você quiser criar uma política de gerenciamento de direitos que inclua **todos os usuários** (Todas as organizações conectadas + quaisquer novos usuários externos), você deve primeiro ativar a autenticação de senha única de e-mail para o seu diretório. Para obter mais informações, consulte A autenticação de senha única do [e-mail (visualização)](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Para obter mais informações sobre as configurações de colaboração externa Azure AD B2B, consulte [Ativar colaboração externa B2B e gerenciar quem pode convidar os convidados](../b2b/delegate-invitations.md).

    ![Configurações de colaboração externa do Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Revise suas políticas de acesso condicional

- Certifique-se de excluir os hóspedes de quaisquer políticas de Acesso Condicional que os novos usuários convidados não poderão atender, pois isso os impedirá de fazer login em seu diretório. Por exemplo, os hóspedes provavelmente não têm um dispositivo registrado, não estão em um local conhecido e não querem se recadastrar para autenticação multifatorial (MFA), então adicionar esses requisitos em uma política de acesso condicional impedirá que os hóspedes usem o direito Gestão. Para obter mais informações, consulte [Quais são as condições no Acesso Condicional do Diretório Ativo do Azure?](../conditional-access/concept-conditional-access-conditions.md).

    ![A política de exclusão da política de acesso condicional do Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Revise suas configurações de compartilhamento externo do SharePoint Online

- Se você quiser incluir sites DoSharePoint Online em seus pacotes de acesso para usuários externos, certifique-se de que sua configuração de compartilhamento externo em nível de organização esteja definida para **Qualquer pessoa** (os usuários não precisam fazer login) ou **hóspedes novos e existentes** (os hóspedes devem fazer login ou fornecer um código de verificação). Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Se você quiser restringir qualquer compartilhamento externo fora da gestão de direitos, você pode definir a configuração de compartilhamento externo para **hóspedes existentes**. Então, somente novos usuários que forem convidados através da gestão de direitos poderão ter acesso a esses sites. Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Certifique-se de que as configurações em nível de site habilitem o acesso aos hóspedes (as mesmas seleções de opção listadas anteriormente). Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo para um site](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Revise as configurações de compartilhamento de grupo do Office 365

- Se você quiser incluir grupos do Office 365 em seus pacotes de acesso para usuários externos, certifique-se de que os **usuários de Let adicionem novos convidados à organização** para permitir o acesso dos hóspedes. **On** Para obter mais informações, consulte [Gerenciar o acesso dos hóspedes aos Grupos do Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Se você quiser que os usuários externos possam acessar o site do SharePoint Online e os recursos associados a um grupo do Office 365, certifique-se de ativar o compartilhamento externo do SharePoint Online. Para obter mais informações, consulte [Ativar ou desativar o compartilhamento externo](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Para obter informações sobre como definir a política de hóspedes para grupos do Office 365 no nível do diretório no PowerShell, consulte [Exemplo: Configure a política de hóspedes para grupos no nível do diretório](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Revise as configurações de compartilhamento de suas equipes

- Se você quiser incluir equipes em seus pacotes de acesso para usuários externos, certifique-se de que o acesso de **hóspedes no Microsoft Teams** esteja definido como **On** para permitir o acesso dos hóspedes. Para obter mais informações, consulte [Configurar o acesso ao hóspede no centro de administradores do Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Gerenciar o ciclo de vida dos usuários externos

Você pode selecionar o que acontece quando um usuário externo, que foi convidado para o seu diretório através de uma solicitação de pacote de acesso sendo aprovado, não tem mais nenhuma atribuições do pacote de acesso. Isso pode acontecer se o usuário renunciar a todas as suas atribuições de pacote de acesso ou sua última atribuição de pacote de acesso expirar. Por padrão, quando um usuário externo não tem mais nenhuma atribuição de pacote de acesso, ele é impedido de fazer login em seu diretório. Após 30 dias, sua conta de usuário convidado é removida do seu diretório.

**Papel pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na seção **De administração de direitos,** clique em **Configurações**.

1. Clique em **Editar**.

    ![Configurações para gerenciar o ciclo de vida de usuários externos](./media/entitlement-management-external-users/settings-external-users.png)

1. Na **seção Gerenciar o ciclo de vida dos usuários externos,** selecione as diferentes configurações para usuários externos.

1. Uma vez que um usuário externo perca sua última atribuição para quaisquer pacotes de acesso, se você quiser bloqueá-los de fazer login neste diretório, defina o **usuário externo do Bloco de login neste diretório** para **Sim**.

    > [!NOTE]
    > Se um usuário estiver impedido de fazer login neste diretório, o usuário não poderá solicitar novamente o pacote de acesso ou solicitar acesso adicional neste diretório. Não configure o bloqueio de login se eles precisarem solicitar acesso a outros pacotes de acesso.

1. Uma vez que um usuário externo perca sua última atribuição para quaisquer pacotes de acesso, se você quiser remover sua conta de usuário convidado neste diretório, defina **Remover usuário externo** para **Sim**.

    > [!NOTE]
    > A gestão de direitos apenas remove contas que foram convidadas através da gestão de direitos. Além disso, observe que um usuário será impedido de fazer login e removido deste diretório, mesmo que esse usuário tenha sido adicionado a recursos neste diretório que não foram atribuições de pacote de acesso. Se o hóspede estava presente neste diretório antes de receber as atribuições do pacote de acesso, eles permanecerão. No entanto, se o convidado foi convidado através de uma atribuição de pacote de acesso, e depois de ser convidado também foi designado para um site OneDrive for Business ou SharePoint Online, eles ainda serão removidos.

1. Se você quiser remover a conta de usuário convidado neste diretório, você pode definir o número de dias antes de ser removido. Se você quiser remover a conta de usuário convidado assim que eles perderem sua última atribuição para quaisquer pacotes de acesso, defina **número de dias antes de remover o usuário externo deste diretório** para **0**.

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma organização conectada](entitlement-management-organization.md)
- [Para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Solução de problemas](entitlement-management-troubleshoot.md)
