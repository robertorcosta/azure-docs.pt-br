---
title: Adicionar um fluxo dos usuários de inscrição por autoatendimento — Azure AD
description: Federar com o Facebook para permitir que usuários externos (convidados) entrem em seus aplicativos do Azure AD com suas próprias contas do Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfd430d750b2220882479a430322f4b4c4e0c44c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594725"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Adicionar um fluxo do usuário de inscrição por autoatendimento a um aplicativo (visualização)
|     |
| --- |
| A inscrição por autoatendimento é a versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Associar seu fluxo de usuário a um aplicativo permite que você habilite a inscrição nesse aplicativo. Você pode escolher mais de um aplicativo a ser associado ao fluxo do usuário. Depois de associar o fluxo do usuário a um ou mais aplicativos, os usuários que visitarem esse aplicativo poderão se inscrever usando as opções configuradas no fluxo do usuário.

## <a name="before-you-begin"></a>Antes de começar

### <a name="add-social-identity-providers-optional"></a>Adicionar provedores de identidade social (opcional)

O Azure AD é o provedor de identidade padrão para inscrição de autoatendimento. Isso significa que os usuários podem se inscrever por padrão com uma conta do Azure AD. Os provedores de identidade social também podem ser incluídos nesses fluxos de inscrição para dar suporte a contas do Google e do Facebook.

- [Adicionar o Google à sua lista de provedores de identidade social](google-federation.md)
- [Adicionar o Facebook à sua lista de provedores de identidade social](facebook-federation.md)
 
### <a name="define-custom-attributes-optional"></a>Definir atributos personalizados (opcional)

Os atributos de usuário são valores coletados do usuário durante a inscrição de autoatendimento. O Azure AD vem com um conjunto interno de atributos, mas você pode criar atributos personalizados para uso em seu fluxo de usuário. Você também pode ler e gravar esses atributos usando a API do Microsoft Graph. Consulte [ atributos personalizados para fluxos de usuário](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Habilitar inscrição de autoatendimento para seu locatário

Para poder adicionar um fluxo de usuário de inscrição de autoatendimento aos seus aplicativos você precisa habilitar o recurso para seu locatário. Depois de habilitado, os controles ficam disponíveis no fluxo do usuário que permite associar o fluxo do usuário a um aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. Selecione **Configurações de usuário** e, em **Usuários externos**, selecione **Gerenciar configurações de colaboração externa**.
4. Alterne a opção **Habilitar a inscrição de autoatendimento de convidado por meio de fluxos de usuário (versão prévia)** para **Sim**.

   ![Habilitar inscrição de autoatendimento de convidado](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>Criar o fluxo de usuário para inscrição de autoatendimento

Em seguida, você criará o fluxo de usuário para inscrição de autoatendimento e o adicionará a um aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **Fluxos dos usuários (versão prévia)** e, em seguida, selecione **Novo fluxo de usuário**.

   ![Botão Adicionar um novo fluxo de usuário](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na página **Criar**, insira um **Nome** para o fluxo de usuário. Observe que o nome é automaticamente prefixado com **B2X_1_** .
6. Na lista **Provedores de identidade**, selecione um ou mais provedores de identidade que os usuários externos podem usar para fazer logon em seu aplicativo. A **inscrição no Azure Active Directory** é selecionada por padrão. (Consulte [Antes de começar](#before-you-begin) no início deste artigo para aprender a adicionar provedores de identidade.)
7. Em **Atributos de usuário**, escolha os atributos que você deseja coletar do usuário. Para obter atributos adicionais, selecione **Mostrar mais**. Por exemplo, selecione **Mostrar mais** e, depois, escolha atributos e declarações de **País/região**, **Nome de exibição** e **Código postal**. Selecione **OK**.

   ![Criar uma nova página de fluxo de usuário](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Selecione **Criar**.
9. O novo fluxo de usuário aparece na lista **Fluxos de usuário (versão prévia)** . Se necessário, atualize a página.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Selecionar o layout do formulário de coleção de atributos

Você pode escolher a ordem na qual os atributos são exibidos na página de inscrição. 

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Selecione **Identidades Externas**, em seguida, selecione **Fluxos de usuário (versão prévia)** .
3. Selecione o fluxo de usuário de inscrição por autoatendimento na lista.
4. Em **Personalizar**, selecione **Layouts da página**.
5. Os atributos que você escolheu coletar estarão listados. Para alterar a ordem de exibição, selecione um atributo e, em seguida, selecione **Mover para cima**, **Mover para baixo**, **Mover para a parte superior** ou **Mover para a parte inferior**.
6. Clique em **Salvar**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Adicionar aplicativos ao fluxo de usuário de inscrição de autoatendimento

Agora você pode associar aplicativos ao fluxo do usuário.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Em **Inscrição para autoatendimento**, selecione **Fluxos de usuário (versão prévia)** .
5. Selecione o fluxo de usuário de inscrição por autoatendimento na lista.
6. No menu à esquerda, em **Usar**, selecione **Aplicativos**.
7. Escolha **Adicionar aplicativo**.

   ![Como atribuir um aplicativo ao fluxo de usuário](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selecione o aplicativo na lista. Ou use a caixa de pesquisa para localizar o aplicativo e, em seguida, selecione-o.
9. Clique em **Selecionar**.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar o Google à sua lista de provedores de identidade social](google-federation.md)
- [Adicionar o Facebook à sua lista de provedores de identidade social](facebook-federation.md)
