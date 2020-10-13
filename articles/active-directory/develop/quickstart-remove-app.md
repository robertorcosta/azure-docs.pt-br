---
title: 'Início Rápido: Remover um aplicativo registrado da plataforma de identidade da Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá a remover um aplicativo registrado na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612380"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Início Rápido: Remover um aplicativo registrado na plataforma de identidade da Microsoft

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) que registraram aplicativos na Microsoft Identity Platform podem precisar remover o registro do aplicativo.

Neste início rápido, você aprenderá a:

* Remover um aplicativo criado por você ou por sua organização
* Remover um aplicativo criado por outra organização

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)

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

Para remover o acesso de um aplicativo ao seu diretório (depois de ter dado autorização), o administrador da empresa deverá remover sua entidade de serviço. O administrador deve ter acesso de administrador global e pode remover o aplicativo pelo Azure ou usar os [Cmdlets do PowerShell do Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os objetos de entidade de serviço e aplicativo na plataforma de identidade da Microsoft:

> [!div class="nextstepaction"]
> [Objetos de entidade de serviço e aplicativo no Azure Active Directory](app-objects-and-service-principals.md)