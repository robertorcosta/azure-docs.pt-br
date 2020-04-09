---
title: Criar painéis pessoais do Azure IoT Central | Microsoft Docs
description: Como usuário, aprenda a criar e gerenciar seus dashboards pessoais.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985483"
---
# <a name="create-and-manage-multiple-dashboards"></a>Crie e gerencie vários dashboards

O **Painel de Controle** é a página que carrega quando você navega pela primeira vez para o seu aplicativo. Um **construtor** em seu aplicativo define o painel de aplicativos padrão para todos os usuários. Além disso, você pode criar seu próprio painel de aplicativos personalizado. Você pode ter vários dashboards que exibem dados diferentes e alternam entre eles.

Se você é **um admin** do aplicativo, você também pode criar até 10 dashboards de nível de aplicativo para compartilhar com outros usuários do aplicativo. Apenas **os admins** têm a capacidade de criar, editar e excluir painéis de nível de aplicativo. 

## <a name="create-dashboard"></a>Criar painel

A captura de tela a seguir mostra o painel de instrumentos em um aplicativo criado a partir do modelo **De aplicativo personalizado.** Você pode substituir o painel de aplicativos padrão por um painel pessoal, ou se você é um admin, outro painel de nível de aplicativo. Para isso, selecione **+ Novo** no canto superior esquerdo da página.
 
> [!div class="mx-imgBorder"]
> ![Painel para aplicativos baseado no modelo "Aplicativo Personalizado"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionando **+ Novo** abre o editor do painel. No editor, você pode dar um nome ao seu painel e escolher itens da biblioteca. A biblioteca contém os azulejos e os primitivos do painel que você pode usar para personalizar o painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-create-personal-dashboards/dashboard-library.png)

Se você é **um admin** do aplicativo, você terá a opção de alternar se você quiser criar um painel de nível pessoal ou um painel de nível de aplicativo. Se você criar um painel de nível pessoal, apenas você poderá vê-lo. Se você criar um painel de nível de aplicativo, cada usuário do aplicativo poderá vê-lo. Depois de inserir um título e selecionar o tipo de painel que deseja criar, você pode salvar e adicionar telhas mais tarde. Ou se você estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância do dispositivo, você pode ir em frente e criar seu primeiro azulejo. 

> [!div class="mx-imgBorder"]
> ![Configure o formulário Detalhes do Dispositivo com detalhes para Temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, você pode adicionar uma telha **de telemetria** para a temperatura atual do dispositivo. Para fazer isso:
1. Selecione um **modelo de dispositivo**
1. Selecione uma **instância de dispositivo** para o dispositivo que você deseja ver em um painel de instrumentos. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no azulejo.
1. Para criar o azulejo no painel, clique em **Temperatura** e arraste-o para a área do painel. Você também pode clicar na caixa de seleção ao lado de **Temperature** e clicar **em Combinar**. A captura de tela a seguir mostra a seleção de um modelo de dispositivo e uma instância do dispositivo, em seguida, criando um azulejo de telemetria de temperatura no painel.
1. Selecione **Salvar** no canto superior esquerdo para salvar o azulejo no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Guia painel com detalhes para o azulejo Temperature](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando você visualiza seu painel pessoal, você vê o novo azulejo com a configuração **temperatura** para o dispositivo:

> [!div class="mx-imgBorder"]
> ![Guia painel com detalhes para o azulejo Temperature](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de azulejos na biblioteca para descobrir como personalizar ainda mais seus painéis pessoais.

Para saber mais sobre como usar telhas no Azure IoT Central, consulte [Adicionar azulejos ao seu painel](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerenciar painéis de controle

Você pode ter vários dashboards pessoais e alternar entre eles ou escolher entre um dos painéis de aplicativos padrão:

> [!div class="mx-imgBorder"]
> ![Alternar entre painéis](media/howto-create-personal-dashboards/switch-dashboards.png)

Você pode editar seus painéis pessoais e excluir quaisquer painéis que você não precise mais. Se você é **um admin,** você também tem a capacidade de editar ou excluir painéis de nível de aplicativo também.

> [!div class="mx-imgBorder"]
> ![Excluir painéis](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar e gerenciar dashboards pessoais, você pode [aprender como gerenciar suas preferências de aplicativos](howto-manage-preferences.md)
