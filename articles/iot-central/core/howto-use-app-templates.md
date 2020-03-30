---
title: Exportar um aplicativo Azure IoT Central | Microsoft Docs
description: Como gerente de soluções, quero exportar um modelo de aplicativo para poder reutilizá-lo.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157582"
---
# <a name="export-your-application"></a>Exportar seu aplicativo



Este artigo descreve como, como gerente de soluções, exportar um aplicativo IoT Central para poder reutilizá-lo.

Você tem duas opções:

- Você pode criar uma cópia do seu aplicativo se você só precisa criar uma cópia duplicada do seu aplicativo.
- Você pode criar um modelo de aplicativo a partir do seu aplicativo se você planeja criar várias cópias.

## <a name="copy-your-application"></a>Copie seu aplicativo

É possível criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivos, histórico de dados do dispositivo e os dados de usuário. A cópia usa um plano de preços padrão para o que você será cobrado. Você não pode criar um aplicativo que use o plano de preços gratuitocopiando um aplicativo.

Selecione **Copiar**. Na caixa de diálogo, digite os detalhes do novo aplicativo. Em seguida, **selecione Copiar** para confirmar se deseja continuar. Para saber mais sobre os campos no formulário, consulte [o Criar um aplicativo](quick-deploy-iot-central.md) quickstart.

![Página de configurações do aplicativo](media/howto-use-app-templates/appcopy2.png)

Depois que a operação de cópia do aplicativo for bem sucedida, você pode navegar até o novo aplicativo usando o link.

![Página de configurações do aplicativo](media/howto-use-app-templates/appcopy3a.png)

Copiar um aplicativo também copia a definição de regras e ação de e-mail. Algumas ações, como Flow e Logic Apps, estão vinculadas a regras específicas através do ID de regra. Quando uma regra é copiada para um aplicativo diferente, ela recebe seu próprio ID de regra. Neste caso, os usuários terão que criar uma nova ação e, em seguida, associar a nova regra com ela. Em geral, é uma boa ideia verificar as regras e ações para ter certeza de que estão atualizadas no novo aplicativo.

> [!WARNING]
> Se um painel de instrumentos incluir telhas que exibem informações sobre dispositivos específicos, então essas telhas mostrarão **O recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar essas telhas para exibir informações sobre dispositivos em seu novo aplicativo.

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

Quando você cria um aplicativo Azure IoT Central, você tem uma escolha de modelos de exemplo incorporados. Você também pode criar seus próprios modelos de aplicativos a partir de aplicativos IoT Central existentes. Em seguida, você pode usar seus próprios modelos de aplicativos quando criar novos aplicativos.

Quando você cria um modelo de aplicativo, ele inclui os seguintes itens do aplicativo existente:

- O painel de aplicativos padrão, incluindo o layout do painel e todas as telhas definidas.
- Modelos de dispositivos, incluindo medidas, configurações, propriedades, comandos e painel de instrumentos.
- Regras. Todas as definições de regras estão incluídas. No entanto, as ações, exceto para ações de e-mail, não estão incluídas.
- Conjuntos de dispositivos, incluindo suas condições e painéis.

> [!WARNING]
> Se um painel de instrumentos incluir telhas que exibem informações sobre dispositivos específicos, então essas telhas mostrarão **O recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar essas telhas para exibir informações sobre dispositivos em seu novo aplicativo.

Quando você cria um modelo de aplicativo, ele não inclui os seguintes itens:

- Dispositivos
- Usuários
- Definições de trabalho
- Definições contínuas de exportação de dados

Adicione esses itens manualmente a quaisquer aplicativos criados a partir de um modelo de aplicativo.

Para criar um modelo de aplicativo a partir de um aplicativo Central de IoT existente:

1. Vá para a seção **Administração** em sua aplicação.
1. Selecione **Exportação de modelo de aplicativo**.
1. Na página **Exportar modelo de aplicativo,** digite um nome e uma descrição para o seu modelo.
1. Selecione o botão **Exportar** para criar o modelo do aplicativo. Agora você pode copiar o **Link Compartilhável** que permite que alguém crie um novo aplicativo a partir do modelo:

![Criar um modelo de aplicativo](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Use um modelo de aplicativo

Para usar um modelo de aplicativo para criar um novo aplicativo IoT Central, você precisa de um **Link Compartilhado**criado anteriormente. Cole o **Link Compartilhável** na barra de endereços do seu navegador. A **página Criar uma** página de aplicativo é exibida com o modelo de aplicativo personalizado selecionado:

![Crie um aplicativo a partir de um modelo](media/howto-use-app-templates/create-app.png)

Selecione seu plano de preços e preencha os outros campos no formulário. Em seguida, selecione **Criar** para criar um novo aplicativo IoT Central a partir do modelo do aplicativo.

### <a name="manage-application-templates"></a>Gerenciar modelos de aplicativos

Na página **Exportação de modelo de aplicativo,** você pode excluir ou atualizar o modelo do aplicativo.

Se você excluir um modelo de aplicativo, você não poderá mais usar o link compartilhável gerado anteriormente para criar novos aplicativos.

Para atualizar o modelo do aplicativo, altere o nome ou a descrição do modelo na página **Exportação de modelos de aplicativo.** Em seguida, selecione novamente o botão **Exportar.** Essa ação gera um novo **link compartilhável** e invalida qualquer URL **de link compartilhável** anterior.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar modelos de aplicativos, o próximo passo sugerido é aprender como [gerenciar a Central de IoT a partir do portal Azure](howto-manage-iot-central-from-portal.md)
