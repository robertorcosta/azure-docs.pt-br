---
title: 'Tutorial: Configure o Floco de Neve para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063125"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configure o Floco de Neve para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Snowflake e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Snowflake.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino floco de neve.](https://www.Snowflake.com/pricing/)
* Uma conta de usuário em Snowflake com permissões de administração.

## <a name="assigning-users-to-snowflake"></a>Atribuindo usuários ao Floco de Neve

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Snowflake. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Floco de Neve seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Dicas importantes para atribuir usuários ao Snowflake

* Recomenda-se que um único usuário azure AD seja atribuído ao Snowflake para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Snowflake, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-snowflake-for-provisioning"></a>Configuração floco de neve para provisionamento

Antes de configurar o Snowflake para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Snowflake.

1. Faça login no seu console de admin Snowflake. Digite a consulta mostrada abaixo na planilha destacada e clique em **Executar**.

    ![Console de Admin de Floco de Neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um Token de Acesso SCIM será gerado para o seu inquilino floco de neve. Para recuperá-lo, clique no link destacado abaixo.

    ![Floco de Neve Adicionar SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor do token gerado e clique **em Feito**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do seu aplicativo Floco de Neve no portal Azure.

    ![Floco de Neve Adicionar SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Adicione floco de neve da galeria

Para configurar o Snowflake para provisionamento automático do usuário com o Azure AD, você precisa adicionar Snowflake da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar floco de neve na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Snowflake**, selecione **Floco de Neve** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Floco de neve na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configuração do provisionamento automático do usuário para Snowflake 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Snowflake com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para O Floco de Neve, seguindo as instruções fornecidas no [tutorial de signon -on do Snowflake Single](Snowflake-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Floco de Neve no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **floco de neve**.

    ![O link de floco de neve na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de `https://<Snowflake Account URL>/scim/v2` Admin, entrada na URL do inquilino. Um exemplo da URL do inquilino:`https://acme.snowflakecomputing.com/scim/v2`

6. Insira o valor **do Token de Autenticação SCIM** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Floco de Neve. Se a conexão falhar, certifique-se de que sua conta snowflake tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para Snowflake**.

    ![Mapeamentos de usuários de flocos de neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Revise os atributos do usuário sincronizados do Azure AD para o Snowflake na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Snowflake para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do floco de neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para Floco de Neve**.

    ![Mapeamentos do Grupo Floco de Neve](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Revise os atributos de grupo sincronizados do Azure AD para o Snowflake na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos em Floco de Neve para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo Floco de Neve](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento Azure AD para Floco de Neve, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar para o Snowflake escolhendo os valores desejados no **Escopo** na seção **Configurações.** Se essa opção não estiver disponível, configure os campos necessários em Credenciais de Administrador, clique **em Salvar** e atualize a página. 

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Snowflake.

    Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens SCIM gerados por Flocos de Neve expiram em 6 meses. Esteja ciente de que estes precisam ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem funcionando. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas
* [Saiba como revisar registros e obter relatórios sobre atividades de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
