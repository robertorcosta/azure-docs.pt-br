---
title: Introdução à autenticação do Azure AD utilizando o Portal do Azure | Microsoft Docs
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
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330661"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD utilizando o Portal do Azure

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, veja [as orientações de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para acessar a API dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).

Quando você usar a autenticação do AD Azure com Serviços de Mídia do Azure haverá duas opções de autenticação:

- **Autenticação principal do serviço**. Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.
- **Autenticação do usuário**. Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 

## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Esta página permite selecionar o método de autenticação que deseja usar para se conectar à API. A página também fornece os valores necessários para se conectar à API.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione como se conectar à API dos Serviços de Mídia.
3. Em **Connect to Media Services API**, selecione a versão de API de serviços de mídia à quais deseja se conectar.

## <a name="service-principal-authentication--recommended"></a>Autenticação principal do serviço (recomendado)

Autentica um serviço usando um aplicativo azure Active Directory (Azure AD) e secreto. Isso é recomendado para qualquer serviço de nível intermediário que ligar para a API de Serviços de Mídia. Exemplos são Aplicativos web, funções, aplicativos lógicos, APIs e microsserviços. Este é o método de autenticação recomendado.

### <a name="manage-your-azure-ad-app-and-secret"></a>Gerencie seu aplicativo AD do Azure e seu segredo

O **aplicativo Gerenciar seu aplicativo AAD e** seção secreta permite que você selecione ou crie um novo aplicativo Ad do Azure e gere um segredo. Para fins de segurança, o segredo não pode ser mostrado depois que a lâmina é fechada. O aplicativo usa o ID do aplicativo e o segredo para autenticação para obter um token válido para serviços de mídia.

Certifique-se de que você tem permissões suficientes para registrar um aplicativo com seu inquilino Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure. Para obter mais informações, consulte [as permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

A **API Conectar aos Serviços de Mídia** fornece valores que você usa para conectar seu aplicativo principal de serviço. Você pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="user-authentication"></a>Autenticação de usuário

Essa opção pode ser usada para autenticar um funcionário ou membro de um Diretório Ativo do Azure que está usando um aplicativo para interagir com os recursos do Media Services. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Este método de autenticação só deve ser usado para aplicativos de gerenciamento.

### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

Copie suas credenciais para conectar seu aplicativo de usuário da seção **API Conectar aos Serviços de Mídia.** Você pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).
