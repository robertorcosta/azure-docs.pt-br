---
title: API onboarding para aplicativos Azure no Mercado Comercial
description: Pré-requisitos de API para aplicativos Azure no mercado comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279752"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API para onboarding de aplicativos Azure no Partner Center

Use a *API de envio* do Partner Center para consultaprogramicamente, crie submissões e publique ofertas do Azure.  Esta API é útil se sua conta gerenciar muitas ofertas e você quiser automatizar e otimizar o processo de envio dessas ofertas.

## <a name="api-prerequisites"></a>Pré-requisitos da API

Existem alguns ativos programáticos que você precisa para usar a API do Partner Center para produtos Azure: 

- um aplicativo do Azure Active Directory.
- um token de acesso a Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passo 1: Pré-requisitos completos para o uso da API de submissão do Partner Center

Antes de começar a escrever código para chamar a API de envio do Partner Center, certifique-se de que você tenha concluído os seguintes pré-requisitos.

- Você (ou sua organização) deve ter um diretório do Azure AD, e você deve ter permissão de [Administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para o diretório. Se você já usa o Office 365 ou outros serviços comerciais da Microsoft, você já tem o diretório Azure AD. Caso contrário, você pode [criar um novo Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sem nenhum custo adicional.

- Você deve [associar um aplicativo Azure AD à sua conta do Partner Center](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e obter seu ID de inquilino, ID do cliente e chave. Você precisa desses valores para obter um token de acesso do Azure AD, que você usará em chamadas para a API de envio da Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Como associar um aplicativo Azure AD à sua conta do Partner Center

Para usar a API de envio da Microsoft Store, você deve associar um aplicativo Azure AD à sua conta do Partner Center, recuperar o ID do inquilino e o ID do cliente para o aplicativo e gerar uma chave. O aplicativo Azure AD representa o aplicativo ou serviço a partir do qual você deseja chamar a API de envio do Partner Center. Você precisa da ID do locatário, ID do cliente e da chave para obter um token de acesso do Azure AD que você passa para a API.

>[!Note]
>Você só precisa executar esta tarefa uma vez. Depois que você tiver a ID do locatário, a ID do cliente e a chave, poderá reutilizá-las sempre que precisa criar um novo token de acesso do Azure AD.

1. No Partner Center, [associe a conta do Partner Center da sua organização com o diretório Azure AD da sua organização.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Em seguida, a partir da página **Usuários** na seção **Deconfigurações** da Conta do Partner Center, [adicione o aplicativo Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa o aplicativo ou serviço que você usará para acessar submissões para sua conta do Partner Center. Certifique-se de atribuir esse aplicativo à **Manager**. Se o aplicativo ainda não existir no diretório Azure AD, você pode [criar um novo aplicativo Azure AD no Partner Center](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Volte para a página **Usuários**, clique no nome do seu aplicativo Azure AD para ir para as configurações do aplicativo e copie os valores da **ID do locatário** e da **ID do cliente**.
1. Clique em **Adicionar nova chave**. Na tela seguinte, copie o valor da **Chave**. Você não poderá acessar essas informações novamente depois que você sair desta página. Para obter mais informações, consulte [Gerenciar chaves para um aplicativo do Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Etapa 2: Obter um token de acesso do Azure AD

Antes de chamar qualquer um dos métodos na API de envio do Partner Center, você deve primeiro obter um token de acesso Azure AD que você passar para o cabeçalho de **autorização** de cada método na API. Depois de obter um token de acesso, você terá 60 minutos para usá-lo antes que ele expire. Depois que o token expirar, você pode atualizar o token para que você possa continuar a usá-lo em chamadas futuras para a API.

Para obter o token de acesso, siga as instruções em [Serviço para chamadas de serviço usando credenciais do cliente](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) para enviar um `HTTP POST` para o `https://login.microsoftonline.com/<tenant_id>/oauth2/token` ponto final. Aqui está um exemplo de solicitação:

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

Para o *valor* `POST URI` tenant_id nos parâmetros *de client_id* e *client_secret,* especifique o ID do inquilino, o ID do cliente e a chave para o seu aplicativo que você recuperou do Partner Center na seção anterior. Para o parâmetro *resource*, especifique `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Etapa 3: Usar a API de envio da Microsoft Store

Depois de ter um token de acesso a Azure AD, você pode chamar métodos na API de envio do Partner Center. Para criar ou atualizar envios, você normalmente chama vários métodos na API de envio do Partner Center em uma ordem específica. Para obter informações sobre cada cenário e a sintaxe de cada método, consulte a aPI de Ingestão.

https://apidocs.microsoft.com/services/partneringestion/
