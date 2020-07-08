---
title: 'Tutorial: configurar o Leapsome para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Leapsome.
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e5837887325c06f9140a3f40eb183139782e2a50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057457"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Tutorial: configurar o Leapsome para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Leapsome e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Leapsome.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* Um locatário do [Leapsome](https://www.Leapsome.com/en/pricing) .
* Uma conta de usuário no Leapsome com permissões de administrador.

## <a name="assigning-users-to-leapsome"></a>Atribuindo usuários ao Leapsome

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Leapsome. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Leapsome seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Dicas importantes para atribuir usuários ao Leapsome

* É recomendável que um único usuário do Azure AD seja atribuído ao Leapsome para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Leapsome, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.


## <a name="setup-leapsome-for-provisioning"></a>Configurar o Leapsome para provisionamento

1. Entre no console do [administrador do Leapsome](https://www.Leapsome.com/app/#/login). Navegue até **configurações > configurações de administrador**.

    ![Console de administração do Leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navegue até **integrações > provisionamento de usuário scim**.

    ![Leapsome adicionar SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Copie o **token de autenticação scim**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo Leapsome no portal do Azure.

    ![Leapsome criar token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Adicionar o Leapsome da Galeria

Antes de configurar o Leapsome para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Leapsome da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Leapsome da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Leapsome**, selecione **Leapsome** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Leapsome na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Configurando o provisionamento automático de usuário para o Leapsome 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Leapsome com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Leapsome, seguindo as instruções fornecidas no [tutorial de logon único do Leapsome](Leapsome-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Leapsome no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Leapsome**.

    ![O link do Leapsome na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://www.leapsome.com/api/scim` a **URL de locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Leapsome. Se a conexão falhar, verifique se sua conta do Leapsome tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Leapsome**.

    ![Mapeamentos de usuário Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Leapsome na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Leapsome para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Leapsome**.

    ![Mapeamentos de grupo Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Leapsome na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Leapsome para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Leapsome, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Leapsome escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Leapsome.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Leapsome exige que o **nome de usuário** seja exclusivo.
* Leapsome só permite que os endereços de email de trabalho sejam salvos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
