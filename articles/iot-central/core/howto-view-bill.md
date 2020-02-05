---
title: Gerencie sua fatura e converta do plano de preços gratuito no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como gerenciar sua fatura e mover-se do plano de preços gratuito para um plano de preços padrão no aplicativo de IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e45109842a081b437c20de81321bf70f909efa2a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982323"
---
# <a name="view-your-bill-in-iot-central-application"></a>Exibir sua fatura no aplicativo IoT Central

Este artigo descreve como, como administrador, você pode gerenciar sua fatura no aplicativo IoT Central do Azure na seção Administração. Você aprenderá como é possível mover seu aplicativo do plano de preços gratuito para um plano de preços padrão e também como atualizar ou fazer downgrade de seu plano de preços.

Para acessar e usar a seção **Administração** , você deve estar na função *administrador* ou ter uma *função de usuário personalizada* que permita exibir a cobrança de um aplicativo IOT central do Azure. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo.

## <a name="move-from-free-to-standard-pricing-plan"></a>Mover do plano de preços gratuito para o padrão

- Os aplicativos que usam o plano de preços gratuito são gratuitos por sete dias antes de expirarem. Para evitar a perda de dados, você pode movê-los para um plano de preços padrão a qualquer momento antes que eles expirem.

Na seção de preços, você pode mover seu aplicativo do plano de preços gratuito para um padrão.

Para concluir esse processo de autoatendimento, execute estas etapas:

1. Vá para a página de **preços** na seção **Administração** .

    ![Estado de avaliação](media/howto-administer/freetrialbilling.png)

1. Selecione **converter para um plano pago**.

    ![Converter a versão de avaliação](media/howto-administer/convert.png)

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo que usa um plano pago.

1. Depois de selecionar **converter**, seu aplicativo agora usa um plano pago e você começa a ser cobrado.

> [!Note]
> Por padrão, você é convertido em um plano de preços *padrão 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Como alterar o plano de preços do aplicativo

- Os aplicativos que usam um plano de preços padrão são cobrados por dispositivo, com os dois primeiros dispositivos livres, por aplicativo.

Na seção de preços, você pode atualizar ou fazer downgrade do plano de preços do Azure IoT a qualquer momento.

1. Vá para a página de **preços** na seção **Administração** .

    ![Estado de avaliação](media/howto-administer/pricing.png)

1. Selecione o **plano** e clique em **salvar** para atualizar ou fazer downgrade.

Saiba mais sobre preços na [Página de preços da microsoft IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="view-your-bill"></a>Exibir a fatura

Para exibir sua fatura, vá para a página de **preços** na seção **Administração** . A página de preços do Azure é aberta em uma nova guia, na qual você pode ver a fatura de cada um dos seus aplicativos de IoT Central do Azure.


## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu sobre como exibir sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é saber mais sobre como [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md) no Azure IOT central.