---
title: Registrar um aplicativo de serviço no Azure AD – API do Azure para FHIR
description: Saiba como registrar um aplicativo cliente de serviço no Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 19d6b0ebfa2570b04c3a9dda3fe69428aa0eed75
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629260"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente de serviço no Azure Active Directory

Neste artigo, você aprenderá a registrar um aplicativo cliente de serviço no Azure Active Directory. Os registros de aplicativo cliente são Azure Active Directory representações de aplicativos que podem ser usados para autenticar e obter tokens. Um cliente de serviço destina-se a ser usado por um aplicativo para obter um token de acesso sem autenticação interativa de um usuário. Ele terá determinadas permissões de aplicativo e usará um segredo de aplicativo (senha) ao obter tokens de acesso.

Siga estas etapas para criar um novo cliente de serviço.

## <a name="app-registrations-in-azure-portal"></a>Registros de aplicativo em portal do Azure

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory**.

2. Selecione **Registros do Aplicativo**.

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Selecione **Novo registro**.

4. Dê ao cliente de serviço um nome de exibição. Aplicativos cliente de serviço normalmente não usam uma URL de resposta.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="portal do Azure. Novo registro de aplicativo cliente de serviço.":::

5. Selecione **Registrar**.

## <a name="api-permissions"></a>Permissões de API

Agora que você registrou seu aplicativo, precisará selecionar quais permissões de API esse aplicativo deve ser capaz de solicitar em nome dos usuários:

1. Selecione **Permissões de API**.
1. Selecione **Adicionar uma permissão**.

    Se estiver usando a API do Azure para FHIR, você adicionará uma permissão às APIs de assistência médica do Azure pesquisando as **APIs de assistência médica do Azure** em **APIs que minha organização usa**. 

    Se você estiver fazendo referência a um aplicativo de recurso diferente, selecione o [registro de aplicativo do recurso de API do FHIR](register-resource-azure-ad-client-app.md) que você criou anteriormente em **minhas APIs**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="portal do Azure. Novo registro de aplicativo cliente de serviço." lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Selecione os escopos (permissões) que o aplicativo confidencial deve ser capaz de solicitar em nome de um usuário:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="portal do Azure. Novo registro de aplicativo cliente de serviço.":::

1. Conceda consentimento ao aplicativo. Se você não tiver as permissões necessárias, verifique com o administrador do Azure Active Directory:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="portal do Azure. Novo registro de aplicativo cliente de serviço.":::

## <a name="application-secret"></a>Segredo do aplicativo

O cliente de serviço precisa de um segredo (senha) para obter um token.

1. Selecione **Certificados e segredos**.
2. Selecione **Novo segredo do cliente**.

    ![portal do Azure. Segredo do cliente de serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Forneça uma descrição e a duração do segredo (1 ano, 2 anos ou nunca).

4. Depois que o segredo tiver sido gerado, ele só será exibido uma vez no Portal. Anote-a e armazene-a com segurança.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo cliente de serviço no Azure Active Directory. Em seguida, você pode aprender sobre configurações adicionais para a API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)