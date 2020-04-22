---
title: Conecte uma RuuviTag no Azure IoT Central | Microsoft Docs
description: Aprenda a conectar um sensor de ambiente RuuviTag ao seu aplicativo IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758945"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Conecte um sensor RuuviTag ao seu aplicativo Azure IoT Central

*Este artigo se aplica a construtores de soluções e desenvolvedores de dispositivos.*

Este artigo descreve como, como um construtor de soluções, você pode conectar um sensor RuuviTag ao seu aplicativo Microsoft Azure IoT Central.

O que é uma etiqueta Ruuvi?

RuuviTag é uma plataforma avançada de farol de sensores de código aberto projetada para atender às necessidades de clientes de negócios, desenvolvedores, fabricantes, estudantes e hobistas. O dispositivo é configurado para funcionar assim que você tirá-lo de sua caixa e está pronto para você implantá-lo onde você precisar. É um farol BLUETOOTH LE com um sensor de ambiente e acelerômetro embutido.

RuuviTag se comunica com BLE (Bluetooth Low Energy) e requer um dispositivo gateway para falar com o Azure IoT Central. Certifique-se de ter um dispositivo de gateway, como o Rigado Cascade 500, configuração para permitir que um RuuviTag se conecte à IoT Central.

Por favor, siga as [instruções aqui](./howto-connect-rigado-cascade-500.md) se quiser configurar um dispositivo de gateway Rigado Cascade 500.

## <a name="prerequisites"></a>Pré-requisitos

Para conectar os sensores RuuviTag, você precisa dos seguintes recursos:

* Um sensor RuuviTag. Para obter mais informações, visite [RuuviTag](https://ruuvi.com/).
* Um dispositivo Rigado Cascade 500 ou outro gateway BLE. Para mais informações, visite [Rigado](https://www.rigado.com/).
* Um aplicativo Azure IoT Central. Para obter mais informações, consulte criar [um novo aplicativo](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Adicione um modelo de dispositivo RuuviTag

Para embarcar um sensor RuuviTag na instância de aplicativo do Azure IoT Central, você precisa configurar um modelo de dispositivo correspondente dentro do seu aplicativo.

Para adicionar um modelo de dispositivo RuuviTag:

1. Navegue até a guia ***Modelos de dispositivo*** no painel esquerdo, selecione + **Novo** ![: Crie um novo modelo de](./media/howto-connect-ruuvi/devicetemplate-new.png) dispositivo A página lhe dá a opção de criar um modelo ***personalizado*** ou usar um modelo de ***dispositivo pré-configurado***
1. Selecione o modelo do dispositivo RuuviTag na lista de ![modelos de dispositivos pré-configurados como mostrado abaixo: Selecione o modelo do dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecione ***A seguir: Personalize*** para continuar até a próxima etapa.
1. Na próxima tela, selecione ***Criar*** para integrar o modelo do dispositivo C500 no seu aplicativo IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Conecte um sensor RuuviTag

Como mencionado anteriormente, para conectar o RuuviTag com o seu aplicativo IoT Central, você precisa configurar um dispositivo gateway. As etapas abaixo supõem que você configurou um dispositivo de gateway Rigado Cascade 500.  

1. Ligue seu dispositivo Rigado Cascade 500 e conecte-o à sua conexão de rede (via Ethernet ou sem fio)
1. Retire a tampa do RuuviTag e puxe a aba de plástico para fixar a conexão com a bateria.
1. Coloque o RuuviTag perto de um gateway Rigado Cascade 500 que já está configurado em seu aplicativo IoT Central.
1. Em apenas alguns segundos, seu RuuviTag deve aparecer em sua lista de dispositivos dentro da IoT Central.  
    ![Lista de dispositivos RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Agora você pode usar este RuuviTag dentro do seu aplicativo IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Crie um RuuviTag simulado

Se você não tiver um dispositivo RuuviTag físico, você pode criar um sensor RuuviTag simulado para usar para testes dentro do seu aplicativo Azure IoT Central.

Para criar um RuuviTag simulado:

1. Selecione **Dispositivos > RuuviTag**.
1. Selecione **+ Novo**.
1. Especifique um **ID** de dispositivo exclusivo e um nome de **dispositivo**amigável .  
1. Habilite a **configuração Simulada.**
1. Selecione **Criar**.  

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos são:

- Leia sobre [a conectividade do dispositivo na Central de IoT do Azure](./concepts-get-connected.md)
- Saiba como monitorar a [conectividade do dispositivo usando o Azure CLI](./howto-monitor-devices-azure-cli.md)
