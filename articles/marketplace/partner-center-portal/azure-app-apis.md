---
title: API de integração para aplicativos do Azure no Marketplace comercial
description: Pré-requisitos de API para aplicativos do Azure no Marketplace comercial no Microsoft Partner Center.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: c14d8c6f27e4b0f4a4a75fa14b83455ff30ee35a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933664"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API para integração de aplicativos do Azure no Partner Center

Use a *API de envio do Partner Center* para consultar programaticamente, criar envios e publicar ofertas do Azure.  Essa API é útil se sua conta gerencia muitas ofertas e você deseja automatizar e otimizar o processo de envio para essas ofertas.

## <a name="api-prerequisites"></a>Pré-requisitos de API

Há alguns ativos programáticos de que você precisa para usar a API do Partner Center para produtos do Azure: 

- um aplicativo Azure Active Directory.
- um token de acesso do Azure Active Directory (AD do Azure).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Etapa 1: concluir os pré-requisitos para usar a API de envio do Partner Center

Antes de começar a escrever o código para chamar a API de envio do Partner Center, verifique se você concluiu os pré-requisitos a seguir.

- Você (ou sua organização) deve ter um diretório do Azure AD, e você deve ter permissão de [Administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para o diretório. Se você já usa o Office 365 ou outros serviços comerciais da Microsoft, você já tem o diretório Azure AD. Caso contrário, você pode [criar um novo Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sem custo adicional.

- Você deve [associar um aplicativo do Azure ad à sua conta do Partner Center](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e obter a ID do locatário, a ID do cliente e a chave. Você precisa desses valores para obter um token de acesso do Azure AD, que você usará em chamadas para a API de envio da Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Como associar um aplicativo do Azure AD à sua conta do Partner Center

Para usar a API de envio de Microsoft Store, você deve associar um aplicativo do Azure AD à sua conta do Partner Center, recuperar a ID do locatário e a ID do cliente para o aplicativo e gerar uma chave. O aplicativo Azure AD representa o aplicativo ou serviço do qual você deseja chamar a API de envio do Partner Center. Você precisa da ID do locatário, ID do cliente e da chave para obter um token de acesso do Azure AD que você passa para a API.

>[!Note]
>Você só precisa executar essa tarefa uma vez. Depois que você tiver a ID do locatário, a ID do cliente e a chave, poderá reutilizá-las sempre que precisa criar um novo token de acesso do Azure AD.

1. No Partner Center, [associe a conta do Partner Center da sua organização ao diretório do Azure ad da sua organização](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Em seguida, na página **usuários** na seção **configurações de conta** do Partner Center, [adicione o aplicativo do Azure ad](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa o aplicativo ou serviço que você usará para acessar os envios para sua conta do Partner Center. Certifique-se de atribuir esse aplicativo à **Manager**. Se o aplicativo ainda não existir no diretório do Azure AD, você poderá [criar um novo aplicativo do Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Volte para a página **Usuários**, clique no nome do seu aplicativo Azure AD para ir para as configurações do aplicativo e copie os valores da **ID do locatário** e da **ID do cliente**.
1. Clique em **Adicionar nova chave**. Na tela seguinte, copie o valor da **Chave**. Você não poderá acessar essas informações novamente depois que você sair desta página. Para obter mais informações, consulte [Gerenciar chaves para um aplicativo do Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Etapa 2: Obter um token de acesso do Azure AD

Antes de chamar qualquer um dos métodos na API de envio do Partner Center, você deve primeiro obter um token de acesso do AD do Azure que você passa para o cabeçalho de **autorização** de cada método na API. Depois de obter um token de acesso, você terá 60 minutos para usá-lo antes que ele expire. Depois que o token expirar, você poderá atualizar o token para que possa continuar a usá-lo em chamadas futuras para a API.

Para obter o token de acesso, siga as instruções em [chamadas de serviço a serviço usando credenciais de cliente](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) para enviar um `HTTP POST` para o ponto de extremidade de `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Aqui está um exemplo de solicitação:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Para o valor *tenant_id* no `POST URI` e os parâmetros *client_id* e *client_secret* , especifique a ID do locatário, a ID do cliente e a chave do aplicativo que você recuperou do Partner Center na seção anterior. Para o parâmetro *resource*, especifique `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Etapa 3: Usar a API de envio da Microsoft Store

Depois de ter um token de acesso do AD do Azure, você pode chamar métodos na API de envio do Partner Center. Para criar ou atualizar envios, você normalmente chama vários métodos na API de envio do Partner Center em uma ordem específica. Para obter informações sobre cada cenário e a sintaxe de cada método, consulte o Swagger de API de ingestão.

https://apidocs.microsoft.com/services/partneringestion/
