---
title: Login vinculado para aplicativos AD do Azure - plataforma de identidade da Microsoft
description: Configure o SSO (Single Signon, soo) vinculado aos aplicativos corporativos do Azure AD na plataforma de identidade Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063536"
---
# <a name="configure-linked-sign-on"></a>Configurar o logon vinculado

Quando você adiciona um aplicativo web de galeria ou não-galeria, uma das opções de login único disponíveis para você é [o login vinculado](what-is-single-sign-on.md). Selecione esta opção para adicionar um link ao aplicativo no Painel de Acesso AD AD da sua organização ou no portal Office 365. Você pode usar este método para adicionar links a aplicativos web personalizados que atualmente usam serviços de federação de diretórios ativos (ou outro serviço da federação) em vez do Azure AD para autenticação. Ou você pode adicionar links profundos para páginas específicas do SharePoint ou outras páginas da Web que você queira que apareçam somente nos Painéis de Acesso do usuário.

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, confira [Adicionar um aplicativo da galeria](add-gallery-app.md) ou [Adicionar um aplicativo inexistente na galeria](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Abra o aplicativo e selecione o login vinculado

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

1. Navegue até os aplicativos **do Azure Active Directory** > **Enterprise**. Uma amostra aleatória dos aplicativos em seu locatário do Azure AD é exibida. 

1. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**.

1. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

1. Na seção **Gerenciar**, selecione **Logon único**. 

1. Selecione **Vinculado**.

1. Digite a URL do aplicativo para vincular. Digite a URL e selecione **Salvar**. 
 
1. Você pode atribuir usuários e grupos ao aplicativo, o que faz com que o aplicativo apareça no [launcher do aplicativo Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no painel de acesso [Azure AD](end-user-experiences.md) para esses usuários.

1. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
