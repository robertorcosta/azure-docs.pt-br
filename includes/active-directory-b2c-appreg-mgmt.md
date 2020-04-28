---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184328"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **registros de aplicativo (Herdado)**.
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para o aplicativo. Por exemplo, *managementapp1*.
1. Para **tipo de aplicativo**, selecione **aplicativo Web/API**.
1. Insira qualquer URL válida na **URL de logon**. Por exemplo, `https://localhost`. O ponto de extremidade não precisa estar acessível, mas deve ser uma URL válida.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** que aparece na página Visão geral do **aplicativo registrado** . Você usará esse valor em uma etapa posterior.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Selecione **contas somente neste diretório organizacional**.
1. Em **permissões**, desmarque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** que aparece na página Visão geral do aplicativo. Você usará esse valor em uma etapa posterior.