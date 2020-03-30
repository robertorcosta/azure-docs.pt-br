---
title: 'Tutorial: Configure O Gerenciador de Senhas do Goleiro & Cofre Digital para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Keeper Password Manager & Cofre Digital.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057491"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configure Keeper Password Manager & Cofre Digital para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Keeper Password Manager & Digital Vault e a zure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Keeper Password Manager & Cofre Digital.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um gerenciador de senhas do Goleiro & inquilino do Cofre Digital](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de usuário no Keeper Password Manager & Cofre Digital com permissões de administração.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar gerenciador de senhas do goleiro & cofre digital da galeria

Antes de configurar o Keeper Password Manager & Cofre Digital para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Keeper Password Manager & Digital Vault da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Gerenciador de Senhas do Goleiro & Cofre Digital da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O Gerenciador de Senhas do Goleiro & Cofre Digital**, selecione Keeper Password Manager & Cofre **Digital** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuindo usuários ao Keeper Password Manager & Cofre Digital

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Keeper Password Manager & Cofre Digital. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Keeper Password Manager & Cofre Digital seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir usuários ao Keeper Password Manager & Cofre Digital

* Recomenda-se que um único usuário azure AD seja atribuído ao Keeper Password Manager & Cofre Digital para testar a configuração de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Gerenciador de Senhas do Goleiro & Cofre Digital, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configuração do provisionamento automático do usuário para o Keeper Password Manager & Cofre Digital 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Keeper Password Manager & Cofre Digital com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o login único baseado em SAML para o Keeper Password Manager & Cofre Digital, seguindo as instruções fornecidas no [Gerenciador de Senhas do Goleiro & tutorial de login único do Cofre Digital](keeperpasswordmanager-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Keeper Password Manager & Cofre Digital no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.

    ![O link do Keeper Password Manager & Digital Vault na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Administração,** insira a **URL do inquilino** e o símbolo **secreto** do seu Gerenciador de Senhas do Goleiro & conta do Cofre Digital conforme descrito na Etapa 6.

6. Faça login no seu [console de admin keeper](https://keepersecurity.com/console/#login). Clique em **Admin** e selecione um nó existente ou crie um novo. Navegue até a guia **Provisionamento** e selecione **'Adicionar método ''Adicionar'.**

    ![Keeper Admin Console](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (System for Cross-Domain Identity Management**.

    ![Keeper Adicionar SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique **em Criar token de provisionamento**.

    ![Goleiro criar ponto final](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores de **URL** e **Token** e cole-os em **URL de inquilino** e **Token Secreto** no Azure AD. Clique **em Salvar** para concluir a configuração de provisionamento no Keeper.

    ![Goleiro criar token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Ao preencher os campos mostrados na Etapa 5, clique em **Conexão de teste** para garantir que o Azure AD possa se conectar ao Keeper Password Manager & Cofre Digital. Se a conexão falhar, certifique-se de que o Gerenciador de Senhas do Goleiro & conta do Cofre Digital tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o Gerenciador de senhas do Goleiro & cofre digital**.

    ![Mapeamentos de usuários do Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Revise os atributos do usuário sincronizados do Azure AD ao Keeper Password Manager & Cofre Digital na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Keeper Password Manager & Cofre Digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do goleiro](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para o Keeper Password Manager & Cofre Digital**.

    ![Mapeamentos do Grupo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Revise os atributos de grupo sincronizados do Azure AD para o Keeper Password Manager & Cofre Digital na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Keeper Password Manager & Cofre Digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo de Goleiros](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento Azure AD para O Gerenciador de Senhas do Goleiro & Cofre Digital, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou grupos que você gostaria de provisionar ao Keeper Password Manager & Cofre Digital, escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Keeper Password Manager & Cofre Digital.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Keeper Password Manager & Cofre Digital requer **que os e-mails** e o **userName** tenham o mesmo valor de origem, pois qualquer atualização para ambos os atributos modificará o outro valor.
* O Keeper Password Manager & Cofre Digital não suporta exclusões de usuários, apenas desabilita. Os usuários desativados aparecerão como bloqueados na ui do console do Keeper Admin.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

