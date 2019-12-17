---
title: Remover um aplicativo registrado com a plataforma de identidade da Microsoft | Azure
description: Saiba como remover um aplicativo registrado na Microsoft Identity Platform.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fff6d07bd0e353541dec23fd8c3edf7407ef6d68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963283"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Início Rápido: Remover um aplicativo registrado na plataforma de identidade da Microsoft

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) que registraram aplicativos na Microsoft Identity Platform podem precisar remover o registro do aplicativo.

Neste início rápido, você aprenderá a:

* Remover um aplicativo criado por você ou por sua organização
* Remover um aplicativo criado por outra organização

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter um locatário com aplicativos registrados. Para aprender a adicionar aplicativos registrados, confira [Registrar um aplicativo na Microsoft Identity Platform](quickstart-register-app.md).

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover um aplicativo criado por você ou por sua organização

Os aplicativos que você ou sua organização registraram são representados por um objeto de aplicativo e pelo objeto de entidade de serviço em seu locatário. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Para remover um aplicativo

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
2. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
3. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e selecione **Registros de aplicativo**. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** do aplicativo.
4. Na página **Visão Geral**, selecione **Excluir**.
5. Selecione **Sim** para confirmar que você deseja excluir o aplicativo.

   > [!NOTE]
   > Para excluir um aplicativo, você precisa estar listado como proprietário do aplicativo ou ter privilégios de administrador.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover um aplicativo criado por outra organização

Se você estiver exibindo **Registros de aplicativo** no contexto de um locatário, um subconjunto dos aplicativos que são exibidos na guia **Todos os aplicativos** são de outro locatário e foram registrados no seu locatário durante o processo de consentimento. Mais especificamente, eles são representados apenas por um objeto de entidade de serviço em seu locatário, sem objeto de aplicativo correspondente. Para saber mais sobre as diferenças entre os objetos de aplicativo e entidade de serviço, confira [Objetos de aplicativo e entidade de serviço no Azure AD](active-directory-application-objects.md).

Para remover o acesso de um aplicativo ao seu diretório (depois de ter dado autorização), o administrador da empresa deverá remover sua entidade de serviço. O administrador deve ter acesso de administrador global e pode remover o aplicativo pelo Azure ou usar os [Cmdlets do PowerShell do Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre esses outros inícios rápidos de gerenciamento de aplicativos relacionados:

* [Registrar um aplicativo com a Microsoft Identity Platform](quickstart-register-app.md)
* [Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
* [Configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Modificar as contas que têm suporte por um aplicativo](quickstart-modify-supported-accounts.md)
