---
title: Como usar a Remover um aplicativo registrado da plataforma de identidade da Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Nestas Instruções, você aprenderá a remover um aplicativo registrado na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: edfed8ca361d456e622096e23079d5887f96ab30
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452986"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Como remover um aplicativo registrado na plataforma de identidade da Microsoft

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) que registraram aplicativos na Microsoft Identity Platform podem precisar remover o registro do aplicativo.

Nas seções seguintes, você aprenderá a:

* Remover um aplicativo criado por você ou por sua organização
* Remover um aplicativo criado por outra organização

## <a name="prerequisites"></a>Pré-requisitos

* Um [aplicativo registrado no locatário do Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover um aplicativo criado por você ou por sua organização

Os aplicativos que você ou sua organização registraram são representados por um objeto de aplicativo e pelo objeto de entidade de serviço em seu locatário. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](./app-objects-and-service-principals.md).

Para excluir um aplicativo, você precisa estar listado como proprietário do aplicativo ou ter privilégios de administrador.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e selecione **Registros de aplicativo**. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** do aplicativo.
1. Na página **Visão Geral**, selecione **Excluir**.
1. Selecione **Sim** para confirmar que você deseja excluir o aplicativo.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover um aplicativo criado por outra organização

Se você estiver exibindo **Registros de aplicativo** no contexto de um locatário, um subconjunto dos aplicativos que são exibidos na guia **Todos os aplicativos** são de outro locatário e foram registrados no seu locatário durante o processo de consentimento. Mais especificamente, eles são representados apenas por um objeto de entidade de serviço em seu locatário, sem objeto de aplicativo correspondente. Para saber mais sobre as diferenças entre os objetos de aplicativo e entidade de serviço, confira [Objetos de aplicativo e entidade de serviço no Azure AD](./app-objects-and-service-principals.md).

Para remover o acesso de um aplicativo ao seu diretório (depois de ter dado autorização), o administrador da empresa deverá remover sua entidade de serviço. O administrador deve ter acesso de administrador global e pode remover o aplicativo pelo Azure ou usar os [Cmdlets do PowerShell do Azure AD](/previous-versions/azure/jj151815(v=azure.100)) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [objetos de entidade de serviço e aplicativo](app-objects-and-service-principals.md) na plataforma de identidade da Microsoft.
