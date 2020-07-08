---
title: Registrar um aplicativo de recurso no Azure AD – API do Azure para FHIR
description: Registrar um aplicativo de recurso (ou API) no Azure Active Directory, para que os aplicativos cliente possam solicitar acesso ao recurso ao autenticar.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 10b951300b8386b057744a980abd5d847b6b6907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871086"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrar um aplicativo de recurso no Azure Active Directory

Neste artigo, você aprenderá a registrar um aplicativo de recurso (ou API) no Azure Active Directory. Um aplicativo de recurso é uma representação Azure Active Directory da API do servidor FHIR e os aplicativos cliente podem solicitar acesso ao recurso durante a autenticação. O aplicativo de recurso também é conhecido como o *público* em linguagem OAuth.

## <a name="azure-api-for-fhir"></a>API do Azure para FHIR

Se você estiver usando a API do Azure para FHIR, um aplicativo de recurso será criado automaticamente quando você implantar o serviço. Contanto que você esteja usando a API do Azure para FHIR no mesmo locatário Azure Active Directory em que está implantando seu aplicativo, você pode ignorar este guia e, em vez disso, implantar sua API do Azure para FHIR para começar.

Se você estiver usando um locatário de Azure Active Directory diferente (não associado à sua assinatura), poderá importar a API do Azure para o aplicativo de recurso FHIR em seu locatário com o PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

ou você pode usar CLI do Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Servidor FHIR para o Azure

Se você estiver usando o servidor de FHIR de software livre para o Azure, siga as etapas abaixo para registrar um aplicativo de recurso.

### <a name="app-registrations-in-azure-portal"></a>Registros de aplicativo em portal do Azure

1. No [portal do Azure](https://portal.azure.com), no painel navegação à esquerda, clique em **Azure Active Directory**.

2. Na folha **Azure Active Directory** , clique em **registros de aplicativo**:

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **novo registro**.

### <a name="add-a-new-application-registration"></a>Adicionar um novo registro de aplicativo

Preencha os detalhes do novo aplicativo. Não há requisitos específicos para o nome de exibição, mas configurá-lo para o URI do servidor FHIR facilita a localização:

![Novo registro de aplicativo](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Definir o URI do identificador e definir escopos

Um aplicativo de recurso tem um URI de identificador (URI de ID de aplicativo), que os clientes podem usar ao solicitar acesso ao recurso. Esse valor preencherá a `aud` declaração do token de acesso. É recomendável que você defina esse URI para ser o URI do seu servidor FHIR. Para aplicativos SMART on FHIR, supõe-se que o *público-alvo* é o URI do servidor FHIR.

1. Clique em **expor uma API**

2. Clique em **definir** ao lado de *ID do aplicativo URI*.

3. Insira o URI do identificador e clique em **salvar**. Um bom URI de identificador seria o URI do seu servidor FHIR.

4. Clique em **Adicionar um escopo** e adicione todos os escopos que você deseja definir para sua API. Você precisa adicionar pelo menos um escopo para conceder permissões ao seu aplicativo de recurso no futuro. Se você não tiver escopos específicos que deseja adicionar, poderá adicionar user_impersonation como um escopo.

![Público e escopo](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definir funções de aplicativo

A API do Azure para FHIR e o servidor FHIR do OSS para Azure usam [Azure Active Directory funções de aplicativo](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) para o controle de acesso baseado em função. Para definir quais funções devem estar disponíveis para a API do servidor do FHIR, abra o [manifesto](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)do aplicativo de recurso:

1. Clique em **manifesto**:

    ![Funções de aplicativo](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Na `appRoles` propriedade, adicione as funções que você deseja que os usuários ou aplicativos tenham:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo de recurso no Azure Active Directory. Em seguida, implante a API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-powershell-quickstart.md)