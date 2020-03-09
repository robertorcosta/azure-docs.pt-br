---
title: 'Início rápido: registrar aplicativos com a plataforma Microsoft Identity | Azure'
description: Neste guia de início rápido, você aprende a adicionar e registrar um aplicativo com a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892859"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft

Neste guia de início rápido, você registra um aplicativo usando a experiência de **registros de aplicativo** no portal do Azure. Seu aplicativo está integrado à plataforma Microsoft Identity. Os desenvolvedores corporativos e os provedores de software como serviço (SaaS) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados à plataforma de identidade da Microsoft. A integração fornece entrada e autorização seguras para tais serviços.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrar um novo aplicativo usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito. Defina a sessão do portal para o locatário do Azure AD que você deseja.
1. Pesquise **Azure Active Directory** e selecione-o. Em **gerenciar**, selecione **registros de aplicativo**.
1. Selecione **Novo registro**.
1. Em **registrar um aplicativo**, insira um nome de aplicativo significativo para exibir aos usuários.
1. Especifique quem pode usar o aplicativo, da seguinte maneira:

    | Tipos de conta com suporte | Descrição |
    |-------------------------|-------------|
    | **Contas somente neste diretório organizacional** | Selecione esta opção se você está criando um aplicativo de linha de negócios (LOB). Essa opção não estará disponível se você não estiver registrando o aplicativo em um diretório.<br><br>Essa opção mapeia para o único locatário somente do Azure AD.<br><br>Essa opção é o padrão, a menos que você esteja registrando o aplicativo fora de um diretório. Quando o aplicativo é registrado fora de um diretório, o padrão é contas da Microsoft pessoais e de vários locatários do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione essa opção se você deseja direcionar para todos os clientes comerciais e educacionais.<br><br>Essa opção mapeia para vários locatários somente do Azure AD.<br><br>Se você registrou o aplicativo como somente um locatário do Azure AD, você pode atualizá-lo para ser multilocatário do Azure AD e voltar para o locatário único por meio da página de **autenticação** . |
    | **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** | Selecione essa opção a fim de direcionar para o conjunto mais amplo de clientes.<br><br>Essa opção mapeia para contas da Microsoft pessoais e multilocatário do Azure AD.<br><br>Se você registrou o aplicativo como Azure AD multilocatário e contas pessoais da Microsoft, não é possível alterar essa configuração na interface do usuário. Em vez disso, use o editor de manifesto do aplicativo para alterar os tipos de conta com suporte. |

1. Em **URI de redirecionamento (opcional)** , selecione o tipo de aplicativo que você está criando: cliente **Web** ou **público (Mobile & Desktop)** . Em seguida, insira o URI de redirecionamento ou a URL de resposta para seu aplicativo.

    * Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `https://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web.
    * Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo, `myapp://auth`.

    Para obter exemplos de aplicativos Web ou aplicativos nativos, consulte os guias de início rápido na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Ao terminar, selecione **Registrar**.

    ![Mostra a tela para registrar um novo aplicativo no portal do Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

O Azure AD atribui um aplicativo ou ID de cliente exclusivo ao seu aplicativo. O portal abre a página de **visão geral** do aplicativo. Para adicionar recursos ao seu aplicativo, você pode selecionar outras opções de configuração, incluindo identidade visual, certificados e segredos, permissões de API e muito mais.

![Exemplo de uma página de visão geral do aplicativo recentemente registrado](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Avance para o próximo artigo para saber como acessar as APIs da Web.
> [!div class="nextstepaction"]
> [Início rápido: configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)

* Para saber mais sobre as permissões, consulte [permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity](v2-permissions-and-consent.md).

* Para expor APIs Web, consulte [início rápido: configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md).

* Para gerenciar contas com suporte, consulte [início rápido: modificar as contas com suporte em um aplicativo](quickstart-modify-supported-accounts.md).

* Para criar um aplicativo e adicionar funcionalidade, consulte os guias de início rápido na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

* Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

* Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos, consulte [diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
