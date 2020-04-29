---
title: Criar painéis pessoais do Azure IoT Central | Microsoft Docs
description: Como usuário, saiba como criar e gerenciar seus painéis pessoais.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985483"
---
# <a name="create-and-manage-multiple-dashboards"></a>Criar e gerenciar vários dashboards

O **painel** é a página que carrega quando você navega pela primeira vez para seu aplicativo. Um **Construtor** em seu aplicativo define o painel do aplicativo padrão para todos os usuários. Além disso, você pode criar seu próprio painel de aplicativo personalizado. Você pode ter vários painéis que exibem dados diferentes e alternam entre eles.

Se você for um **administrador** do aplicativo, também poderá criar até 10 painéis de nível de aplicativo para compartilhar com outros usuários do aplicativo. Somente os **Administradores** têm a capacidade de criar, editar e excluir painéis de nível de aplicativo. 

## <a name="create-dashboard"></a>Criar painel

A captura de tela a seguir mostra o painel em um aplicativo criado a partir do modelo de **aplicativo personalizado** . Você pode substituir o painel do aplicativo padrão por um painel pessoal, ou se você for um administrador, outro painel de nível de aplicativo. Para fazer isso, selecione **+ novo** na parte superior esquerda da página.
 
> [!div class="mx-imgBorder"]
> ![Painel para aplicativos com base no modelo "aplicativo personalizado"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionar **+ novo** abre o editor de painel. No editor, você pode dar um nome ao seu painel e escolher itens da biblioteca. A biblioteca contém os blocos e os primitivos de painel que você pode usar para personalizar o painel.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-create-personal-dashboards/dashboard-library.png)

Se você for um **administrador** do aplicativo, terá a opção de alternar se quiser criar um painel de nível pessoal ou um painel de nível de aplicativo. Se você criar um painel de nível pessoal, apenas você poderá vê-lo. Se você criar um painel de nível de aplicativo, todos os usuários do aplicativo poderão vê-lo. Depois de inserir um título e selecionar o tipo de painel que você deseja criar, você pode salvar e adicionar blocos posteriormente. Ou, se você estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância de dispositivo, poderá criar seu primeiro bloco. 

> [!div class="mx-imgBorder"]
> ![Configurar detalhes do dispositivo ", formulário com detalhes de temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, você pode adicionar um bloco de **telemetria** para a temperatura atual do dispositivo. Para fazer isso:
1. Selecionar um **modelo de dispositivo**
1. Selecione uma **instância de dispositivo** para o dispositivo que você deseja ver em um bloco do Dashboard. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no bloco.
1. Para criar o bloco no painel, clique em **temperatura** e arraste-o para a área painel. Você também pode clicar na caixa de seleção ao lado de **temperatura** e clicar em **combinar**. A captura de tela a seguir mostra a seleção de um modelo de dispositivo e uma instância de dispositivo, criando um bloco de telemetria de temperatura no painel.
1. Selecione **salvar** na parte superior esquerda para salvar o bloco no painel.

> [!div class="mx-imgBorder"]
> ![Painel "com detalhes para o bloco de temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando você exibir seu painel pessoal, verá o novo bloco com a configuração de **temperatura** para o dispositivo:

> [!div class="mx-imgBorder"]
> ![Painel "com detalhes para o bloco de temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de bloco na biblioteca para descobrir como personalizar ainda mais seus painéis pessoais.

Para saber mais sobre como usar blocos no Azure IoT Central, confira [Adicionar blocos ao seu painel](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerenciar painéis

Você pode ter vários painéis pessoais e alternar entre eles ou escolher um dos painéis de aplicativo padrão:

> [!div class="mx-imgBorder"]
> ![Alternar entre painéis](media/howto-create-personal-dashboards/switch-dashboards.png)

Você pode editar seus painéis pessoais e excluir os painéis que não são mais necessários. Se você for um **administrador**, também terá a capacidade de editar ou excluir painéis de nível de aplicativo também.

> [!div class="mx-imgBorder"]
> ![Excluir painéis](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar e gerenciar painéis pessoais, você pode [aprender a gerenciar suas preferências de aplicativo](howto-manage-preferences.md)
