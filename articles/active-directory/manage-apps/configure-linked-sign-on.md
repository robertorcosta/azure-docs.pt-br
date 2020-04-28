---
title: Logon vinculado para aplicativos do Azure AD-plataforma Microsoft Identity
description: Configurar o logon único vinculado (SSO) para seus aplicativos empresariais do Azure AD na plataforma Microsoft Identity (Azure AD)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063536"
---
# <a name="configure-linked-sign-on"></a>Configurar o logon vinculado

Quando você adiciona um aplicativo Web galeria ou não Galeria, uma das opções de logon único disponíveis para você é o [logon vinculado](what-is-single-sign-on.md). Selecione esta opção para adicionar um link para o aplicativo no painel de acesso do Azure AD ou no portal do Office 365 da sua organização. Você pode usar esse método para adicionar links a aplicativos Web personalizados que atualmente usam Serviços de Federação do Active Directory (AD FS) (ou outro serviço de Federação) em vez do Azure AD para autenticação. Ou você pode adicionar links profundos para páginas específicas do SharePoint ou outras páginas da Web que você queira que apareçam somente nos Painéis de Acesso do usuário.

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, confira [Adicionar um aplicativo da galeria](add-gallery-app.md) ou [Adicionar um aplicativo inexistente na galeria](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Abra o aplicativo e selecione logon vinculado

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

1. Navegue até **Azure Active Directory** > **aplicativos empresariais**. Uma amostra aleatória dos aplicativos em seu locatário do Azure AD é exibida. 

1. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**.

1. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

1. Na seção **Gerenciar**, selecione **Logon único**. 

1. Selecione **vinculado**.

1. Insira a URL do aplicativo a ser vinculado. Digite a URL e selecione **salvar**. 
 
1. Você pode atribuir usuários e grupos ao aplicativo, o que faz com que o aplicativo seja exibido no [iniciador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso do Azure ad](end-user-experiences.md) para esses usuários.

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
