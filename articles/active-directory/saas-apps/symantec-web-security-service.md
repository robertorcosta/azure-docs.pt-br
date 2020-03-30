---
title: 'Tutorial: Configure o Symantec Web Security Service (WSS) para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Symantec Web Security Service (WSS).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063111"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Tutorial: Configure o Symantec Web Security Service (WSS) para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Symantec Web Security Service (WSS) e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Symantec Web Security Service (WSS).

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino do Symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uma conta de usuário no Symantec Web Security Service (WSS) com permissões de administração.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Atribuindo usuários ao Symantec Web Security Service (WSS)

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Symantec Web Security Service (WSS). Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Symantec Web Security Service (WSS) seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Dicas importantes para atribuir usuários ao Symantec Web Security Service (WSS)

* Recomenda-se que um único usuário azure AD seja atribuído ao Symantec Web Security Service (WSS) para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao WSS (Symantec Web Security Service, serviço de segurança web da Symantec), você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurar O Serviço de Segurança Web (WSS) da Symantec para provisionamento

Antes de configurar o Symantec Web Security Service (WSS) para provisionamento automático do usuário com o Azure AD, você precisará habilitar o provisionamento scim no Symantec Web Security Service (WSS).

1. Faça login no seu console de [admin symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Navegue até o**Serviço** **de Soluções** > .

    ![Serviço de Segurança Web da Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Navegue até **integrações de manutenção** > **de contas** > **Nova integração**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecione **usuários de terceiros & sincronização de grupos**. 

    ![Serviço de Segurança Web da Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Copie a URL e **o Token** **SCIM** . Esses valores serão inseridos no campo **URL do inquilino** e no campo **Token Secreto** na guia Provisionamento do aplicativo Symantec Web Security Service (WSS) no portal Azure.

    ![Serviço de Segurança Web da Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Adicionar O Serviço de Segurança Web da Symantec (WSS) na galeria

Para configurar o Symantec Web Security Service (WSS) para provisionamento automático de usuários com o Azure AD, você precisa adicionar o Symantec Web Security Service (WSS) da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Symantec Web Security Service (WSS) na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o Serviço de Segurança web da Symantec,** selecione o Serviço de Segurança Web da **Symantec** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Symantec Web Security Service (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configuração do provisionamento automático do usuário para o Symantec Web Security Service (WSS)

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Symantec Web Security Service (WSS) com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Symantec Web Security Service (WSS), seguindo as instruções fornecidas no tutorial de login único do [Symantec Web Security Service (WSS).](symantec-tutorial.md) O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Symantec Web Security Service (WSS) no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Symantec Web Security Service**.

    ![O link do Symantec WSS (Web Security Service) na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, insira os valores de URL e **Token** **SCIM** recuperados anteriormente em **URL de inquilino** e **Token Secreto,** respectivamente. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Serviço de Segurança Web da Symantec. Se a conexão falhar, certifique-se de que sua conta Symantec Web Security Service (WSS) tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Diretório Ativo do Azure para o WSS (Web Security Service, serviço de segurança web da Symantec).**

    ![Mapeamentos de usuários do Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Symantec Web Security Service (WSS) na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Symantec Web Security Service (WSS) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Mapeamentos de usuários do Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para o Serviço de Segurança Web da Symantec**.

    ![Mapeamentos de usuários do Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o Symantec Web Security Service (WSS) na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Symantec Web Security Service (WSS) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Mapeamentos de usuários do Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para o Serviço de Segurança Web da Symantec, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de prover ao Symantec Web Security Service (WSS) escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo levará para os usuários e/ou grupos provisionarem, consulte [quanto tempo levará para provisionar os usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Symantec Web Security Service (WSS). Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
