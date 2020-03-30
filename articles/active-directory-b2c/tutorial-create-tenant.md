---
title: Tutorial - Crie um inquilino B2C do Diretório Ativo do Azure
description: Saiba como preparar para registrar os aplicativos criando um locatário do Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186396"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial - Criar um locatário do Azure Active Directory B2C

Antes que seus aplicativos possam interagir com o Azure Active Directory B2C (Azure AD B2C), eles devem ser registrados em um inquilino que você gerencia.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Mude para o diretório que contém seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Saiba como registrar um aplicativo no próximo tutorial.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

1. Faça login no [portal Azure](https://portal.azure.com/). Faça login com uma conta do Azure que tenha sido atribuída pelo menos a função [Contribuinte](../role-based-access-control/built-in-roles.md) dentro da assinatura ou um grupo de recursos dentro da assinatura.

1. Selecione o diretório que contém sua assinatura.

    Na barra de ferramentas do portal Azure, selecione o ícone **Diretório + Assinatura** e selecione o diretório que contém sua assinatura. Este diretório é diferente daquele que conterá o seu inquilino Azure AD B2C.

    ![Inquilino de assinatura, filtro diretório + assinatura com inquilino de assinatura selecionado](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Procure **o Azure Active Directory B2C**e selecione **Criar**.
1. Selecione **Criar um novo Locatário Azure AD B2C**.

    ![Crie um novo inquilino Azure AD B2C selecionado no portal Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Digite um **nome de organização** e nome de domínio **inicial**. Selecione o **País ou região** (não pode ser alterado mais tarde) e selecione **Criar**.

    O nome de domínio é usado como parte do nome de domínio do inquilino completo. Neste exemplo, o nome do inquilino é *contosob2c.onmicrosoft.com:*

    ![Criar formulário de inquilino com valores de exemplo no portal Azure](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Uma vez concluída a criação do inquilino, selecione o **Criar novo Inquilino B2C ou Link para** o link do Inquilino existente na parte superior da página de criação do inquilino.

    ![Link link de migalhas de pão de inquilino destacado no portal Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecione **Vincular um inquilino AD B2C existente à minha assinatura do Azure**.

   ![Vincular uma seleção de assinatura existente no portal Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecione o **inquilino AD B2C do Azure** que você criou e selecione sua **Assinatura**.

    Para **Grupo de recursos**, selecione **Criar novo**. Digite um **Nome** para o grupo de recursos que conterá o inquilino, selecione o local do **grupo Recurso**e, em seguida, selecione **Criar**.

    ![Formulário de configurações de assinatura de link no portal Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Você pode vincular vários inquilinos Azure AD B2C a uma única assinatura do Azure para fins de faturamento.

## <a name="select-your-b2c-tenant-directory"></a>Selecione seu diretório de inquilinos B2C

Para começar a usar seu novo inquilino Azure AD B2C, você precisa mudar para o diretório que contém o inquilino.

Selecione o filtro **de assinatura Diretório +** no menu superior do portal Azure e selecione o diretório que contém o inquilino Azure AD B2C.

Se no início você não ver seu novo inquilino Azure B2C na lista, atualize a janela do navegador e selecione novamente o filtro **de assinatura Do Diretório +** no menu superior.

![Diretório com contendo inquilinoS B2C selecionado no portal Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Adicionar Azure AD B2C como favorito (opcional)

Esta etapa opcional torna mais fácil selecionar o seu inquilino Azure AD B2C nos seguintes e todos os tutoriais subsequentes.

Em vez de procurar *o Azure AD B2C* em **todos os serviços** toda vez que você quiser trabalhar com seu inquilino, você pode, em vez disso, preferir o recurso. Em seguida, você pode selecioná-lo na seção **Favoritos** do menu do portal para navegar rapidamente para o seu inquilino Azure AD B2C.

Você só precisa realizar esta operação uma vez. Antes de executar essas etapas, certifique-se de que você mudou para o diretório contendo o inquilino Azure AD B2C conforme descrito na seção anterior, [Selecione seu diretório de inquilino B2C](#select-your-b2c-tenant-directory).

1. Faça login no [portal Azure](https://portal.azure.com).
1. No menu portal Azure, selecione **Todos os serviços**.
1. Na caixa de pesquisa **All services,** procure **por Azure AD B2C**, paire sobre o resultado da pesquisa e selecione o ícone de estrela na dica de ferramenta. **Azure AD B2C** agora aparece no portal Azure em **Favorites**.
1. Se você quiser alterar a posição do seu novo favorito, vá para o menu do portal Azure, selecione **Azure AD B2C**e arraste-o para cima ou para baixo para a posição desejada.

    ![Azure AD B2C, menu Favoritos, portal Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Mude para o diretório que contém seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Em seguida, saiba como registrar um aplicativo web em seu novo inquilino.

> [!div class="nextstepaction"]
> [Registre suas inscrições >](tutorial-register-applications.md)
