---
title: Criar um aplicativo Azure IoT Central | Microsoft Docs
description: Crie um novo aplicativo Azure IoT Central. Crie um aplicativo de Avaliação ou de Pagamento Conforme o Uso usando um modelo de aplicativo.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001256"
---
# <a name="create-an-azure-iot-central-application"></a>Crie um aplicativo Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Como um _construtor_, use a interface do usuário do Azure IoT Central para definir seu aplicativo Microsoft Azure IoT Central. Este início rápido mostra como criar um aplicativo Azure IoT Central que contém um _modelo de dispositivo_ de exemplo.

## <a name="create-an-application"></a>Criar um aplicativo

Navegue até o site do [Gerenciador de aplicativos do Azure IoT Central](https://aka.ms/iotcentral). Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Para começar a criar um novo aplicativo Azure IoT Central, selecione **Novo Aplicativo**. Esse link leva você para a página **Criar um aplicativo**.

![Página Criar um aplicativo do Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Para criar um novo aplicativo Azure IoT Central:

1. Escolha um plano de pagamento:
   - Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Eles podem ser convertidos em **Pagamento Conforme o Uso** em qualquer momento antes de expirarem. Se você criar um aplicativo de **Avaliação**, precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft.
   - Os aplicativos de **Pagamento Conforme o Uso** são cobrados por dispositivo, com os cinco primeiros dispositivos gratuitos. Se você criar um aplicativo de **Pagamento Conforme o Uso**, precisará escolher o *Directory*, a *Assinatura do Azure* e a *Região*:
        - *Directory* é o AD (Azure Active Directory) para criar o aplicativo. Ele contém identidades de usuário, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, ele será gerado quando você criar uma assinatura do Azure.
        - Uma *Assinatura do Azure* permite que você crie instâncias de serviços do Azure. O IoT Central provisiona recursos em sua assinatura. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, volte à página **Criar um aplicativo**. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.
        - A *Região* é a localização física ou a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) em que você deseja criar seu aplicativo. Normalmente, você deve escolher a região fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. É possível visualizar as regiões nas quais o Azure IoT Central está disponível, na página [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central). Depois de escolher uma região, você não poderá mover o aplicativo para outra região posteriormente.

        Saiba mais sobre preços na [Página de preços da microsoft IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Selecione um modelo de aplicativo. Um modelo de aplicativo pode conter itens predefinidos, como modelos de dispositivo e dashboards, para ajudá-lo a começar.

    | Modelo de aplicativo | DESCRIÇÃO |
    | -------------------- | ----------- |
    | Exemplo Contoso       | Cria um aplicativo que inclui um modelo de dispositivo já criado para uma Máquina de Vendas Refrigerada. Use esse modelo para começar a explorar o Azure IoT Central. |
    | Exemplo Devkits       | Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos que está experimentando com algum desses dispositivos. |
    | Aplicativo personalizado   | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo de aplicativo selecionado. Aceite esse nome ou insira seu próprio nome de aplicativo amigável, como **Contoso IoT**. O Azure IoT Central também gera um prefixo de URL exclusivo para você, com base no nome do aplicativo. Você terá a liberdade para alterar esse prefixo de URL para algo mais fácil de memorizar se desejar.

1. Preencha as informações adicionais necessárias para o plano de pagamento selecionado anteriormente, na Etapa 1.

1. Selecione **Criar** na parte inferior da página.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um aplicativo IoT Central. Aqui estão sugestões para as próximas etapas:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](./tutorial-define-device-type.md)
