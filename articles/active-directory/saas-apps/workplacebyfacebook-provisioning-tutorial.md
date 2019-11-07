---
title: 'Tutorial: configurar o Workplace by Facebook para o provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee091d1c8f0f477354f6bb422d041278ec5668e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574260"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: configurar o Workplace by Facebook para provisionamento automático de usuários

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no Workplace by Facebook e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Workplace by Facebook.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workplace by Facebook, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workplace by Facebook habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Atribuição de usuários ao Workplace by Facebook

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Workplace by Facebook. Depois de decidir, atribua esses usuários ao seu aplicativo Workplace by Facebook seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Dicas importantes para atribuir usuários ao Workplace by Facebook

*   Recomendamos a atribuição de um único usuário do Azure AD ao Workplace by Facebook para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Workplace by Facebook, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Habilitar o provisionamento de usuário

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do Workplace by Facebook e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Workplace by Facebook, com base na atribuição de usuário e de grupo do Azure AD.

>[!Tip]
>Você também pode optar por habilitar o Logon Único baseado em SAML no Workplace by Facebook, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Para configurar o provisionamento de conta de usuário para o Workplace by Facebook no Azure AD:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Workplace by Facebook.

O Azure AD dá suporte à capacidade de sincronizar automaticamente os detalhes da conta de usuários atribuídos ao Workplace by Facebook. Essa sincronização automática permite que o Workplace by Facebook obtenha os dados necessários para autorizar o acesso aos usuários, antes que eles tentem se conectar pela primeira vez. Ele também desprovisiona os usuários do Workplace by Facebook quando o acesso tiver sido revogado no Azure AD.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

2. Se você já tiver configurado o Workplace by Facebook para logon único, pesquise a instância do Workplace by Facebook usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Workplace by Facebook** na galeria de aplicativos. Selecione o Workplace by Facebook nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do Workplace by Facebook e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, insira o Token de Acesso do administrador do Workplace by Facebook e defina o valor da URL de locatário como `https://www.facebook.com/scim/v1/` . Consulte essas [instruções](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) sobre como criar um Token de Acesso para o Workplace. 

6. No Portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao aplicativo Workplace by Facebook. Se a conexão falhar, verifique se sua conta do Workplace by Facebook tem permissões de Administrador de Equipe.

7. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

8. Clique em **Salvar.**

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Workplace by Facebook.**

10. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Workplace by Facebook. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Workplace by Facebook em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD no Workplace by Facebook, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

12. Clique em **Salvar.**

Para obter mais informações sobre como configurar o provisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foi sincronizada com o Workplace by Facebook.

> [!NOTE]
> Estamos trabalhando junto com a equipe do workplace by Facebook para garantir que o aplicativo do Azure AD seja aprovado e atenda às suas novas diretrizes. O local de trabalho dos prazos do Facebook é 16 de dezembro e esperamos atendê-lo. Nenhum trabalho é esperado para os clientes nesse momento. Em 28 de fevereiro de 2020, os clientes precisarão fazer a transição para a nova integração. Postaremos aqui assim que o caminho de migração estiver disponível.    

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](workplacebyfacebook-tutorial.md)
