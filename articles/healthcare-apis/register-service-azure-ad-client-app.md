---
title: Registrar um aplicativo de serviço no Azure AD – API do Azure para FHIR
description: Saiba como registrar um aplicativo cliente de serviço no Azure Active Directory que pode ser usado para autenticar e obter tokens.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871076"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente de serviço no Azure Active Directory

Neste artigo, você aprenderá a registrar um aplicativo cliente de serviço no Azure Active Directory. Os registros de aplicativo cliente são Azure Active Directory representações de aplicativos que podem ser usados para autenticar e obter tokens. Um cliente de serviço destina-se a ser usado por um aplicativo para obter um token de acesso sem autenticação interativa de um usuário. Ele terá determinadas permissões de aplicativo e usará um segredo de aplicativo (senha) ao obter tokens de acesso.

Siga as etapas abaixo para criar um novo cliente de serviço.

## <a name="app-registrations-in-azure-portal"></a>Registros de aplicativo em portal do Azure

1. No [portal do Azure](https://portal.azure.com), no painel navegação à esquerda, clique em **Azure Active Directory**.

2. Na folha **Azure Active Directory** , clique em **registros de aplicativo**:

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique em **Novo registro**.

## <a name="service-client-application-details"></a>Detalhes do aplicativo cliente do serviço

* O cliente de serviço precisa de um nome de exibição e você também pode fornecer uma URL de resposta, mas ela normalmente não será usada.

    ![portal do Azure. Novo registro de aplicativo cliente de serviço.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Permissões de API

Você precisará conceder as funções de aplicativo do cliente de serviço. 

1. Abra as **permissões de API** e selecione o [registro do aplicativo de recurso de API do FHIR](register-resource-azure-ad-client-app.md). Se estiver usando a API do Azure para FHIR, você adicionará uma permissão às APIs de assistência médica do Azure pesquisando as APIs de assistência médica do Azure em **APIs que minha organização usa**.

    ![portal do Azure. Permissões de API de cliente de serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Selecione o aplicativo que você deseja para as funções que estão definidas no aplicativo de recurso:

    ![portal do Azure. Permissões de aplicativo do cliente de serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Conceda consentimento ao aplicativo. Se você não tiver as permissões necessárias, verifique com o administrador do Azure Active Directory:

    ![portal do Azure. Consentimento do administrador do cliente de serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Segredo do aplicativo

O cliente de serviço precisa de um segredo (senha), que você usará ao obter tokens.

1. Clique em **certificados &amp; segredos**

2. Clique em **Novo segredo do cliente**

    ![portal do Azure. Segredo do cliente de serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Forneça uma duração do segredo.

4. Depois que ele tiver sido gerado, ele só será exibido uma vez no Portal. Anote-a e armazene-a com segurança.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo cliente de serviço no Azure Active Directory. Em seguida, implante uma API do FHIR no Azure.
 
>[!div class="nextstepaction"]
>[Implantar o servidor FHIR de código-fonte aberto](fhir-oss-powershell-quickstart.md)