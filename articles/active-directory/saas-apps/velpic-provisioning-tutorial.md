---
title: 'Tutorial: Configuração do Velpic para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064114"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurando velpic para provisionamento automático do usuário

O objetivo deste tutorial é mostrar os passos que você precisa executar no Velpic e no Azure AD para provisionar e desprovisionar automaticamente contas de usuários do Azure AD para o Velpic.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um inquilino velpic com o [plano Enterprise](https://www.velpic.com/pricing.html) ou melhor habilitado
* Uma conta de usuário em Velpic com permissões de administração

## <a name="assigning-users-to-velpic"></a>Atribuindo usuários ao Velpic

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Velpic. Uma vez decidido, você pode atribuir esses usuários ao seu aplicativo Velpic seguindo as instruções aqui:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir usuários ao Velpic

* Recomenda-se que um único usuário azure AD seja designado ao Velpic para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Velpic, você deve selecionar a função **Usuário** ou outra função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Observe que a **função Default Access** não funciona para provisionamento, e esses usuários serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configuração do provisionamento do usuário para velpic

Esta seção orienta você através da conexão do Seu Azure AD à API de provisionamento de contas de usuário da Velpic e configurando o serviço de provisionamento para criar, atualizar e desativar contas de usuário atribuídas no Velpic com base na atribuição de usuário e grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o Single Sign-On baseado em SAML para Velpic, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o provisionamento automático da conta de usuário para velpic no Azure AD:

1. No [portal Azure,](https://portal.azure.com)navegue até a seção **Azure Active Directory > Enterprise Apps > All.**

2. Se você já configurou o Velpic para o único login, procure por sua instância de Velpic usando o campo de pesquisa. Caso contrário, **selecione Adicionar** e procurar **por Velpic** na galeria de aplicativos. Selecione Velpic nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

3. Selecione sua instância de Velpic e selecione a guia **Provisionamento.**

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Na seção Credenciais de **Admin,** insira a **URL do inquilino&sinal secreto** do Velpic. (Você pode encontrar esses valores em sua conta Velpic: **Gerenciar** > o**Plugin** > de**Integração** > **SCIM**)

    ![Valores de Autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal Azure, clique em **Conexão de Teste** para garantir que o Azure AD possa se conectar ao seu aplicativo Velpic. Se a conexão falhar, certifique-se de que sua conta Velpic tenha permissões de administração e tente o passo 5 novamente.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

8. Clique em **Salvar**.

9. Na seção Mapeamentos, selecione **Sincronizar usuários do diretório ativo do Azure para Velpic**.

10. Na seção Mapeamentos de **atributos,** revise os atributos do usuário que serão sincronizados do Azure AD para o Velpic. Observe que os atributos selecionados como **propriedades correspondentes** serão usados para corresponder às contas de usuário no Velpic para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento Azure AD para Velpic, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

12. Clique em **Salvar**.

Isso iniciará a sincronização inicial de quaisquer usuários e/ou grupos atribuídos ao Velpic na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)