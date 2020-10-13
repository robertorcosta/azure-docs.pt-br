---
title: 'Tutorial: configurar o protetor Password Manager & cofre digital para o provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o protetor Password Manager & cofre digital.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 26f12b18e2199fb45e6eb94f2a136d2af2b1576c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317575"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: configurar o protetor Password Manager & cofre digital para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no protetor Password Manager & o cofre digital e o Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no protetor Password Manager & cofre digital.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um Gerenciador de senhas protetor & locatário de cofre digital](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de usuário no protetor Password Manager & cofre digital com permissões de administrador.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar o protetor Password Manager & cofre digital da Galeria

Antes de configurar o protetor Password Manager & cofre digital para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o protetor Password Manager & o cofre digital da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o protetor Password Manager & cofre digital da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **protetor password manager & cofre digital**, selecione **protetor Password Manager & cofre digital** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuindo usuários ao protetor Password Manager & cofre digital

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao protetor Password Manager & o cofre digital. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao protetor Password Manager & cofre digital seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir usuários ao protetor Password Manager & cofre digital

* É recomendável que um único usuário do Azure AD seja atribuído ao protetor Password Manager & cofre digital para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao protetor Password Manager & cofre digital, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurando o provisionamento automático de usuário para o protetor Password Manager & cofre digital 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no protetor Password Manager & cofre digital com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o protetor Password Manager & cofre digital, seguindo as instruções fornecidas no [tutorial de logon único do protetor password manager & digital Vault](keeperpasswordmanager-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o protetor Password Manager & o cofre digital no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.

    ![O link do Keeper Password Manager & Digital Vault na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** de seu Gerenciador de senhas do protetor & conta do cofre digital, conforme descrito na etapa 6.

6. Entre no console do [administrador do protetor](https://keepersecurity.com/console/#login). Clique em **administrador** e selecione um nó existente ou crie um novo. Navegue até a guia **provisionamento** e selecione **Adicionar método**.

    ![Console de administração do protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **scim (sistema para gerenciamento de identidade entre domínios**.

    ![Protetor adicionar SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique em **criar token de provisionamento**.

    ![Protetor criar ponto de extremidade](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores para **URL** e **token** e cole-os na **URL do locatário** e no **token secreto** no Azure AD. Clique em **salvar** para concluir a configuração de provisionamento no protetor.

    ![Protetor criar token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao protetor Password Manager & cofre digital. Se a conexão falhar, verifique se o Gerenciador de senhas do protetor & conta do cofre digital tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o protetor Password Manager & cofre digital**.

    ![Mapeamentos de usuário protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o protetor Password Manager & cofre digital na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no protetor Password Manager & cofre digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o protetor Password Manager & cofre digital**.

    ![Mapeamentos de grupo protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o protetor Password Manager & cofre digital na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Gerenciador de senhas protetor & cofre digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o protetor Password Manager & o cofre digital, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para o protetor Password Manager & o cofre digital escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no protetor Password Manager & cofre digital.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Gerenciador de senhas do protetor & cofre digital exige que os **emails** e o **nome de usuário** tenham o mesmo valor de origem, já que qualquer atualização para ambos os atributos modificará o outro valor.
* O Gerenciador de senhas do protetor & cofre digital não dá suporte a exclusões de usuário, apenas desabilitar. Os usuários desabilitados aparecerão como bloqueados na interface do usuário do protetor admin console.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

