---
title: 'Tutorial: Configure Zscaler Private Access (ZPA) para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para Zscaler Private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064131"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Private Access (ZPA) para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler Private Access (ZPA) e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para Zscaler Private Access (ZPA).

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino Zscaler Private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uma conta de usuário no Zscaler Private Access (ZPA) com permissões de administração.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Atribuindo usuários ao Zscaler Private Access (ZPA)

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Zscaler Private Access (ZPA). Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Zscaler Private Access (ZPA) seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Dicas importantes para atribuir usuários ao Zscaler Private Access (ZPA)

* Recomenda-se que um único usuário azure AD seja atribuído ao Zscaler Private Access (ZPA) para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler Private Access (ZPA), você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurar zscaler private access (ZPA) para provisionamento

1. Faça login no seu console de [admin Zscaler Private Access (ZPA).](https://admin.private.zscaler.com/) Navegue até a **configuração de IdP de administração >**.

    ![Zscaler Private Access (ZPA) Admin Console](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verifique se um IdP para **a adesão única** está configurado. Se nenhum IdP estiver configurado, adicione um clicando no ícone de adição no canto superior direito da tela.

    ![Zscaler Private Access (ZPA) Adicionar SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Siga o **assistente de configuração Add IdP** para adicionar um IdP. Deixe o **campo de sinalização único** definido para o **Usuário**. Forneça um **nome** e selecione os **Domínios** na lista de desímparadas. Clique em **Next** para navegar até a próxima janela.

    ![Zscaler Private Access (ZPA) Adicionar IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Baixe o **Certificado do Provedor de Serviços**. Clique em **Next** para navegar até a próxima janela.

    ![Certificado zscaler private access (ZPA) SP](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Na janela seguinte, faça upload do **Certificado do Provedor de Serviços** baixado anteriormente.

    ![Certificado de upload zscaler private access (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Role para baixo para fornecer a **URL de login único** e o **ID ID da entidade de IdP**.

    ![IdD de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Role para baixo para **ativar o SCIM Sync**. Clique em Gerar novo botão **de token.** Copie o **Token do Portador**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Zscaler Private Access (ZPA) no portal Azure.

    ![Zscaler Private Access (ZPA) Criar Token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Para localizar a URL do **inquilino,** navegue até a **configuração de IdP de administração >**. Clique no nome da configuração de IdP recém-adicionada listada na página.

    ![Nome de Idp de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Role para baixo para ver o **ponto final do provedor de serviços SCIM** no final da página. Copie o **ponto final do provedor de serviços SCIM**. Esse valor será inserido no campo URL do Inquilino na guia Provisionamento do aplicativo Zscaler Private Access (ZPA) no portal Azure.

    ![Url SCIM de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicionar Zscaler Private Access (ZPA) na galeria

Antes de configurar o Zscaler Private Access (ZPA) para provisionamento automático do usuário com o Azure AD, você precisa adicionar zscaler Private Access (ZPA) da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Zscaler Private Access (ZPA) na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler Private Access (ZPA)**, selecione **Zscaler Private Access (ZPA)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![ZPA (Zscaler Private Access) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configuração do provisionamento automático do usuário para Zscaler Private Access (ZPA) 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Zscaler Private Access (ZPA) com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o logon único baseado em SAML para Zscaler Private Access (ZPA) seguindo as instruções fornecidas no [tutorial de logon único do Zscaler Private Access (ZPA).](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final scim do Zscaler Private Access, consulte [isso](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Zscaler Private Access (ZPA) no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)**.

    ![O link do ZPA (Zscaler Private Access) na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** insira o valor **de ponto final do provedor de serviços SCIM** recuperado anteriormente na URL do **inquilino**. Insira o valor **do Token do Portador** recuperado anteriormente em Secret **Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Zscaler Private Access (ZPA). Se a conexão falhar, certifique-se de que sua conta Zscaler Private Access (ZPA) tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Diretório Ativo do Azure para Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) Mapeamentos de usuários](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Zscaler Private Access (ZPA) na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário zscaler private access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de Diretórios Ativos do Azure para Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) Mapeamentos de grupos](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o Zscaler Private Access (ZPA) na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Zscaler Private Access (ZPA), altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar ao Zscaler Private Access (ZPA) escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Zscaler Private Access (ZPA).

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

