---
title: Configurar dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como configurar os dispositivos conectados ao acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e4236952bd41c4955e337813ff6d706263b8ef47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890913"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Tutorial: Configurar dispositivos conectados à sua solução de monitoramento

Neste tutorial, você usará o acelerador da solução de Monitoramento Remoto para identificar e corrigir problemas em dispositivos IoT conectados. Adicione um novo dispositivo ao acelerador de solução e configure-o.

A Contoso solicitou novas máquinas para expandir uma de suas instalações. Enquanto espera a entrega das novas máquinas, você pode executar uma simulação para testar o comportamento da solução. Para executar a simulação, é possível adicionar um novo dispositivo de mecanismo simulado ao acelerador de solução de Monitoramento Remoto e testar se esse dispositivo simulado responde corretamente às atualizações de configuração. Embora este tutorial use dispositivos simulados, um desenvolvedor de dispositivo pode implementar métodos diretos em um [dispositivo real conectado ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Neste tutorial, você:

>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar um dispositivo simulado.
> * Reconfigurar um dispositivo.
> * Organizar seus dispositivos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Navegue até a página **Device Explorer** na solução e clique em **+ Novo dispositivo**:

[![Provisionar um dispositivo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

No painel **Novo dispositivo**, escolha **Simulado**, deixe o número de dispositivos a serem provisionados como **1**, escolha o modelo de dispositivo **Mecanismo Defeituoso** e escolha **Aplicar** para criar o dispositivo simulado:

[![Provisionar um dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para testar se o dispositivo de mecanismo simulado está enviando a telemetria e relatando valores de propriedade, selecione-o na lista de dispositivos na página **Device Explorer**. Informações em tempo real sobre o mecanismo são exibidas no painel **Detalhes do Dispositivo**:

[![Exibir o novo dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Em **Detalhes do Dispositivo**, verifique se o novo dispositivo está enviando telemetria. Para exibir o fluxo de telemetria de vibração do dispositivo, clique em **Vibração**:

[![Selecionar um fluxo de telemetria a ser exibido](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

O painel **Detalhes do Dispositivo** exibe outras informações sobre o dispositivo como valores de marca, os métodos com suporte e as propriedades relatadas pelo dispositivo.

Para exibir um diagnóstico detalhado, role para baixo no painel **Detalhes do dispositivo** para exibir a seção **Diagnóstico**.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para testar se você pode atualizar as propriedades de configuração do mecanismo, selecione-o na lista de dispositivos na página **Device Explorer**. Em seguida, clique em **Trabalhos** e escolha **Propriedades**. O painel Trabalhos mostra os valores de propriedade do dispositivo selecionado que podem ser alterados:

[![Reconfigurar um dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Para atualizar o local do mecanismo, defina o nome do trabalho como **UpdateEngineLocation**, defina a longitude como **-122.15**, defina o local como **Factory 2**, defina a latitude como **47.62** e clique em **Aplicar**:

[![Atualizar um valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Para acompanhar o status do trabalho, clique em **Exibir status do trabalho**:

[![Atualizar um valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Depois que o trabalho for concluído, navegue até a página **Painel**. O dispositivo de mecanismo é exibido no mapa em seu novo local:

[![Exibir local do mecanismo](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizar os dispositivos

Para facilitar seu trabalho como operador de organizar e gerenciar dispositivos, você pode marcá-los com o nome da equipe. A Contoso tem duas equipes diferentes para atividades de serviço de campo:

* A equipe Veículo Inteligente gerencia caminhões e dispositivos de protótipo.
* A equipe Prédio Inteligente gerencia resfriadores, elevadores e mecanismos.

Para exibir seus dispositivos, navegue até a página **Device Explorer** e selecione o filtro **Todos os dispositivos**:

[![Mostrar todos os dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Adicionar marcas

Selecione todos os dispositivos **Caminhões** e **Protótipos**. Em seguida, clique em **Trabalhos**.

No painel **Trabalhos**, selecione **Marca**, defina o nome do trabalho como **AddConnectedVehicleTag** adicione uma marca de texto chamada **FieldService** com um valor **ConnectedVehicle**. Em seguida, clique em **Aplicar**:

[![Adicionar marcas aos dispositivos de protótipo e caminhão](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na página do dispositivo, selecione todos os dispositivos **Resfriador**, **Elevador**, e **Mecanismo**. Em seguida, clique em **Trabalhos**.

No painel **Trabalhos**, selecione **Marca**, defina o nome do trabalho como **AddSmartBuildingTag** e adicione uma marca de texto chamada **FieldService** com um valor **SmartBuilding**. Em seguida, clique em **Aplicar**:

[![Adicionar marca aos dispositivos Resfriador, Elevador e Mecanismo](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Criar filtros

Agora você pode usar os valores da marca para criar filtros. Na página **Device Explorer**, clique em **Gerenciar grupos de dispositivos**:

[![Gerenciar grupos de dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Crie um filtro de texto que use o nome de marca **FieldService** e o valor **SmartBuilding** na condição. Salve o filtro como **Prédio Inteligente**:

[![Criar um filtro de prédio inteligente](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Crie um filtro de texto que use o nome de marca **FieldService** e o valor **ConnectedVehicle** na condição. Salve o filtro como **Veículo Conectado**.

[![Criar um filtro de veículo conectado](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Agora, o operador da Contoso pode consultar dispositivos de acordo com a equipe operacional:

[![Criar um filtro de veículo conectado](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como configurar e gerenciar os dispositivos conectados para o acelerador de solução de Monitoramento Remoto. Para saber como usar o Solution Accelerator para realizar uma análise da causa raiz em um alerta inesperado, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Realizar uma análise da causa-raiz em um alerta](iot-accelerators-remote-monitoring-root-cause-analysis.md)
