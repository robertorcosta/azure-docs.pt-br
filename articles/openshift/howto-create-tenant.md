---
title: Crie um inquilino Azure AD para Azure Red Hat OpenShift
description: Veja como criar um inquilino do Azure Active Directory (Azure AD) para hospedar seu cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243686"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Crie um inquilino Azure AD para Azure Red Hat OpenShift

O Microsoft Azure Red Hat OpenShift requer um inquilino [do Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) para criar seu cluster. Um *inquilino* é uma instância dedicada do Azure AD que uma organização ou desenvolvedor de aplicativos recebe quando cria um relacionamento com a Microsoft, inscrevendo-se no Azure, Microsoft Intune ou Microsoft 365. Cada inquilino do Azure AD é distinto e separado de outros inquilinos do Azure AD e tem suas próprias identidades de trabalho e escola e registros de aplicativos.

Se você ainda não tem um inquilino Azure AD, siga estas instruções para criar um.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo locatário do Azure Active Directory

Para criar um inquilino:

1. Faça login no [portal Azure](https://portal.azure.com/) usando a conta que deseja associar ao seu cluster Azure Red Hat OpenShift.
2. Abra a [lâmina do Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo inquilino (também conhecido como um novo *Diretório Ativo do Azure).*
3. Forneça um **nome de organização**.
4. Forneça um **nome de domínio inicial**. Isso terá *onmicrosoft.com* anexado a ele. Você pode reutilizar o valor para *o nome da Organização* aqui.
5. Escolha um país ou região onde o inquilino será criado.
6. Clique em **Criar**.
7. Depois que o inquilino Azure AD for criado, selecione o Clique aqui para gerenciar seu novo link **de diretório.** Seu novo nome de inquilino deve ser exibido no canto superior direito do portal Azure:  

    ![Captura de tela do portal mostrando o nome do inquilino no canto superior direito][tenantcallout]  

8. Anote o *ID* do inquilino para que você possa especificar mais tarde onde criar o cluster Azure Red Hat OpenShift. No portal, você deve agora ver a lâmina de visão geral do Azure Active Directory para o seu novo inquilino. Selecione **Propriedades** e copie o valor do seu **ID de diretório**. Vamos nos referir a `TENANT` esse valor como no tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Confira a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) para obter mais informações sobre [os inquilinos do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Próximas etapas

Aprenda a criar um principal de serviço, gerar um URL de retorno de chamada de autenticação e segredo do cliente e criar um novo usuário do Active Directory para testar aplicativos no cluster Azure Red Hat OpenShift.

[Criar um usuário e objeto de aplicativo do Azure AD](howto-aad-app-configuration.md)
