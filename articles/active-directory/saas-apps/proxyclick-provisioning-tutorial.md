---
title: 'Tutorial: Configure proxyclick para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para proxyclick.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063349"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Configure proxyclick para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Proxyclick e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para proxyclick.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de usuário em Proxyclick com permissões de administração.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar o Proxyclick por meio da galeria

Antes de configurar o Proxyclick para provisionamento automático do usuário com o Azure AD, você precisa adicionar proxyclick da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar proxyclick na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Proxyclick**, selecione **Proxyclick** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuindo usuários ao Proxyclick

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Proxyclick. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Proxyclick seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir usuários ao Proxyclick

* Recomenda-se que um único usuário azure AD seja atribuído ao Proxyclick para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Proxyclick, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configuração do provisionamento automático do usuário para Proxyclick 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos em Proxyclick com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para proxyclick, seguindo as instruções fornecidas no [tutorial de login único do Proxyclick](proxyclick-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para proxyclick no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Proxyclick**.

    ![O link Proxyclick na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Para recuperar a **URL do inquilino** e o **token secreto** da sua conta Proxyclick, siga o passo a passo conforme descrito na Etapa 6.

6. Faça login no seu [console de admin proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegue até **configurações** > **Integrações** > **Navegue pelo Mercado**de Navegação .

    ![Configurações do proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **Microsoft Azure Active Directory**. Clique em **Instalar agora**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalação proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione **Provisionamento do usuário** e clique **em Iniciar a integração**. 

    ![Provisionamento do usuário proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A uI de configuração de configurações apropriada deve agora aparecer em **Configurações** > **Integrações**. Selecione **Configurações** em **Azure AD (Provisionamento de usuário)**.

    ![Proxyclick Criar](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Você pode encontrar a **URL do inquilino** e o **Token Secreto** aqui.

    ![Proxyclick Criar token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Ao preencher os campos mostrados na Etapa 5, clique em **Conexão de teste** para garantir que o Azure AD possa se conectar ao Proxyclick. Se a conexão falhar, certifique-se de que sua conta Proxyclick tenha permissões de administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos,** selecione Sincronizar usuários do diretório ativo do **Azure para proxyclick**.

    ![Mapeamentos de usuários proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Revise os atributos do usuário sincronizados do Azure AD para o Proxyclick na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Proxyclick para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento Azure AD para Proxyclick, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar para proxyclick escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Proxyclick.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Proxyclick requer **e-mails** e **userName** para ter o mesmo valor de origem. Quaisquer atualizações para ambos os atributos modificarão o outro valor.
* Proxyclick não suporta provisionamento para grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

