---
title: Versões de javaScript e layout de página
titleSuffix: Azure AD B2C
description: Saiba como ativar javaScript e usar versões de layout de página no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185822"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdo embalado contendo HTML, CSS e JavaScript para os elementos de interface do usuário em seus fluxos de usuário e políticas personalizadas.

Para habilitar o JavaScript para seus aplicativos:

* Habilite-o no fluxo de usuário usando o portal Azure
* Selecione um [layout de página](page-layout.md)
* Use [b2clogin.com](b2clogin.md) em suas solicitações

Se você pretende habilitar o código do lado do cliente [javaScript,](javascript-samples.md) os elementos em que você baseia seu JavaScript devem ser imutáveis. Se eles não forem imutáveis, qualquer alteração pode causar um comportamento inesperado em suas páginas de usuário. Para evitar esses problemas, imponha o uso de um layout de página e especifique uma versão de layout de página para garantir que as definições de conteúdo em que você baseou seu JavaScript sejam imutáveis. Mesmo que você não pretenda ativar javaScript, você pode especificar uma versão de layout de página para suas páginas.

## <a name="enable-javascript"></a>Habilitar o JavaScript

No fluxo do usuário **Propriedades,** você pode ativar JavaScript. A ativação do JavaScript também reforça o uso de um layout de página. Em seguida, você pode definir a versão de layout da página para o fluxo de usuário conforme descrito na próxima seção.

![Página de propriedades de fluxo do usuário com configuração Ativar JavaScript destacada](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selecione uma versão de layout de página

Se você habilita rativar ou não o JavaScript nas propriedades do fluxo de usuário, você pode especificar uma versão de layout de página para as páginas de fluxo do usuário. Abra o fluxo do usuário e selecione **layouts de página**. Em **LAYOUT NAME,** selecione uma página de fluxo de usuário e escolha a **versão de layout da página**.

Para obter informações sobre as diferentes versões de layout da página, consulte o [registro de alteração da versão do layout da página](page-layout.md).

![Configurações de layout de página no portal mostrando a versão do layout da página](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar exemplos de uso javaScript em [amostras JavaScript para uso no Azure Active Directory B2C](javascript-samples.md).
