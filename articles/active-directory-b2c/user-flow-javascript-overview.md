---
title: JavaScript e versões de layout de página
titleSuffix: Azure AD B2C
description: Saiba como habilitar o JavaScript e usar versões de layout de página no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ca9c36b0f8df6651c12d3e94279ad557e67d7487
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87115990"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fornece um conjunto de conteúdo empacotado que contém HTML, CSS e JavaScript para os elementos da interface do usuário em seus fluxos de usuário e políticas personalizadas.

Para habilitar o JavaScript para seus aplicativos:

* Habilite-o no fluxo do usuário usando o portal do Azure
* Selecionar um [layout de página](page-layout.md)
* Usar [b2clogin.com](b2clogin.md) em suas solicitações

Se você pretende habilitar o código [JavaScript](javascript-samples.md) do lado do cliente, os elementos dos quais você baseia seu JavaScript devem ser imutáveis. Se não forem imutáveis, as alterações poderão causar um comportamento inesperado nas páginas do usuário. Para evitar esses problemas, aplique o uso de um layout de página e especifique uma versão de layout de página para garantir que as definições de conteúdo com base em seu JavaScript sejam imutáveis. Mesmo que você não pretenda habilitar o JavaScript, você pode especificar uma versão de layout de página para suas páginas.

## <a name="enable-javascript"></a>Habilitar o JavaScript

Nas **Propriedades**de fluxo do usuário, você pode habilitar o JavaScript. Habilitar o JavaScript também impõe o uso de um layout de página. Em seguida, você pode definir a versão de layout da página para o fluxo do usuário, conforme descrito na próxima seção.

![Página Propriedades do fluxo de usuário com a configuração Habilitar JavaScript realçada](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selecionar uma versão de layout de página

Se você habilitar ou não o JavaScript nas propriedades do fluxo de usuário, poderá especificar uma versão de layout de página para suas páginas de fluxo de usuário. Abra o fluxo de usuário e selecione **layouts de página**. Em **nome do layout**, selecione uma página fluxo de usuário e escolha a **versão de layout da página**.

Para obter informações sobre as diferentes versões de layout de página, consulte o [log de página versão de layout de alteração](page-layout.md).

![Configurações de layout de página no portal mostrando o menu suspenso versão de layout de página](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar exemplos de uso de JavaScript em [exemplos de JavaScript para uso no Azure Active Directory B2C](javascript-samples.md).
