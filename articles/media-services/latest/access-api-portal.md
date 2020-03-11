---
title: Introdução à autenticação do Azure AD utilizando o Portal do Azure
description: Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para consumir a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 03643ea3f96448e5ec9693cd8ce9589472e8e3f2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082737"
---
# <a name="use-the-azure-portal-to-get-credentials-to-access-media-services-api"></a>Usar o portal do Azure para obter credenciais para acessar a API dos serviços de mídia  

Ao usar a autenticação do Azure AD para acessar a API dos serviços de mídia do Azure, você tem duas opções de autenticação:

- **Autenticação de entidade de serviço**. Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.
- **Autenticação de usuário**. Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 

Este artigo mostra como usar o portal do Azure para obter credenciais para acessar a API dos serviços de mídia.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](create-account-portal.md).

## <a name="api-access"></a>Acesso à API 

A página **acesso à API** permite selecionar o método de autenticação que você deseja usar para se conectar à API. A página também fornece os valores necessários para se conectar à API.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione como se conectar à API dos Serviços de Mídia.
3. Em **conectar à API dos serviços de mídia**, selecione a versão da API dos serviços de mídia à qual você deseja se conectar (v3 é a versão mais recente do serviço).

## <a name="service-principal-authentication--recommended"></a>Autenticação de entidade de serviço (recomendada)

Autentica um serviço usando um aplicativo Azure Active Directory (Azure AD) e o segredo. Isso é recomendado para qualquer serviço de camada intermediária que chame a API dos serviços de mídia. Os exemplos são aplicativos Web, funções, aplicativos lógicos, APIs e microservices. Esse é o método de autenticação recomendado.

### <a name="manage-your-azure-ad-app-and-secret"></a>Gerenciar seu aplicativo e segredo do Azure AD

A seção **gerenciar seu aplicativo e segredo do AAD** permite que você selecione ou crie um novo aplicativo do Azure AD e gere um segredo. Para fins de segurança, o segredo não pode ser mostrado depois que a folha é fechada. O aplicativo usa a ID do aplicativo e o segredo para autenticação para obter um token válido para os serviços de mídia.

Verifique se você tem permissões suficientes para registrar um aplicativo com seu locatário do Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure. Para obter mais informações, consulte [Required Permissions](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Conectar-se à API dos serviços de mídia

A **API conectar a serviços de mídia** fornece valores que você usa para conectar seu aplicativo de entidade de serviço. Você pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="user-authentication"></a>Autenticação de usuário

Essa opção pode ser usada para autenticar um funcionário ou membro de um Azure Active Directory que está usando um aplicativo para interagir com os recursos dos serviços de mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Esse método de autenticação deve ser usado somente para aplicativos de gerenciamento.

### <a name="connect-to-media-services-api"></a>Conectar-se à API dos serviços de mídia

Copie suas credenciais para conectar seu aplicativo de usuário da seção **conectar-se à API dos serviços de mídia** . Você pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[Tutorial: carregar, codificar e transmitir vídeos com os serviços de mídia v3](stream-files-tutorial-with-api.md).
