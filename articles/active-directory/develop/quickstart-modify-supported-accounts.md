---
title: 'Início Rápido: Alterar os tipos de conta com suporte por um aplicativo | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, você configura um aplicativo registrado na plataforma de identidade da Microsoft para alterar quem (ou quais contas) pode acessar o aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/27/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 2382eedcc14f683d354b88bf2eb8d53b2af40dbd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083262"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Início Rápido: Modificar as contas que têm suporte de um aplicativo

Quando registrou seu aplicativo na plataforma de identidade da Microsoft, você especificou quem – quais tipos de conta – pode acessá-lo. Por exemplo, você pode ter especificado contas apenas em sua organização, que é um aplicativo de *locatário único*. Ou, então, você pode ter especificado contas em qualquer organização (incluindo a sua), que é um aplicativo *multilocatário*.

Neste início rápido, você aprenderá a modificar a configuração do aplicativo para alterar quem, ou quais tipos de contas, pode acessá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alterar o registro do aplicativo para dar suporte a contas diferentes

Para especificar uma configuração diferente para os tipos de conta com suporte de um registro de aplicativo existente:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar** , selecione **Registros de aplicativo** e, em seguida, selecione seu aplicativo.
1. Agora, especifique quem pode usar o aplicativo, às vezes chamado de *público-alvo de conexão*.

    | Tipos de conta com suporte | Descrição |
    |-------------------------|-------------|
    | **Contas somente neste diretório organizacional** | Selecione essa opção se você está criando um aplicativo para uso somente por usuários (ou convidados) no *seu* locatário.<br><br>Geralmente chamado de aplicativo LOB ( *linha de negócios* ), é um aplicativo de **locatário único** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional** | Selecione essa opção se você quer que os usuários em *qualquer* locatário do Azure AD possam usar seu aplicativo. Essa opção é apropriada se, por exemplo, você está criando um aplicativo SaaS (software como serviço) que quer fornecer a várias organizações.<br><br>Isso é conhecido como um aplicativo **multilocatário** na plataforma de identidade da Microsoft. |
1. Selecione **Salvar**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Por que fazer a alteração para multilocatário pode falhar

A alternância de um registro de aplicativo de locatário único para multilocatário às vezes pode falhar devido a conflitos de nome do URI de ID do Aplicativo. Um URI de ID do Aplicativo de exemplo é `https://contoso.onmicrosoft.com/myapp`.

O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, o URI de ID do Aplicativo precisa ser exclusivo dentro desse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo que o nome do host do URI de ID do Aplicativo corresponda a um dos [domínios de editor verificado](howto-configure-publisher-domain.md) do locatário do Azure AD.

Por exemplo, se o nome do locatário for *contoso.onmicrosoft.com* , então um `https://contoso.onmicrosoft.com/myapp` será um URI de ID do Aplicativo válido. Se o locatário tivesse um domínio verificado de *contoso.com* , então, um URI da ID do aplicativo válido também seria `https://contoso.com/myapp`. Se o URI de ID do Aplicativo não seguir o segundo padrão, `https://contoso.com/myapp`, a conversão do registro do aplicativo multilocatário falhará.

Para obter mais informações sobre como configurar um domínio de editor verificado, confira [Configurar um domínio verificado](quickstart-modify-supported-accounts.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Converter seu aplicativo em multilocatário](howto-convert-app-to-be-multi-tenant.md)
