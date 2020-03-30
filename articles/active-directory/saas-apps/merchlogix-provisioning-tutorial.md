---
title: 'Tutorial: Configure o MerchLogix para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061287"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configure merchLogix para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no MerchLogix e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o MerchLogix.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um inquilino merchLogix
* Um contato técnico no MerchLogix que pode fornecer o URL de ponto final scim e o token secreto necessário para o provisionamento do usuário

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionando MerchLogix da galeria

Antes de configurar o MerchLogix para provisionamento automático do usuário com o Azure AD, você precisa adicionar o MerchLogix da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MerchLogix na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos Corporativos** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]

3. Para adicionar MerchLogix, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix**e clique no botão **Adicionar** para adicionar MerchLogix à sua lista de aplicativos SaaS.

    ![Provisionamento MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuindo usuários ao MerchLogix

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao MerchLogix. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao MerchLogix seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir usuários ao MerchLogix

* Recomenda-se que um único usuário Azure AD seja atribuído ao MerchLogix para testar sua configuração inicial de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.

* Ao atribuir um usuário ao MerchLogix, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configuração do provisionamento automático do usuário para MerchLogix 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no MerchLogix com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o logon único baseado em SAML para o MerchLogix, seguindo as instruções fornecidas no [tutorial de logon único do MerchLogix](merchlogix-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para MerchLogix no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione MerchLogix na sua lista de aplicativos SaaS.

3. Selecione a guia **Provisionamento**.

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Na seção Credenciais de **Admin:**

    * No campo URL do **inquilino,** digite a URL de ponto final SCIM fornecida pelo seu contato técnico MerchLogix.

    * No campo **Token Secreto,** digite token secreto fornecido pelo seu contato técnico MerchLogix.

6. Ao preencher os campos mostrados no Passo 5, clique em **Conexão de teste** para garantir que o Azure AD possa se conectar ao MerchLogix. Se a conexão falhar, certifique-se de que sua conta MerchLogix tenha permissões de administração e tente novamente.

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

8. Clique em **Salvar**.

9. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para MerchLogix**.

10. Revise os atributos do usuário sincronizados do Azure AD para o MerchLogix na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

11. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para MerchLogix**.

12. Revise os atributos de grupo sincronizados do Azure AD para o MerchLogix na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

13. Para habilitar o serviço de provisionamento Azure AD para MerchLogix, altere o **status de provisionamento** para **Ativado** na seção **Configurações.**

14. Quando estiver pronto para provisionar, clique em **Salvar**.

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no MerchLogix.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
