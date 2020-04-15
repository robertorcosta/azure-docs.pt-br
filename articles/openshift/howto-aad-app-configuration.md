---
title: Integração do Azure Active Directory para O Azure Red Hat OpenShift
description: Aprenda a criar um grupo de segurança Azure AD e usuário para testar aplicativos no seu cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382909"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Azure Active Directory para O Azure Red Hat OpenShift

Se você ainda não criou um inquilino do Azure Active Directory (Azure AD), siga as instruções em [Criar um inquilino Azure AD para o Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com essas instruções.

O Microsoft Azure Red Hat OpenShift precisa de permissões para executar tarefas em nome do seu cluster. Se sua organização ainda não tiver um usuário Azure AD, um grupo de segurança Azure AD ou um registro de aplicativo Azure AD para usar como diretor de serviço, siga estas instruções para criá-los.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo usuário do Azure Active Directory

No [portal Azure,](https://portal.azure.com)certifique-se de que seu inquilino apareça sob o nome do usuário no canto superior direito do portal:

![Captura de tela do portal](./media/howto-create-tenant/tenant-callout.png) com inquilino listado no canto superior direito Se o inquilino errado for exibido, clique no nome do usuário no canto superior direito e clique em **Diretório de Switch**e selecione o inquilino correto na lista Todos os **Diretórios.**

Crie um novo usuário do Azure Active Directory 'Owner' para fazer login no cluster Azure Red Hat OpenShift.

1. Vá para a lâmina [Usuários-Todos os usuários.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Clique **em +Novo usuário** para abrir o painel do **Usuário.**
3. Digite um **nome** para este usuário.
4. Crie um **nome de usuário** com base no `.onmicrosoft.com` nome do inquilino que você criou, com anexo no final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote este nome de usuário. Você vai precisar dele para entrar no seu cluster.
5. Clique **na função Diretório** para abrir o painel de função de diretório e selecione **'Proprietário'** e clique em **'Ok'** na parte inferior do painel.
6. No **painel do Usuário,** clique em **Mostrar Senha** e registre a senha temporária. Depois de assinar na primeira vez, você será solicitado a redefini-lo.
7. Na parte inferior do painel, clique **em Criar** para criar o usuário.

## <a name="create-an-azure-ad-security-group"></a>Crie um grupo de segurança AD do Azure

Para conceder acesso ao administrador de cluster, as adesões em um grupo de segurança Azure AD são sincronizadas no grupo OpenShift "osa-customer-admins". Se não for especificado, nenhum acesso ao administrador de cluster será concedido.

1. Abra a lâmina dos grupos de diretórioativo ativo do [Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Clique **em +Novo Grupo**.
3. Forneça um nome de grupo e uma descrição.
4. Definir **o tipo de grupo** como **Segurança**.
5. Definir **o tipo de associação** para **Atribuído**.

    Adicione o usuário Azure AD que você criou na etapa anterior a este grupo de segurança.

6. Clique **em Membros** para abrir o painel Selecionar **membros.**
7. Na lista de membros, selecione o usuário Azure AD que você criou acima.
8. Na parte inferior do portal, clique em **Selecionar** e, em seguida, **Criar** para criar o grupo de segurança.

    Anote o valor do ID do grupo.

9. Quando o grupo for criado, você o verá na lista de todos os grupos. Clique no novo grupo.
10. Na página que aparece, copie o **ID do objeto**. Vamos nos referir a `GROUPID` esse valor como no tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)

> [!IMPORTANT]
> Para sincronizar esse grupo com o grupo Osa-customer-admins OpenShift, crie o cluster usando o Cli do Azure. O portal Azure atualmente não tem um campo para definir esse grupo.

## <a name="create-an-azure-ad-app-registration"></a>Crie um registro de aplicativo AD do Azure

Você pode criar automaticamente um cliente de registro de aplicativo do Azure Active Directory (Azure AD) como parte da criação do cluster, omitindo o `--aad-client-app-id` sinalizador ao `az openshift create` comando. Este tutorial mostra como criar o registro do aplicativo Azure AD para completude.

Se sua organização ainda não tiver um registro de aplicativo do Azure Active Directory (Azure AD) para usar como diretor de serviço, siga estas instruções para criar um.

1. Abra o [blade de inscrições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) do App e clique em **+Novo cadastro**.
2. No Cadastro um painel de **inscrição,** digite um nome para o seu registro de inscrição.
3. Certifique-se **de que, em tipos de conta suportados,** **as contas neste diretório organizacional só** serão selecionadas. Esta é a escolha mais segura.
4. Adicionaremos um URI de redirecionamento mais tarde, assim que soubermos o URI do cluster. Clique no botão **Registrar** para criar o registro do aplicativo Azure AD.
5. Na página que aparece, copie o **ID do aplicativo (cliente).** Vamos nos referir a `APPID` esse valor como no tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)

![Captura de tela da página de objetos do aplicativo](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Gere um segredo de cliente para autenticar seu aplicativo no Azure Active Directory.

1. Na seção **Gerenciar** a página de registros do aplicativo, clique em **Certificados & segredos**.
2. No **painel De Certificados & segredos,** clique **em +Novo cliente secreto**.  O **Painel Secreto Adicionar um cliente** aparece.
3. Forneça uma **descrição**.
4. O **conjunto Expira** a duração que você preferir, por **exemplo, em 2 anos**.
5. Clique **em Adicionar** e o valor-chave aparecerá na seção Segredos do **Cliente** da página.
6. Copie o valor-chave. Vamos nos referir a `SECRET` esse valor como no tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)

![Captura de tela do painel de certificados e segredos](./media/howto-create-tenant/create-key.png)

Para obter mais informações sobre objetos de aplicativo do Azure, consulte [Os principais objetos de aplicativo e serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre a criação de um novo aplicativo Azure AD, consulte Registre um aplicativo com o ponto final do [Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Adicionar permissões de API

[//]: # (Não mude para O Gráfico do Microsoft. Não funciona com o Microsoft Graph.)
1. Na seção **Gerenciar,** clique **em permissões de API**
2. Clique **em Adicionar permissão** e selecione **O Gráfico de Diretório Ativo do Azure** e, em seguida, **as permissões delegadas**.
> [!NOTE]
> Certifique-se de que você selecionou o "Gráfico de Diretório Ativo do Azure" e não o azulejo "Microsoft Graph".

3. Expanda o **Usuário** na lista abaixo e habilite a permissão **User.Read.** Se **o Usuário.Read** estiver ativado por padrão, certifique-se de que é a permissão do **Azure Active Directory Graph** **User.Read**.
4. Role para cima e selecione **permissões de aplicativo**.
5. Expanda o **diretório** na lista abaixo e habilite **o Diretório.ReadAll**.
6. Clique **em Adicionar permissões** para aceitar as alterações.
7. O painel de permissões da API deve agora mostrar *o Usuário.Read* e *o Directory.ReadAll*. Observe o aviso na coluna **de consentimento do Admin necessária** ao lado do *Diretório.ReadAll*.
8. Se você é o administrador de assinaturas do *Azure,* clique em **Conceder consentimento para nome de *assinatura* ** abaixo. Se você não é o Administrador de Assinaturas do *Azure,* solicite o consentimento do administrador.

![Captura de tela do painel de permissões da API. Usuário.Leia e Diretório.LeiaTodas as permissões adicionadas, consentimento de admin necessário para o Diretório.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A sincronização do grupo de administradores de cluster só funcionará após a concessão do consentimento. Você verá um círculo verde com uma marca de seleção e uma mensagem "Concedido para *nome de assinatura"* na coluna *de consentimento do Admin.*

Para obter detalhes sobre o gerenciamento de administradores e outras funções, consulte [Adicionar ou alterar administradores de assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Recursos

* [Aplicativos e objetos principais de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Início Rápido: registrar um aplicativo no ponto de extremidade v1.0 do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Próximas etapas

Se você conheceu todos os [pré-requisitos do Azure Red Hat OpenShift,](howto-setup-environment.md)você está pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)
