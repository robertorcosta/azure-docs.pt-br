---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184328"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. Em **Gerenciar,** selecione **registros de aplicativos (Legado)**.
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para o aplicativo. Por exemplo, *managementapp1*.
1. Para **o tipo de aplicativo,** selecione web app / **API**.
1. Digite qualquer URL válido na **URL de login**. Por exemplo, `https://localhost`. O ponto final não precisa ser acessível, mas deve ser uma URL válida.
1. Selecione **Criar**.
1. Registre o **ID do aplicativo** que aparece na página de visão geral do **aplicativo registrado.** Você usa esse valor em uma etapa posterior.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Selecione **Apenas Contas neste diretório organizacional**.
1. Em **Permissões,** libere o consentimento do anúncio da Grant para abrir e offline_access caixa de *seleção de permissões.*
1. Selecione **Registrar**.
1. Registre o **ID do aplicativo (cliente)** que aparece na página de exibição geral do aplicativo. Você usa esse valor em uma etapa posterior.