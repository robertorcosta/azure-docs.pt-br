---
title: 'Tutorial: Criar um locatário do Azure Active Directory B2C'
description: Siga este tutorial para saber como preparar para registrar os aplicativos criando um locatário do Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dce41f979a46ae2bda568b5db79f0e0304705dd8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670170"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um locatário do Azure Active Directory B2C

Antes que os aplicativos possam interagir com o Azure Active Directory B2C (Azure AD B2C), eles precisarão ser registrados em um locatário gerenciado por você.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Alternar para o diretório que contém o locatário do Azure AD B2C
> * Adicione o recurso de Azure AD B2C como um **Favorito** no portal do Azure

Saiba como registrar um aplicativo no próximo tutorial.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

1. Entre no [portal do Azure](https://portal.azure.com/). Entre com uma conta do Azure que tenha sido atribuída pelo menos à função de [Colaborador](../role-based-access-control/built-in-roles.md) na assinatura ou em um grupo de recursos contido na assinatura.

1. Selecione o diretório que contém a assinatura.

    Na barra de ferramentas do portal do Azure, selecione o ícone **Diretório + Assinatura** e selecione o diretório que contém a assinatura. Esse diretório será diferente daquele que conterá seu locatário do Azure AD B2C.

    ![Locatário de assinatura, filtro Diretório + Assinatura com o locatário de assinatura selecionado](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. No menu do portal do Azure ou na **Página Inicial** , selecione **Criar um recurso**.
1. Pesquise e selecione **Azure Active Directory B2C** e selecione **Criar**.
1. Selecione **Criar um novo Locatário Azure AD B2C**.

    ![Criar um locatário do Azure AD B2C selecionado no portal do Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)
    
1. Insira um **Nome da organização** e o **Nome de domínio inicial**. Selecione **País ou região** (isso não poderá ser alterado posteriormente) e clique em **Criar**.

    O nome de domínio é usado como parte do nome de domínio completo do locatário. Neste exemplo, o nome do locatário é *contosob2c.onmicrosoft.com* :

1. Após a conclusão da criação do locatário, selecione o link **Criar Locatário B2C ou Vinculá-lo ao Locatário existente** na parte superior da página de criação do locatário.

    ![Link de navegação estrutural de Vincular locatário no portal do Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecione **Vincular um Locatário do Azure AD B2C existente à minha assinatura do Azure**. Você precisa estar conectado como a função de proprietário para executar esta etapa.

   ![Vincular uma seleção de assinatura existente no portal do Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecione o **Locatário do Azure AD B2C** criado e escolha sua **Assinatura**.

    Para **Grupo de recursos** , selecione **Criar novo**. Insira um **Nome** para o grupo de recursos que conterá o locatário, selecione o **Localização do grupo de recursos** e clique em **Criar**.

    ![Formulário de configurações de Vincular assinatura no portal do Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)
    

É possível vincular vários locatários Azure AD B2C a apenas uma assinatura do Azure para fins de cobrança. Para vincular um locatário, você precisa ser um administrador no locatário do Azure AD B2C e receber pelo menos uma função de Colaborador dentro da assinatura do Azure. Confira [Vincular um locatário do Azure AD B2C a uma assinatura](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Selecionar o diretório do locatário B2C

Para começar a usar seu novo locatário do Azure AD B2C, você precisa alternar para o diretório que contém o locatário.

Selecione o filtro **Diretório + assinatura** no menu superior do portal do Azure e escolha o diretório que contém o locatário do Azure AD B2C.

Se, a princípio, você não vir seu novo locatário do Azure B2C na lista, atualize a janela do navegador e selecione o filtro **Diretório + assinatura** novamente no menu superior.

![Diretório que contém o locatário do B2C selecionado no portal do Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Adicionar o Azure AD B2C como um favorito (opcional)

Essa etapa opcional facilita a seleção de seu locatário do Azure AD B2C no tutorial a seguir em todos os tutoriais subsequentes.

Em vez de pesquisar pelo *Azure AD B2C* em **Todos os serviços** cada vez que você quiser trabalhar com seu locatário, você pode, em vez disso, adicionar o recurso aos favoritos. Em seguida, você pode selecioná-lo na seção **Favoritos** do menu do portal para navegar rapidamente até o locatário do Azure AD B2C.

Você só precisa executar essa operação uma vez. Antes de executar essas etapas, verifique se você alternou para o diretório que contém o locatário do Azure AD B2C, conforme descrito na seção anterior, [Selecionar o diretório do locatário B2C](#select-your-b2c-tenant-directory).

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu do portal do Azure, selecione **Todos os serviços**.
1. Na caixa de pesquisa **Todos os serviços** , pesquise **Azure AD B2C** , focalize o resultado da pesquisa e selecione o ícone de estrela na dica de ferramenta. O **Azure AD B2C** agora aparece no portal do Azure, em **Favoritos**.
1. Se você quiser alterar a posição do novo favorito, vá para o menu do portal do Azure, selecione **Azure AD B2C** e arraste-o para cima ou para baixo até a posição desejada.

    ![Azure AD B2C, menu Favoritos, portal do Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Alternar para o diretório que contém o locatário do Azure AD B2C
> * Adicione o recurso de Azure AD B2C como um **Favorito** no portal do Azure

Em seguida, saiba como registrar um aplicativo Web em seu novo locatário.

> [!div class="nextstepaction"]
> [Registrar seus aplicativos >](tutorial-register-applications.md)
