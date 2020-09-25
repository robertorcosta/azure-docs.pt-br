---
title: 'Tutorial: configurar o Proxyclick para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb89e4e85e18e00179b894f2587bcf4127dc22
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255817"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: configurar o Proxyclick para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Proxyclick e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Proxyclick.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de usuário no Proxyclick com permissões de administrador.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar o Proxyclick por meio da galeria

Antes de configurar o Proxyclick para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Proxyclick da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Proxyclick da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Proxyclick**, selecione **Proxyclick** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuindo usuários ao Proxyclick

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Proxyclick. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Proxyclick seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir usuários ao Proxyclick

* É recomendável que um único usuário do Azure AD seja atribuído ao Proxyclick para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Proxyclick, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurando o provisionamento automático de usuário para o Proxyclick 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Proxyclick com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Proxyclick, seguindo as instruções fornecidas no [tutorial de logon único do Proxyclick](proxyclick-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Proxyclick no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Proxyclick**.

    ![O link Proxyclick na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Para recuperar a **URL do locatário** e o **token secreto** da sua conta do Proxyclick, siga o passo a passos, conforme descrito na etapa 6.

6. Entre no console do [administrador do Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegue até **configurações**  >  **integrações**  >  **procurar Marketplace**.

    ![Configurações de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **Microsoft Azure Active Directory**. Clique em **Instalar agora**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick instalar](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione **provisionamento de usuário** e clique em **Iniciar integração**. 

    ![Provisionamento de usuário do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A interface do usuário de configuração das configurações apropriadas agora **Settings**deve aparecer em  >  **integrações**de configurações. Selecione **configurações** no **Azure AD (provisionamento de usuário)**.

    ![Proxyclick criar](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Você pode encontrar a **URL do locatário** e o **token secreto** aqui.

    ![Proxyclick criar token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Proxyclick. Se a conexão falhar, verifique se sua conta do Proxyclick tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Proxyclick**.

    ![Mapeamentos de usuário Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Proxyclick na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Proxyclick para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o Proxyclick, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você deseja provisionar para o Proxyclick escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Proxyclick.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Proxyclick exige que **emails** e o **nome de usuário** tenham o mesmo valor de origem. Todas as atualizações para ambos os atributos modificarão o outro valor.
* O Proxyclick não oferece suporte ao provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

