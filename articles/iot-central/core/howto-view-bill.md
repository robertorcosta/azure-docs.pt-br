---
title: Gerencie sua fatura e converta do plano de preços gratuito no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como gerenciar sua fatura e mover-se do plano de preços gratuito para um plano de preços padrão no aplicativo de IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157479"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerenciar sua fatura em um aplicativo IoT Central

Este artigo descreve como, como administrador, você pode gerenciar sua fatura no aplicativo IoT Central do Azure na seção Administração. Você aprenderá como é possível mover seu aplicativo do plano de preços gratuito para um plano de preços padrão e também como atualizar ou fazer downgrade de seu plano de preços.

Para acessar e usar a seção **Administração** , você deve estar na função *administrador* ou ter uma *função de usuário personalizada* que permita exibir a cobrança de um aplicativo IOT central do Azure. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo.

## <a name="move-from-free-to-standard-pricing-plan"></a>Mover do plano de preços gratuito para o padrão

- Os aplicativos que usam o plano de preços gratuito são gratuitos por sete dias antes de expirarem. Para evitar a perda de dados, você pode movê-los para um plano de preços padrão a qualquer momento antes que eles expirem.
- Os aplicativos que usam um plano de preços padrão são cobrados por dispositivo, com os dois primeiros dispositivos livres, por aplicativo.

Saiba mais sobre preços na [Página de preços da microsoft IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na seção de preços, você pode mover seu aplicativo do plano de preços gratuito para um padrão.

Para concluir esse processo de autoatendimento, execute estas etapas:

1. Vá para a página de **preços** na seção **Administração** .

    ![Estado de avaliação](media/howto-view-bill/freetrialbilling.png)

1. Selecione **converter para um plano pago**.

    ![Converter a versão de avaliação](media/howto-view-bill/convert.png)

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo que usa um plano pago.

1. Depois de selecionar **converter**, seu aplicativo agora usa um plano pago e você começa a ser cobrado.

> [!Note]
> Por padrão, você é convertido em um plano de preços *padrão 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Como alterar o plano de preços do aplicativo

Os aplicativos que usam um plano de preços padrão são cobrados por dispositivo, com os dois primeiros dispositivos livres, por aplicativo.

Na seção de preços, você pode atualizar ou fazer downgrade do plano de preços do Azure IoT a qualquer momento.

1. Vá para a página de **preços** na seção **Administração** .

    ![Estado de avaliação](media/howto-view-bill/pricing.png)

1. Selecione o **plano** e clique em **salvar** para atualizar ou fazer downgrade.

## <a name="view-your-bill"></a>Exibir a fatura

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo que usa um plano pago.

1. Depois de selecionar **converter**, seu aplicativo agora usa um plano pago e você começa a ser cobrado.

> [!Note]
> Por padrão, você é convertido em um plano de preços *padrão 2* .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como gerenciar sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md) no Azure IOT central.