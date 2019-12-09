---
title: Início rápido – Configurar regras e ações no Azure IoT Central
description: Este início rápido mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f81171d0f545bbf7f903d54bee761817e9451d9d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706714"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Início Rápido: Configurar regras e ações para o dispositivo no Azure IoT Central (versão prévia dos recursos)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Neste início rápido, você criará uma regra que envia um email quando a temperatura em um dispositivo de sensor ambiental exceder 90&deg; F.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os dois inícios rápidos anteriores [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-pnp-device.md) para criar o modelo de dispositivo **Sensor Ambiental** com o qual trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao aplicativo, no painel esquerdo, selecione **Regras**.

1. Para criar uma regra, selecione **+Novo**.

1. Insira **Temperatura ambiental** como o nome da regra.

1. Na seção **Dispositivos de destino**, selecione o **Sensor Ambiental** como o modelo de dispositivo. Essa opção filtra os dispositivos aos quais a regra se aplica por tipo de modelo de dispositivo. Você pode adicionar mais critérios de filtro escolhendo **+ Filtro**.

1. Na seção **Condições**, defina o que dispara a regra. Use as seguintes informações para definir uma condição com base na telemetria de temperatura:

    | Campo                                        | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | Medida                                  | Temperatura                       |
    | Operador                                     | é maior que                   |
    | Valor                                        | 90                                |

    Para adicionar mais condições, selecione **+Condição**.

    ![Criar uma condição de regra](./media/quick-configure-rules/condition.png)

1. Para adicionar uma ação de email a ser executada quando a regra for disparada, selecione **+ Email**.

1. Use as informações da seguinte tabela para definir a ação:

    | Configuração   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome de exibição | Ação de email do operador                          |
    | Para        | Seu endereço de email                                |
    | Observações     | A temperatura ambiental excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer.md), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    ![Criar uma ação de regra](./media/quick-configure-rules/action.png)

1. Clique em **Salvar**. A regra é listada na página **Regras**.

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra são atendidas, o aplicativo envia uma mensagem para o endereço de email especificado na ação.

> [!NOTE]
> Depois que os testes forem concluídos, desligue a regra para interromper o recebimento de alertas na caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, passe para o início rápido:

> [!div class="nextstepaction"]
> [Usar o Azure IoT Central para monitorar seus dispositivos](quick-monitor-devices.md).
