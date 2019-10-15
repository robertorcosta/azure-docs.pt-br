---
title: Monitorar seus dispositivos no Azure IoT Central | Microsoft Docs
description: Como um operador, use o seu aplicativo Azure IoT Central para monitorar seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f68bc211be3cffb61b3381390ae2eeacaffa4213
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960416"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Tutorial: Use o Azure IoT Central para monitorar seus dispositivos

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este tutorial mostra a você, como um operador, como usar o seu aplicatio Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Receber uma notificação
> * Investigar um problema
> * Resolver um problema

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir os três tutorial para construtores para criar o aplicativo Azure IoT Central:

* [Definir um novo tipo de dispositivo](tutorial-define-device-type.md)
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre dispositivos como mensagens de email. O construtor adicionou uma regra para enviar uma notificação quando a temperatura em um ar-condicionado conectado exceder um limite. Verifique os emails enviados para a conta escolhida pelo construtor para receber notificações.

Abra a mensagem de email recebida no final do tutorial [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md). No email, selecione o link para o dispositivo ao lado de **Nome do Dispositivo** na seção **Detalhes**:

![Email de notificação de alerta](media/tutorial-monitor-devices/email.png)

A página **Dispositivo** para o dispositivo simulado **Ar-condicionado conectado 1** que você criou nos tutoriais anteriores abre no navegador:

![Dispositivo que disparou a mensagem de email com a notificação](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode ver informações sobre as páginas de **Medidas**, **Configurações**, **Propriedades**, **Regras** e **Painel**. O construtor personalizou o **Painel** para exibir informações importantes sobre um dispositivo de ar-condicionado conectado.

Escolha o modo de exibição de **Painel** para ver informações sobre o dispositivo.

![Painel do dispositivo](media/tutorial-monitor-devices/initial_screen.png)

O gráfico no painel mostra um gráfico de temperatura do dispositivo. Você também pode ver a temperatura de destino atual para o dispositivo no bloco **Propriedades do destino**. Você decide se a temperatura de destino está muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para alterar a temperatura de destino do dispositivo, use a página **Configurações**:

1. Escolha **Configurações**. Altere a **Configuração de temperatura** para 75. Escolha **Atualizar** para enviar a nova temperatura de destino par ao dispositivo. Quando o dispositivo confirma as alterações de configuração, o status da configuração muda para **sincronizado**:

    ![Atualizar configurações](media/tutorial-monitor-devices/change_settings.png)

2. Escolha **Painel** e verifique o novo valor configurado:

    ![Painel do dispositivo atualizado](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="nextstepaction"]
> * Receber uma notificação
> * Investigar um problema
> * Resolver um problema

Agora que você sabe como monitorar o seu dispositivo, a próxima etapa sugerida é [Adicionar um dispositivo](tutorial-add-device.md).