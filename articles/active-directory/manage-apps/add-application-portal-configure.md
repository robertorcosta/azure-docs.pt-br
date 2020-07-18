---
title: 'Início Rápido: Configurar propriedades para um aplicativo em seu locatário do Azure AD (Azure Active Directory)'
description: Este guia de início rápido usa o portal do Azure para configurar um aplicativo que foi registrado com seu locatário do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8418a45c9eba596339999c2bddf937a516d3e2d9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223339"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Configurar propriedades para um aplicativo em seu locatário do Azure AD (Azure Active Directory)

No guia de início rápido anterior, você adicionou um aplicativo ao seu locatário do Azure AD (Azure Active Directory). Quando você adiciona um aplicativo, está permitindo que seu locatário do Azure AD saiba que ele é o provedor de identidade do aplicativo. Agora você configurará algumas das propriedades para o aplicativo.
 
## <a name="prerequisites"></a>Pré-requisitos

Para configurar as propriedades de um aplicativo em seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).

>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste início rápido.

## <a name="configure-app-properties"></a>Configurar propriedades do aplicativo

Depois de concluir a adição de um aplicativo ao locatário do Azure AD, a página de visão geral é exibida. Se você estiver configurando um aplicativo que já tenha sido adicionado, confira o primeiro início rápido. Ele orienta você na exibição dos aplicativos adicionados ao seu locatário. 

Para editar as propriedades do aplicativo:

1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo que você deseja configurar.
1. Na seção **Gerenciar**, selecione **Propriedades** para abrir o painel **Propriedades** para edição.

    ![Captura de tela da tela Propriedades que mostra as propriedades editáveis do aplicativo.](media/add-application-portal/edit-properties.png)

1. Reserve um tempo para entender as opções disponíveis para configurar:
    - **Habilitado para que os usuários entrem?** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - **Atribuição do usuário obrigatória?** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para os usuários?** determina se os usuários atribuídos a um aplicativo podem vê-lo no [Painel de Acesso](https://myapps.microsoft.com) e no inicializador de aplicativos do Office 365. (Confira o menu do Waffle no canto superior esquerdo de um site do Office 365 ou Microsoft 365.)
1. Use as tabelas a seguir para ajudar você a escolher as melhores opções para suas necessidades.

   - Comportamento de usuários *atribuídos*:

       | Propriedade do aplicativo | Propriedade do aplicativo | Propriedade do aplicativo | Experiência do usuário atribuído | Experiência do usuário atribuído |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários atribuídos podem entrar? | Os usuários atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | Sim | Sim  |
       | Sim | Sim | Não  | Sim | Não   |
       | Sim | Não  | Sim | Sim | Sim  |
       | Sim | Não  | Não  | Sim | Não   |
       | Não  | Sim | Sim | Não  | Não   |
       | Não  | Sim | Não  | Não  | Não   |
       | Não  | Não  | Sim | Não  | Não   |
       | Não  | Não  | Não  | Não  | Não   |

   - Comportamento de usuários *não atribuídos*:

       | Propriedade do aplicativo | Propriedade do aplicativo | Propriedade do aplicativo | Experiência de usuário não atribuído | Experiência de usuário não atribuído |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários não atribuídos podem entrar? | Os usuários não atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | Não  | Não   |
       | Sim | Sim | Não  | Não  | Não   |
       | Sim | Não  | Sim | Sim | Não   |
       | Sim | Não  | Não  | Sim | Não   |
       | Não  | Sim | Sim | Não  | Não   |
       | Não  | Sim | Não  | Não  | Não   |
       | Não  | Não  | Sim | Não  | Não   |
       | Não  | Não  | Não  | Não  | Não   |

     *O usuário pode ver o aplicativo no painel de acesso e no iniciador de aplicativos do Office 365?

## <a name="use-a-custom-logo"></a>Usar um logotipo personalizado

Para usar um logotipo personalizado:

1. Crie um logotipo de 215 x 215 pixels e salve-o no formato PNG.
1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo que você deseja configurar.
1. Na seção **Gerenciar**, selecione **Propriedades** para abrir o painel **Propriedades** para edição. 
1. Selecione o ícone para carregar o logotipo.
1. Quando terminar, selecione **Salvar**.

    ![Captura de tela da tela Propriedades mostrando como alterar o logotipo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A miniatura exibida neste painel de **Propriedades** não é atualizada imediatamente. Você pode fechar e reabrir o painel **Propriedades** para ver o ícone atualizado.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou as propriedades de um aplicativo, pode continuar a configurar o logon único.

- [Configurar logon único](add-application-portal-setup-sso.md)
- [Excluir um aplicativo](delete-application-portal.md)