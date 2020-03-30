---
title: Gerar mapa de calor da umidade do solo
description: Descreve como gerar o mapa de calor da umidade do solo no Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482563"
---
# <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor da umidade do solo

A umidade do solo é a água que é mantida nos espaços entre partículas do solo.O mapa de calor da umidade do solo ajuda você a entender os dados de umidade em qualquer profundidade e em alta resolução dentro de suas fazendas. Para gerar um mapa de calor preciso e utilizável para a umidade do solo, é necessária uma implantação uniforme de sensores do mesmo provedor. Diferentes provedores terão diferenças na forma como a umidade do solo é medida, juntamente com diferenças na calibração. O Mapa de Calor é gerado para uma profundidade específica usando os sensores implantados a essa profundidade.

Este artigo descreve o processo de geração de um mapa de calor de umidade do solo para sua fazenda, usando o Acelerador Azure FarmBeats. Neste artigo, você aprenderá a:

- [Criar fazendas](#create-a-farm)
- [Atribuir sensores às Fazendas](#get-soil-moisture-sensor-data-from-partner)
- [Gerar mapa de calor da umidade do solo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Antes de começar

Verifique o seguinte:  

- Uma assinatura do Azure.
- Uma instância em execução do Azure FarmBeats.
- No mínimo, três sensores de umidade do solo estão disponíveis para a fazenda.

## <a name="create-a-farm"></a>Criar um farm

Uma fazenda é uma área geográfica de interesse para a qual você deseja criar um mapa de calor de umidade do solo. Você pode criar uma fazenda usando a [API Farms](https://aka.ms/FarmBeatsDatahubSwagger) ou na [FarmBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Implantar sensores

Você deve implantar fisicamente sensores de umidade do solo na fazenda. Você pode comprar sensores de umidade do solo de qualquer um de nossos parceiros aprovados - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). Você deve coordenar com o seu provedor de sensores para fazer a configuração física em sua fazenda.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Obtenha dados do sensor de umidade do solo do parceiro

À medida que os sensores começam a transmitir, os dados no painel de dados do parceiro, eles habilitam os dados para o Azure FarmBeats. Isso pode ser feito a partir da aplicação do parceiro.

Por exemplo, se você adquiriu sensores Davis, você entrará em sua conta de link meteorológico e fornecerá as credenciais necessárias para habilitar o fluxo de dados para o Azure FarmBeats. Para obter as credenciais necessárias, siga as instruções de [Obter dados do sensor](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Depois de inserir suas credenciais e selecionar **Enviar** no aplicativo parceiro, você pode ter os dados fluindo para o Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Atribua sensores de umidade do solo à fazenda

Uma vez que você tenha ligado sua conta de sensores no Azure FarmBeats, você precisa atribuir os sensores de umidade do solo à fazenda de interesse.

1.  Na página inicial, selecione **Fazendas** no menu, a página da lista **Fazendas** é exibida.
2.  Selecione dispositivos de adicionar **MyFarm** > **Add Devices**.
3.  A **janela Adicionar dispositivos** é exibida. Selecione qualquer dispositivo que esteja ligado aos sensores de umidade do solo para sua fazenda.

    ![Projeto do FarmBeats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar dispositivos**.     

## <a name="generate-soil-moisture-heatmap"></a>Gerar mapa de calor da umidade do solo

Este passo é criar um trabalho ou uma operação de longa duração que irá gerar mapa de calor de umidade do solo para sua fazenda.

1.  Na página inicial, vá para **Fazendas** a partir do menu de navegação à esquerda para ver a página fazendas.
2.  Selecione **MyFarm**.
3.  Na página **Detalhes** da fazenda, selecione **Gerar mapa de precisão**.
4.  No menu suspenso, selecione **Umidade do Solo**.
5.  Na janela umidade do **solo,** selecione **Esta Semana**.
6.  Na **medida do** medidor de umidade do solo **select**, digite a medida que deseja usar para o mapa.
    Para encontrar a medida do sensor, em **Sensores,** selecione qualquer sensor de umidade do solo. Em **Propriedades do sensor,** use o valor **'Nomear medida'.**

    ![Projeto do FarmBeats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecione **Gerar mapas**.
    Uma mensagem de confirmação com detalhes do trabalho é exibida. Para obter mais informações, consulte Job Status in Jobs.

    >[!NOTE]
    > O trabalho leva em torno de três a quatro horas para ser concluído.

### <a name="download-the-soil-moisture-heatmap"></a>Baixe o mapa de calor da umidade do solo

Use as seguintes etapas:

1. Na página **Empregos,** verifique o Status do **Trabalho** para o trabalho que você criou no último procedimento.
2. Quando o status do trabalho for **executado, selecione** **Mapas** no menu.
3. Procure o mapa no dia em que foi criado no formato <> de solo-moisture_MyFarm_YYYY-MM-DD.
4. Selecione um mapa na coluna **Nome,** uma janela pop-up é exibida com a visualização do mapa selecionado.
5. Selecione **Baixar**. O mapa é baixado e armazenado na pasta local do seu computador.

    ![Projeto do FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você gerou com sucesso um mapa de calor da umidade do solo, aprenda a [gerar posicionamento de sensores](generate-maps-in-azure-farmbeats.md#sensor-placement-map) e [ingerir dados históricos de telemetria](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
