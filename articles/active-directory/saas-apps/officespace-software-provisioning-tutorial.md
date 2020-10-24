---
title: 'Tutorial: configurar o software OfficeSpace para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o OfficeSpace software.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: fe48cef0fb235d217f95012e81d7daf501928300
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518561"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Tutorial: configurar o software OfficeSpace para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no OfficeSpace software e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no software OfficeSpace.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um [locatário do OfficeSpace software](https://www.officespacesoftware.com/)
* Uma conta de usuário no OfficeSpace software com permissões de administrador.

## <a name="assigning-users-to-officespace-software"></a>Atribuindo usuários ao OfficeSpace software

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao OfficeSpace software. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao OfficeSpace software seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Dicas importantes para atribuir usuários ao OfficeSpace software

* É recomendável que um único usuário do Azure AD seja atribuído ao OfficeSpace software para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao OfficeSpace software, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-officespace-software-for-provisioning"></a>Configurar o software OfficeSpace para provisionamento

1. Entre no console de [Administração do OfficeSpace software](https://support.officespacesoftware.com/hc). Navegue até **configurações > conectores**.

    ![Console de administração de software OfficeSpace](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navegue até **sincronização de diretório > scim**.

    ![OfficeSpace software Add SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Copie o **token de autenticação scim**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo OfficeSpace software na portal do Azure.

    ![Token de criação de software OfficeSpace](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Adicionar o OfficeSpace software da Galeria

Antes de configurar o OfficeSpace software para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o OfficeSpace software da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OfficeSpace software da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OfficeSpace software**, selecione **OfficeSpace software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![OfficeSpace Software na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Configurando o provisionamento automático de usuário para o OfficeSpace software 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no OfficeSpace software com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o OfficeSpace software seguindo as instruções fornecidas no tutorial de [logon único do OfficeSpace software](./officespace-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o OfficeSpace software no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **OfficeSpace Software**.

    ![O link do OfficeSpace Software na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , digite o `https://<subdomain>.officespacesoftware.com/api/scim/v2/` formato da URL de entrada na **URL do locatário**. Por exemplo, `https://contoso.officespacesoftware.com/api/scim/v2/`. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao OfficeSpace software. Se a conexão falhar, verifique se sua conta do OfficeSpace software tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para OfficeSpace software**.

    ![Mapeamentos de usuário do software OfficeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o OfficeSpace software na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no OfficeSpace software para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do software OfficeSpace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o OfficeSpace software, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para OfficeSpace software escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no OfficeSpace software.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)