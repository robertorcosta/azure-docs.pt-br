---
title: Criar um aplicativo de monitoramento de painel solar com o IoT Central | Microsoft Docs
description: Saiba como criar um aplicativo de painel solar usando os modelos de aplicativo do Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c560c7e298dabbb5a3c31d90e0d48d0d63cfac5f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581514"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Criar e percorrer o modelo de aplicativo de monitoramento do painel solar 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você pelo processo de criação do aplicativo de monitoramento de painel solar, que inclui um modelo de dispositivo de exemplo cujos dados são simulados. Neste tutorial, você aprenderá:


> [!div class="checklist"]
> * A criar o aplicativo de painel solar gratuitamente
> * Passo a passo do aplicativo
> * Limpar recursos


Se não tiver uma assinatura, crie uma [conta de avaliação gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhum
- A assinatura do Azure é recomendada, mas não é obrigatória para experimentar


## <a name="create-a-solar-panel-monitoring-app"></a>Criar um aplicativo de monitoramento de painel solar 

Você pode criar esse aplicativo em três etapas simples:

1. Abra a [home page do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Criar** para criar um novo aplicativo. 

2. Selecione a guia **Energia** e clique em **Criar aplicativo** no bloco de aplicativo **Monitoramento de painel solar**. 

    > [!div class="mx-imgBorder"]
    > ![Compilar o aplicativo](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Criar aplicativo** abrirá o formulário **Novo aplicativo**. Preencha os detalhes solicitados, conforme mostrado na figura abaixo:
    * **Nome do aplicativo**: escolha o nome para seu aplicativo do IoT Central. 
    * **URL**: escolha uma URL do IoT Central, a plataforma verificará sua exclusividade.
    * **Avaliação gratuita de 7 dias**: se você já tiver uma assinatura do Azure, a configuração padrão será recomendada. Se não tiver uma assinatura do Azure, comece com uma avaliação gratuita.
    * **Informações de cobrança**: o aplicativo é gratuito. é necessário informar os detalhes em Diretório, Assinatura do Azure e Região para provisionar os recursos para seu aplicativo.
    * Clique no botão **Criar** na parte inferior da página e seu aplicativo será criado em cerca de um minuto.
        > [!div class="mx-imgBorder"]
        > ![Formulário de novo aplicativo](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifique o aplicativo e os dados simulados

O aplicativo de painel solar recém-criado é seu e você pode modificá-lo a qualquer momento. Vamos garantir que o aplicativo esteja implantado e que funcione conforme o esperado antes de modificá-lo.

Para verificar a criação do aplicativo e a simulação de dados, acesse o **Painel**. Se você puder ver os blocos com alguns dados, a implantação do aplicativo terá sido bem-sucedida. A simulação de dados pode levar algum tempo para gerar os dados, portanto, aguarde 1 ou 2 minutos. 

## <a name="application-walk-through"></a>Passo a passo do aplicativo
Após você implantar com êxito o modelo de aplicativo, ele vem com o dispositivo de medidor inteligente de exemplo, o modelo de dispositivo e o painel.

Adatum é uma empresa de energia fictícia, que monitora e gerencia painéis solares. No painel de monitoramento de painel solar, você vê as propriedades do painel solar, os dados e os comandos de exemplo. Ele permite que os operadores e as equipes de suporte executem proativamente as seguintes atividades antes que ocorram incidentes de suporte:
* Examinar as informações mais recentes do painel e sua localização de instalação no mapa
* Verificar proativamente o status do painel e o status da conexão
* Examinar tendências de geração de energia e temperatura para capturar padrões anormais
* Acompanhar a geração de energia total para fins de planejamento e cobrança
* Comandar e controlar operações, como ativar o painel e atualizar a versão do firmware. No modelo, os botões de comando mostram as funcionalidades possíveis e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> ![Painel de monitoramento de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
O aplicativo vem com um dispositivo de painel solar de exemplo. Você pode ver os detalhes do dispositivo clicando na guia **Dispositivos**.

> [!div class="mx-imgBorder"]
> ![Dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Clique no link do dispositivo de exemplo **SP0123456789** para ver os detalhes. Na página **Atualizar Propriedades**, você pode atualizar as propriedades graváveis do dispositivo e visualizar os valores atualizados no painel. 

> [!div class="mx-imgBorder"]
> ![Propriedades do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Clique na guia **Modelos de dispositivo** para ver o modelo de dispositivo do painel solar. O modelo tem uma interface predefinida para Dados, Propriedade, Comandos e Exibições.

> [!div class="mx-imgBorder"]
> ![Modelo de dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar recursos
Se optar por não continuar usando este aplicativo, exclua-o seguindo estas etapas:

1. No painel esquerdo, abra a guia Administração
2. Selecione Configurações do aplicativo e clique no botão Excluir na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Excluir aplicativo](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a arquitetura do aplicativo de painel solar conferindo o [artigo de conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Crie modelos de aplicativos de painel solar gratuitamente: [aplicativo de painel solar](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Saiba mais sobre o IoT Central conferindo a [Visão geral do IoT Central](https://docs.microsoft.com/azure/iot-central/)

