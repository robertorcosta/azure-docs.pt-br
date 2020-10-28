---
title: 'Tutorial: configurar o NetSuite OneWorld para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8406ee5647d02cc917a0fdb1daf2355611bb781d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792201"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configurando o NetSuite para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no NetSuite OneWorld e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o NetSuite.

> [!NOTE]
> Essa integração atualmente se autentica usando a autenticação básica (nome de usuário e senha). O NetSuite implementou um requisito de autenticação multifator que impede que os clientes usem essa integração, a menos que tenham uma isenção desse requisito. Estamos trabalhando com o NetSuite para atualizar essa integração com um método de autenticação mais recente para permitir que os clientes sem uma isenção o usem novamente. Atualizaremos este documento com uma ETA quando houver uma disponível.

Ação recomendada: Aguarde até que lancemos uma atualização para o comportamento de autenticação dessa integração ou entrem em contato com o suporte do NetSuite para consultar sobre uma isenção para o requisito de autenticação multifator.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura do NetSuite OneWorld. Observe que o provisionamento automático de usuários atualmente é suportado apenas com o NetSuite OneWorld.
*   Uma conta de usuário no Netsuite com permissões de administrador.
*   A integração com o Azure AD requer uma isenção de 2FA. Entre em contato com a equipe de suporte do NetSuite para solicitar essa isenção.

## <a name="assigning-users-to-netsuite-oneworld"></a>Atribuindo usuários ao NetSuite OneWorld

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo NetSuite. Depois de decidir, você pode atribuir esses usuários ao aplicativo NetSuite seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Dicas importantes para atribuir usuários ao NetSuite OneWorld

*   É recomendável que um único usuário do Azure AD seja atribuído ao NetSuite para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao NetSuite, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Habilitar o provisionamento de usuário

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do NetSuite e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no NetSuite com base na atribuição de usuário e de grupo no Azure AD.

> [!TIP] 
> Você também pode optar por habilitar o Sign-On único baseado em SAML para o NetSuite, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de usuário de Active Directory contas de usuário para o NetSuite.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos** .

1. Se você já tiver configurado o NetSuite para logon único, pesquise sua instância do NetSuite usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **NetSuite** na Galeria de aplicativos. Selecione NetSuite nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do NetSuite e, em seguida, selecione a guia **provisionamento** .

1. Defina o **Modo de Provisionamento** como **Automático** . 

    ![Captura de tela mostra a página de provisionamento do NetSuite, com o modo de provisionamento definido como automático e outros valores que você pode definir.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Na seção **Credenciais de Administrador** , forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **nome de usuário administrador** , digite um nome de conta do NetSuite que tenha o perfil de **administrador do sistema** em NetSuite.com atribuído.
   
    b. Na caixa de texto **Senha do Administrador** , digite a senha dessa conta.
      
1. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao aplicativo NetSuite.

1. No campo **Email de Notificação** , insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção.

1. Clique em **Salvar.**

1. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para o NetSuite.**

1. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o NetSuite. Observe que os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no NetSuite para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD para o NetSuite, altere o **status de provisionamento** para **ativado** na seção Configurações

1. Clique em **Salvar.**

Ele inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao NetSuite na seção usuários e grupos. Observe que a sincronização inicial leva mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo NetSuite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar logon único](netsuite-tutorial.md)
