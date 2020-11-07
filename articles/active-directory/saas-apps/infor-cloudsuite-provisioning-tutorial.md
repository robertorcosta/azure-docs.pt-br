---
title: 'Tutorial: configurar o infor CloudSuite para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a62afa9469caa886d86814036017427c0cc0d193
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357429"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: configurar infor CloudSuite para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no infor CloudSuite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de usuário no infor CloudSuite com permissões de administrador.

## <a name="assigning-users-to-infor-cloudsuite"></a>Atribuindo usuários ao infor CloudSuite

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao infor CloudSuite. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao infor CloudSuite seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir usuários ao infor CloudSuite

* É recomendável que um único usuário do Azure AD seja atribuído a infor CloudSuite para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao infor CloudSuite, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar o infor CloudSuite para provisionamento

1. Entre no console de [Administração do infor CloudSuite](https://www.infor.com/customer-center). Clique no ícone de usuário e navegue até **Gerenciamento de usuário**.

    ![Console de administração do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone de menu no canto superior esquerdo da tela. Clique em **gerenciar**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegue até **contas do scim**.

    ![Conta do infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um usuário administrador clicando no ícone de adição. Forneça uma **senha do scim** e digite a mesma senha em **Confirmar senha**. Clique no ícone de pasta para salvar a senha. Em seguida, você verá um **identificador de usuário** gerado para o usuário administrador.

    ![Usuário administrador do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite senha](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Captura de tela do console de administração do infor CloudSuite mostrando uma linha de tabela realçada. Essa linha contém um identificador de usuário, senhas e um carimbo de data/hora." border="false":::

5. Para gerar o token de portador, copie o **identificador de usuário** e a **senha scim**. Cole-os no bloco de notas + + separados por dois-pontos. Codifique o valor da cadeia de caracteres navegando até **plug-ins > ferramentas MIME > codificação Basic64**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Captura de tela de um documento do bloco de notas + +. No menu plug-ins, as ferramentas MIME são realçadas. No menu ferramentas MIME, a codificação Base64 é realçada." border="false":::

3.  Copie o token de portador. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo infor CloudSuite no portal do Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicionar o infor CloudSuite da Galeria

Antes de configurar o infor CloudSuite para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o infor CloudSuite da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o infor CloudSuite da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa** , em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **infor CloudSuite** , selecione **infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurando o provisionamento automático de usuário para infor CloudSuite 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no infor CloudSuite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para infor CloudSuite, seguindo as instruções fornecidas no tutorial de [logon único do infor CloudSuite](./infor-cloud-suite-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do infor CloudSuite, confira [isso](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para infor CloudSuite no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Infor CloudSuite**.

    ![O Infor CloudSuite na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` a **URL de locatário**. Insira o valor do token de portador recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao infor CloudSuite. Se a conexão falhar, verifique se sua conta do infor CloudSuite tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email** , insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para infor CloudSuite**.

    ![Mapeamentos de usuário do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para infor CloudSuite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para infor CloudSuite**.

    ![Mapeamentos de grupo infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para infor CloudSuite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Infor atributos de grupo CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para infor CloudSuite, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para infor CloudSuite escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no infor CloudSuite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)