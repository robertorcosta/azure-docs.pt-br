---
title: Conecte uma Casca de Rigado 500 na Central de IoT azure | Microsoft Docs
description: Aprenda a conectar um dispositivo de gateway Rigado Cascade 500 ao seu aplicativo IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158314"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Conecte um dispositivo de gateway Rigado Cascade 500 ao seu aplicativo Azure IoT Central


Este artigo descreve como, como um construtor de soluções, você pode conectar um dispositivo de gateway Rigado Cascade 500 ao seu aplicativo Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>O que é cascade 500?

O gateway Cascade 500 IoT é uma oferta de hardware da Rigado que está incluída como parte de sua solução Cascade Edge-as-a-Service. Ele fornece equipes comerciais de projetos de IoT e produtos com poder de computação de borda flexível, um ambiente de aplicação robusto em contêineres e uma grande variedade de opções de conectividade de dispositivos sem fio, incluindo Bluetooth 5, LTE & Wi-Fi.

O Cascade 500 é pré-certificado para o Azure IoT Plug and Play (preview) permitindo que nossos construtores de soluções abordo facilmente o dispositivo em suas soluções de ponta a ponta. O gateway Cascade permite que você se conecte sem fio a uma variedade de sensores de monitoramento de condições que estão próximos ao dispositivo gateway. Esses sensores podem ser embarcados na IoT Central através do dispositivo gateway.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como fazer, você precisa dos seguintes recursos:

* Um dispositivo Rigado Cascade 500. Para mais informações, visite [Rigado](https://www.rigado.com/).
* Um aplicativo Azure IoT Central. Para obter mais informações, consulte criar [um novo aplicativo](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Para embarcar em um dispositivo de gateway Cascade 500 na instância de aplicativo Do Azure IoT Central, você precisará configurar um modelo de dispositivo correspondente dentro do seu aplicativo.

Para adicionar um modelo de dispositivo Cascade 500: 

1. Navegue até a guia ***Modelos de dispositivo*** no ![painel esquerdo, selecione + **Novo**: Crie um novo modelo de dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. A página lhe dá a opção ***de criar um modelo personalizado*** ou usar um modelo de dispositivo ***pré-configurado***
1. Selecione o modelo do dispositivo C500 na lista de ![modelos de dispositivos pré-configurados como mostrado abaixo: Selecione o modelo do dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecione ***A seguir: Personalize*** para continuar até a próxima etapa. 
1. Na próxima tela, selecione ***Criar*** para integrar o modelo do dispositivo C500 no seu aplicativo IoT Central.

## <a name="retrieve-application-connection-details"></a>Recuperar detalhes da conexão do aplicativo

Agora você precisará recuperar o **ID do escopo** e **a chave principal** para o aplicativo Azure IoT Central para conectar o dispositivo Cascade 500. 

1. Navegue até **a administração** no painel esquerdo e clique na **conexão do dispositivo**. 
2. Anote o **ID** do escopo para o seu aplicativo IoT Central.
![ID do escopo do aplicativo](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Agora clique em **'Exibir teclas'** e anote a **tecla**
![principal principal](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Entre em contato com Rigado para conectar o gateway 

Para conectar o dispositivo Cascade 500 ao seu aplicativo IoT Central, você precisará entrar em contato com rigado e fornecer-lhes os detalhes de conexão do aplicativo das etapas acima. 

Uma vez que o dispositivo esteja conectado à internet, o Rigado poderá empurrar uma atualização de configuração para o dispositivo gateway Cascade 500 através de um canal seguro. 

Esta atualização aplicará os detalhes de conexão IoT Central no dispositivo Cascade 500 e aparecerá na lista de dispositivos. 

![Chave primária](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Agora você está pronto para usar seu dispositivo C500 em seu aplicativo IoT Central!

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um Rigado Cascade 500 ao seu aplicativo Azure IoT Central, o próximo passo sugerido é aprender como [criar um aplicativo de análise na loja](../retail/tutorial-in-store-analytics-create-app-pnp.md) para construir uma solução de ponta a ponta. 