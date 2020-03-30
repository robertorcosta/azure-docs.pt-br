---
title: Comece com a autenticação Azure AD
description: Saiba como acessar a autenticação do Azure Active Directory (Azure AD) para consumir a API azure Media Services.
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
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478247"
---
# <a name="get-credentials-to-access-media-services-api"></a>Obtenha credenciais para acessar a API de Serviços de Mídia  

Quando você usa a autenticação Azure AD para acessar a API do Azure Media Services, você tem duas opções de autenticação:

- **Autenticação principal do** serviço (recomendado)

    Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.
- **Autenticação do usuário**

    Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 

Este artigo descreve as etapas para obter credenciais para acessar a API de Serviços de Mídia. Escolha entre as guias a seguir.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

### <a name="api-access"></a>Acesso à API 

A página **de acesso à API** permite selecionar o método de autenticação que deseja usar para se conectar à API. A página também fornece os valores necessários para se conectar à API.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione como se conectar à API dos Serviços de Mídia.
3. Em **Connect to Media Services API**, selecione a versão de API de serviços de mídia que deseja conectar (v3 é a versão mais recente do serviço).

### <a name="service-principal-authentication--recommended"></a>Autenticação principal do serviço (recomendado)

Autentica um serviço usando um aplicativo azure Active Directory (Azure AD) e secreto. Isso é recomendado para qualquer serviço de nível intermediário que ligar para a API de Serviços de Mídia. Exemplos são Aplicativos web, funções, aplicativos lógicos, APIs e microsserviços. Este é o método de autenticação recomendado.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gerencie seu aplicativo AD do Azure e seu segredo

O **aplicativo Gerenciar seu aplicativo AAD e** seção secreta permite que você selecione ou crie um novo aplicativo Ad do Azure e gere um segredo. Para fins de segurança, o segredo não pode ser mostrado depois que a lâmina é fechada. O aplicativo usa o ID do aplicativo e o segredo para autenticação para obter um token válido para serviços de mídia.

Certifique-se de que você tem permissões suficientes para registrar um aplicativo com seu inquilino Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure. Para obter mais informações, consulte [as permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

A **API Conectar aos Serviços de Mídia** fornece valores que você usa para conectar seu aplicativo principal de serviço. Você pode obter valores de texto ou copiar os blocos JSON ou XML.

### <a name="user-authentication"></a>Autenticação de usuário

Essa opção pode ser usada para autenticar um funcionário ou membro de um Diretório Ativo do Azure que está usando um aplicativo para interagir com os recursos do Media Services. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Este método de autenticação só deve ser usado para aplicativos de gerenciamento.

#### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

Copie suas credenciais para conectar seu aplicativo de usuário da seção **API Conectar aos Serviços de Mídia.** Você pode obter valores de texto ou copiar os blocos JSON ou XML.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Carregar, codificar e transmitir vídeos com media services v3](stream-files-tutorial-with-api.md).
