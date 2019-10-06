---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978609"
---
## <a name="azure-active-directory-legacy-registration"></a>Azure Active Directory registro herdado

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **Propriedades**. Cópia da **ID de diretório** em um arquivo temporário. Você usará esse valor para configurar o aplicativo de exemplo na próxima seção.

    [ID do diretório de Active Directory de @no__t 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **registros de aplicativo (Herdado)** . Selecione o botão **novo registro de aplicativo** .

1. Dê um nome amigável para esse registro de aplicativo na caixa **Nome**. Escolha **Tipo de aplicativo** como **Nativo** e **URI de Redirecionamento** como `https://microsoft.com`. Selecione **Criar**.

    [![Criar painel](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Abra o aplicativo registrado e copie o valor do campo **ID do aplicativo** em um arquivo temporário. Esse valor identifica seu aplicativo do Azure Active Directory. Você usará a ID do aplicativo para configurar o aplicativo de exemplo nas seções a seguir.

    [![ID do aplicativo do Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Abra o painel de registro de aplicativo. Selecione **Configurações** > **Permissões necessárias** e então:

   a. Selecione **Adicionar** na parte superior esquerda para abrir o painel **Adicionar acesso à API**.

   b. Escolha **Selecionar uma API** e pesquise **Gêmeos Digitais do Azure**. Se sua pesquisa não localizar a API, pesquise por **Espaços Inteligentes do Azure** em vez disso.

   c. Selecione a opção **Gêmeos Digitais do Azure (Serviço de Espaços Inteligentes do Azure)** e escolha **Selecionar**.

   d. Escolha **Selecionar permissões**. Marque a caixa de seleção **Acesso de Leitura/Gravação** de permissões delegadas e escolha **Selecionar**.

   e. Selecione **Concluído** no painel **Adicionar acesso à API**.

   f. No painel **permissões necessárias** , selecione o botão **conceder permissões** e aceite a confirmação que aparece. Se a permissão não for concedida para essa API, contate o administrador.

      [painel de permissões ![Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
