---
title: Início Rápido – Monitorar seus dispositivos no Azure IoT Central
description: Início Rápido – Como operador, aprenda a usar o aplicativo do Azure IoT Central para monitorar os dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: af5683bf253e26ab928e46059f9af9d2ab8af3bd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90987338"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Início Rápido: Use o Azure IoT Central para monitorar seus dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*

Este início rápido mostra a você, como um operador, como usar o seu aplicativo do Microsoft Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os três inícios rápidos anteriores [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md), [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-simulated-device.md) e [Configurar regras e ações para seu dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre dispositivos como mensagens de email. O construtor adicionou uma regra para enviar uma notificação quando a temperatura em um sensor de dispositivo conectado exceder um limite. Verifique os emails enviados para a conta escolhida pelo construtor para receber notificações.

Abra a mensagem de email recebida no final do início rápido [Configurar regras e ações para o seu dispositivo](quick-configure-rules.md). No email, selecione o link para o dispositivo:

![Email de notificação de alerta](media/quick-monitor-devices/email.png)

A exibição **Visão geral** do dispositivo simulado que você criou nos inícios rápidos anteriores é aberta no navegador:

![Dispositivo que disparou a mensagem de email com a notificação](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode exibir informações sobre o dispositivo nas exibições **Visão geral**, **Sobre** e **Comandos**. O construtor criou uma exibição **Gerenciar dispositivos** para que você edite informações do dispositivo e defina as propriedades do dispositivo.

O gráfico no painel mostra um gráfico de temperatura do dispositivo. Você decide se a temperatura do dispositivo está muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para fazer uma alteração no dispositivo, use a página **Gerenciar dispositivo**.

Altere a **Velocidade do ventilador** para 500 para resfriar o dispositivo. Escolha **Salvar** para atualizar o dispositivo. Quando o dispositivo confirma as alterações de configuração, o status da propriedade muda para **sincronizado**:

![Atualizar configurações](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que você sabe como monitorar o dispositivo, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Criar e gerenciar um modelo de dispositivo](howto-set-up-template.md).
