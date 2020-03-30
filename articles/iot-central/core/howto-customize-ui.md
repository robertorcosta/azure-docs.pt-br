---
title: Personalizar a UI Central de IoT do Azure | Microsoft Docs
description: Como personalizar o tema e ajudar links para o seu aplicativo central De IoT Do Zure
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157957"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalize a UI Central de IoT do Azure

Este artigo descreve como, como administrador, você pode personalizar a interface do usuário aplicando temas personalizados e modificando os links de ajuda para apontar seus próprios recursos de ajuda personalizados. 



A captura de tela a seguir mostra uma página usando o tema padrão:

![Tema padrão da IoT Central](./media/howto-customize-ui/standard-ui.png)

A captura de tela a seguir mostra uma página usando uma captura de tela personalizada com os elementos de IU personalizados destacados:

![Tema personalizado da Central de IoT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar tema

Para criar um tema personalizado, navegue até a **página Personalizar sua** página de aplicativo na seção **Administração:**

![Temas da Central de IoT](./media/howto-customize-ui/themes.png)

Nesta página, você pode personalizar os seguintes aspectos do seu aplicativo:

### <a name="application-logo"></a>Logotipo do aplicativo

Uma imagem PNG, não maior que 1 MB, com um fundo transparente. Este logotipo é exibido à esquerda na barra de título do aplicativo IoT Central.

Se a imagem do logotipo incluir o nome do seu aplicativo, você pode ocultar o texto do nome do aplicativo. Para obter mais informações, consulte [Gerenciar sua aplicação](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ícone do navegador (favicon)

Uma imagem PNG, não maior que 32 x 32 pixels, com um fundo transparente. Um navegador da Web pode usar essa imagem na barra de endereços, histórico, marcadores e guia do navegador.

### <a name="browser-colors"></a>Cores do navegador

Você pode alterar a cor do cabeçalho da página e a cor usada para acentuar botões e outros destaques. Use um valor de cor hexodana de seis caracteres no formato `##ff6347`. Para obter mais informações sobre a notação de cor **HEX Value,** consulte [CORES HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Você sempre pode reverter para as opções padrão na **página Personalizar sua página de aplicativo.**

### <a name="changes-for-operators"></a>Mudanças para operadores

Se um administrador criar um tema personalizado, os operadores e outros usuários do seu aplicativo não poderão mais escolher um tema em **Configurações**.

## <a name="replace-help-links"></a>Substitua os links de ajuda

Para fornecer informações de ajuda personalizadas para seus operadores e outros usuários, você pode modificar os links no menu **ajuda** do aplicativo.

Para modificar os links de ajuda, navegue até a página **personalizar a** ajuda na seção **Administração:**

![Personalize links de ajuda da IoT Central](./media/howto-customize-ui/help-links.png)

Você também pode adicionar novas entradas ao menu de ajuda e remover entradas padrão:

![Ajuda personalizada da Central de IoT](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Você sempre pode reverter para os links de ajuda padrão na página **de ajuda Personalizar.**

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a personalizar a interface do usuário em seu aplicativo IoT Central, aqui estão alguns passos sugeridos:

- [Administrar seu aplicativo](./howto-administer.md)
- [Adicionar blocos ao dashboard](howto-add-tiles-to-your-dashboard.md)