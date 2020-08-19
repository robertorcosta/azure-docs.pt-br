---
title: 'Tutorial: configurar o DocuSign para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 5b4e74d5db2d1454360370c05d75cdf826875143
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535927"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: configurar o DocuSign para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no DocuSign e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o DocuSign.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura habilitada para logon único do DocuSign.
*   Uma conta de usuário no DocuSign com permissões de Administrador de Equipe.

## <a name="assigning-users-to-docusign"></a>Atribuindo usuários ao DocuSign

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo DocuSign. Depois de decidir, atribua esses usuários ao aplicativo DocuSign seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Dicas importantes para atribuir usuários ao DocuSign

*   Recomendamos a atribuição de um único usuário do Azure AD ao DocuSign para testar a configuração de provisionamento. Outros usuários podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao DocuSign, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

> [!NOTE]
> O Azure Active Directory não oferece suporte para o provisionamento de grupo com o aplicativo do Docusign, somente os usuários podem ser provisionados.

## <a name="enable-user-provisioning"></a>Habilitar o provisionamento de usuário

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do DocuSign e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no DocuSign, com base na atribuição de usuário e de grupo do Azure AD.

> [!Tip]
> Você também pode optar por habilitar o Logon Único baseado em SAML no DocuSign, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no DocuSign.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

1. Se você já tiver configurado o DocuSign para logon único, pesquise a instância do DocuSign usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **DocuSign** na galeria de aplicativos. Selecione o DocuSign nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione a instância do DocuSign e, depois, a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/docusign-provisioning-tutorial/provisioning.png)

1. Na seção **Credenciais de Administrador**, forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Nome de Usuário Administrador**, digite um nome de conta do DocuSign que tem o perfil **Administrador do Sistema** atribuído no DocuSign.com.
   
    b. Na caixa de texto **Senha do Administrador**, digite a senha dessa conta.

> [!NOTE]
> Se o SSO e o provisionamento de usuário forem configurados, as credenciais de autorização usadas para o provisionamento precisarão ser definidas para funcionar com o SSO e o nome de usuário/senha.

1. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD pode se conectar ao aplicativo DocuSign.

1. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção.

1. Clique em **Salvar.**

1. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o DocuSign.**

1. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o DocuSign. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do DocuSign em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD no DocuSign, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

1. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários atribuídos ao DocuSign na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo DocuSign.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar logon único](docusign-tutorial.md)
