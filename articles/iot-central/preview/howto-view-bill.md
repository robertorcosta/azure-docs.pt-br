---
title: Gerencie sua fatura e converta a avaliação para pagamento conforme o uso no aplicativo de IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como gerenciar sua fatura e o seu aplicativo de avaliação para pagamento à medida que você estiver em sua aplicação de IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1c1643fb8c8394ce3ce50e5926f05fd4d366f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976410"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerenciar sua fatura em um aplicativo IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode gerenciar sua fatura no aplicativo IoT Central do Azure na seção Administração e também como você pode converter sua versão de avaliação para pré-pago.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo.

## <a name="view-your-bill"></a>Exibir a fatura

Para exibir a fatura, vá até a página **Cobrança** na seção **Administração**. A página de cobrança do Azure abrirá em uma nova guia e você poderá exibir a fatura de cada um dos aplicativos do Azure IoT Central.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Converta sua avaliação para pré-paga

- Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Eles podem ser convertidos em Pagamento Conforme o Uso em qualquer momento antes de expirarem.
- Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os primeiros cinco dispositivos gratuitos, por assinatura.

Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na seção de cobrança, você pode converter seus aplicativos de avaliação em pré-pago.

Para concluir esse processo de autoatendimento, execute estas etapas:

1. Vá para a página **Faturamento** na seção **Administração**.

    ![Estado de avaliação](media/howto-view-bill/freetrialbilling.png)

1. Selecione **converter para pago conforme o uso**.

    ![Converter a versão de avaliação](media/howto-view-bill/convert.png)

1. Selecione o Microsoft Azure Active Directory apropriado do Azure e, em seguida, a assinatura do Azure a ser usada em seu aplicativo Pagamento Conforme o Uso.

1. Depois de selecionar **converter**, seu aplicativo agora é um aplicativo pago conforme o uso e você começa a ser cobrado.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu sobre como gerenciar sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md) no Azure IOT central.