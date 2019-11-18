---
title: 'Tutorial: configurar o RingCentral para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8aa6a8bfb690b00ee21980f4aa64d950abec2f3c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152665"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Tutorial: configurar o RingCentral para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no RingCentral e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no RingCentral.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do RingCentral](https://www.ringcentral.com/office/plansandpricing.html)
* Uma conta de usuário no RingCentral com permissões de administrador.

## <a name="assigning-users-to-ringcentral"></a>Atribuindo usuários ao RingCentral

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao RingCentral. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao RingCentral seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ringcentral"></a>Dicas importantes para atribuir usuários ao RingCentral

* É recomendável que um único usuário do Azure AD seja atribuído ao RingCentral para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao RingCentral, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-ringcentral-for-provisioning"></a>Configurar o RingCentral para provisionamento

1. Entre no console do [administrador do RingCentral](https://login.ringcentral.com/sw.html). Navegue até **ferramentas > integração de diretórios**.

    ![Console de administração do RingCentral](media/ringcentral-provisioning-tutorial/admin.png)

2.  Escolha **scim** em **Selecionar provedor de diretório**. (No futuro, haverá uma opção chamada Azure Active Directory). Clique em **habilitar serviço scim**.

    ![RingCentral adicionar SCIM](media/ringcentral-provisioning-tutorial/scim.png)

3.  Contate a equipe de suporte do RingCentral em matthew.hunt@ringcentral.com para obter um **token de autenticação scim**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo RingCentral no portal do Azure.

> [!NOTE]
> Para atribuir licenças a usuários, consulte o link de vídeo [aqui](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="add-ringcentral-from-the-gallery"></a>Adicionar o RingCentral da Galeria

Antes de configurar o RingCentral para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o RingCentral da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RingCentral da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **RingCentral**, selecione **RingCentral** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![RingCentral na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ringcentral"></a>Configurando o provisionamento automático de usuário para o RingCentral 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no RingCentral com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o RingCentral, seguindo as instruções fornecidas no [tutorial de logon único do RingCentral](https://docs.microsoft.comazure/active-directory/saas-apps/ringcentral-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade de SCIM do RingCentral, consulte [referência de API do RingCentral](https://developers.ringcentral.com/api-reference).

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para RingCentral no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **RingCentral**.

    ![O link do RingCentral na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://platform.ringcentral.com/scim/v2` na **URL do locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao RingCentral. Se a conexão falhar, verifique se sua conta do RingCentral tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para RingCentral**.

    ![Mapeamentos de usuário RingCentral](media/ringcentral-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o RingCentral na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no RingCentral para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do RingCentral](media/ringcentral-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o RingCentral, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o RingCentral escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no RingCentral.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

