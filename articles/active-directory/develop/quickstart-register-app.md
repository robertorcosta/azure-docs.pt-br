---
title: 'Início Rápido: Registrar aplicativos com a plataforma de identidade da Microsoft | Azure'
description: Neste início rápido, você aprenderá a adicionar e registrar um aplicativo na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 1625b48d86eebaf5d8fcd4c100d89b83716ba459
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408361"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft

Neste início rápido, registre um aplicativo usando a experiência **Registros de aplicativo** no portal do Azure. 

Seu aplicativo é integrado à plataforma de identidade da Microsoft registrando-o com um locatário do Azure Active Directory. Os desenvolvedores corporativos e provedores de SaaS (software como serviço) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados à plataforma de identidade da Microsoft. A integração fornece entrada e autorização seguras para tais serviços.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Um [locatário do Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrar um novo aplicativo usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito. Defina sua sessão do portal para o locatário do Azure AD desejado.
1. Pesquise **Azure Active Directory** e selecione-o. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Em **Registrar um aplicativo**, insira um nome de aplicativo significativo para exibir aos usuários.
1. Especifique quem pode usar o aplicativo, da seguinte maneira:

    | Tipos de conta com suporte | Descrição |
    |-------------------------|-------------|
    | **Contas somente neste diretório organizacional** | Selecione esta opção se você está criando um aplicativo de linha de negócios (LOB). Essa opção não estará disponível se você não estiver registrando o aplicativo em um diretório.<br><br>Essa opção mapeia para o único locatário somente do Azure AD.<br><br>Essa é a opção padrão, a menos que você esteja registrando o aplicativo fora de um diretório. Quando o aplicativo é registrado fora de um diretório, o padrão é contas da Microsoft pessoais e de vários locatários do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione essa opção se você deseja direcionar para todos os clientes comerciais e educacionais.<br><br>Essa opção mapeia para vários locatários somente do Azure AD.<br><br>Se você registrou o aplicativo como único locatário somente do Azure AD, pode atualizá-lo para ser multilocatário e voltar a ser locatário único na página **Autenticação**. |
    | **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** | Selecione essa opção a fim de direcionar para o conjunto mais amplo de clientes.<br><br>Essa opção mapeia para contas da Microsoft pessoais e multilocatário do Azure AD.<br><br>Se você registrou o aplicativo como contas da Microsoft pessoais e multilocatário do Azure AD, não poderá alterar essa configuração na interface do usuário. Em vez disso, use o editor de manifesto do aplicativo para alterar os tipos de conta com suporte. |

1. Em **URI de redirecionamento (opcional)** , selecione o tipo de aplicativo que você está criando: **Web** ou **Cliente público (móvel e desktop)** . EM seguida, insira o URI de redirecionamento ou a URL de resposta para seu aplicativo.

    * Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `https://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web.
    * Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo, `myapp://auth`.

    Para obter exemplos de aplicativos Web ou aplicativos nativos, confira os guias de início rápido na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Ao terminar, selecione **Registrar**.

    ![Mostra a tela para registrar um novo aplicativo no portal do Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

O Azure AD atribui um aplicativo ou uma ID de cliente exclusiva ao seu aplicativo. O portal abre a página de **Visão geral** do seu aplicativo. Para adicionar funcionalidades ao aplicativo, você pode selecionar outras opções de configuração, incluindo identidade visual, certificados e segredos, permissões de API e muito mais.

![Exemplo da página de visão geral de um aplicativo registrado recentemente](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Próximas etapas

* Para acessar APIs Web, confira [Início rápido: Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)

* Para saber mais sobre as permissões, confira [Permissões e consentimento no ponto de extremidade da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).

* Para expor APIs Web, confira [Início rápido: Configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md).

* Para gerenciar contas com suporte, confira [Início rápido: Modificar as contas que têm suporte de um aplicativo](quickstart-modify-supported-accounts.md).

* Para criar um aplicativo e adicionar funcionalidade, confira os guias de início rápido na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

* Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

* Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos, confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
