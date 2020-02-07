---
title: 'Tutorial: configurar o MerchLogix para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o MerchLogix.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061287"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: configurar o MerchLogix para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no MerchLogix e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no MerchLogix.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisites

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um locatário do MerchLogix
* Um contato técnico em MerchLogix que pode fornecer a URL do ponto de extremidade do SCIM e o token secreto necessários para o provisionamento de usuários

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionando o MerchLogix da Galeria

Antes de configurar o MerchLogix para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o MerchLogix da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MerchLogix da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]

3. Para adicionar o MerchLogix, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix**e, em seguida, clique no botão **Adicionar** para adicionar o MerchLogix à lista de aplicativos SaaS.

    ![Provisionamento do MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuindo usuários ao MerchLogix

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao MerchLogix. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao MerchLogix seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir usuários ao MerchLogix

* É recomendável que um único usuário do Azure AD seja atribuído ao MerchLogix para testar a configuração de provisionamento automático de usuário inicial. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.

* Ao atribuir um usuário ao MerchLogix, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurando o provisionamento automático de usuário para o MerchLogix 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no MerchLogix com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o MerchLogix, seguindo as instruções fornecidas no [tutorial de logon único do MerchLogix](merchlogix-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para MerchLogix no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione MerchLogix na lista de aplicativos SaaS.

3. Selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Na seção **credenciais de administrador** :

    * No campo **URL do locatário** , insira a URL do ponto de extremidade scim fornecida pelo seu contato técnico do MerchLogix.

    * No campo **token secreto** , insira o token secreto fornecido pelo seu contato técnico do MerchLogix.

6. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao MerchLogix. Se a conexão falhar, verifique se sua conta do MerchLogix tem permissões de administrador e tente novamente.

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

8. Clique em **Save** (Salvar).

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para MerchLogix**.

10. Examine os atributos de usuário que são sincronizados do Azure AD para o MerchLogix na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

11. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para MerchLogix**.

12. Examine os atributos de grupo que são sincronizados do Azure AD para o MerchLogix na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD para o MerchLogix, altere o **status de provisionamento** para **ativado** na seção **configurações** .

14. Quando estiver pronto para provisionar, clique em **Salvar**.

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no MerchLogix.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
