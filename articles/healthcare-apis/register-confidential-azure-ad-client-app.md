---
title: Registrar um aplicativo cliente confidencial no Azure AD – API do Azure para FHIR
description: Registrar um aplicativo cliente confidencial no Azure Active Directory que se autentica em nome de um usuário e solicita acesso a aplicativos de recursos.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852541"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente confidencial no Azure Active Directory

Neste tutorial, você aprenderá a registrar um aplicativo cliente confidencial no Azure Active Directory. 

Um registro de aplicativo cliente é uma representação Azure Active Directory de um aplicativo que pode ser usado para autenticar em nome de um usuário e solicitar acesso a [aplicativos de recursos](register-resource-azure-ad-client-app.md). Um aplicativo cliente confidencial é um aplicativo que pode ser confiável para manter um segredo e apresentar esse segredo ao solicitar tokens de acesso. Exemplos de aplicativos confidenciais são aplicativos do lado do servidor.

Para registrar um novo aplicativo confidencial no portal, siga as etapas abaixo.

## <a name="app-registrations-in-azure-portal"></a>Registros de aplicativo em portal do Azure

1. No [portal do Azure](https://portal.azure.com), no painel navegação à esquerda, clique em **Azure Active Directory**.

2. Na folha **Azure Active Directory** , clique em **registros de aplicativo**:

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **novo registro**.

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

1. Dê ao aplicativo um nome de exibição.

2. Forneça uma URL de resposta. Esses detalhes podem ser alterados mais tarde, mas se você souber a URL de resposta do seu aplicativo, insira-o agora.

    ![Novo registro de aplicativo cliente confidencial.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Permissões de API

Próximas permissões de API de adição:

1. Abra as **permissões de API**:

    ![Cliente confidencial. Permissões de API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Clique em **Adicionar uma permissão**

3. Selecione a API de recurso apropriada:

    Para a API do Azure para FHIR (serviço gerenciado), clique em **APIs minha organização usa** e pesquise "APIs de saúde do Azure". Para o servidor FHIR de software livre para o Azure, selecione o [registro do aplicativo de recurso de API do FHIR](register-resource-azure-ad-client-app.md):

    ![Cliente confidencial. Minhas APIs](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Selecione os escopos (permissões) que o aplicativo confidencial deve ser capaz de solicitar em nome de um usuário:

    ![Cliente confidencial. Permissões delegadas](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Segredo do aplicativo

1. Criar um segredo do aplicativo (segredo do cliente):

    ![Cliente confidencial. Segredo do aplicativo](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Forneça uma descrição e a duração do segredo.

3. Depois de gerado, ele será exibido no portal apenas uma vez. Anote-o e armazene-o com segurança.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo cliente confidencial no Azure Active Directory. Agora você está pronto para implantar a [API do Azure para FHIR](fhir-paas-powershell-quickstart.md).

Depois de implantar a API do Azure para FHIR, você poderá examinar as configurações adicionais disponíveis.
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-powershell-quickstart.md)