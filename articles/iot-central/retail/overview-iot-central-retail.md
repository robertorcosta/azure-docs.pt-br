---
title: Como criar soluções de varejo com o Azure IoT Central | Microsoft Docs
description: Aprenda sobre o uso de modelos de aplicativo do Azure IoT Central para criar logística conectada, centro de distribuição digital, monitoramento de condição de análise no repositório, finalização de compra, gerenciamento de estoque inteligente e soluções de varejo.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 9427b007bc047e2f01db2dad02e06bf0ab0f5dea
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "81001375"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Como criar soluções de varejo com o Azure IoT Central



O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo associado ao desenvolvimento, ao gerenciamento e à manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar seu tempo, dinheiro e energia na transformação de seus negócios com os dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em evolução.

Este artigo descreve vários modelos de aplicativo do IoT Central específicos para o varejo. Como um criador de solução, você pode usar esses modelos para criar soluções de IoT que otimizem a cadeia de suprimentos, aprimorem a experiência na loja para os clientes e acompanhem o estoque com mais eficiência.

> [!div class="mx-imgBorder"]
> ![Visão geral de varejo do Azure IoT](./media/overview-iot-central-retail/retail-app-templates.png)

As seções a seguir descrevem as funcionalidades desses modelos de aplicativo:

## <a name="connected-logistics"></a>Logística conectada

Espera-se que gastos globais com logística alcance US$ 10,6 trilhões em 2020. O transporte de bens representa a maior parte desses gastos e os provedores de serviços de transporte estão sob intensas restrições e pressão competitiva.

Você pode usar sensores de IoT para coletar e monitorar condições do ambiente, como temperatura, umidade, inclinação, impacto, luz e a localização de uma remessa. Você pode combinar a telemetria obtida de sensores e dispositivos IoT com outras fontes de dados, como informações sobre clima e tráfego em sistemas de business intelligence baseados em nuvem.

Os benefícios de uma solução de logística conectada incluem:

* Monitoramento de remessa com rastreamento e acompanhamento em tempo real. 
* Integridade da remessa com monitoramento em tempo real das condições do ambiente.
* Segurança contra roubo, perda ou danos das remessas.
* Isolamento geográfico, otimização de rota, gerenciamento de frota e análise de veículo.
* Previsão para horários previsíveis de saída e chegada das remessas.

As capturas de tela a seguir mostram o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![Painel de Logística Conectada](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Painel de Logística Conectada](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Para saber mais, confira o tutorial [Implantar e usar um modelo de aplicativo de logística conectado](./tutorial-iot-central-connected-logistics.md).

## <a name="digital-distribution-center"></a>Centro de distribuição digital

À medida que fabricantes e varejistas estabelecem presença em todo o mundo, as respectivas cadeias de suprimento ramificam-se e tornam-se mais complexas do que nunca. Os consumidores agora esperam que grandes seleções de produtos estejam disponíveis e que essas mercadorias cheguem dentro de um ou dois dias após a compra. Os centros de distribuição devem se adaptar a essas tendências enquanto superam as ineficiências existentes. 

Hoje, uma dependência do trabalho manual significa que a separação e o empacotamento respondem por 55-65% dos custos do centro de distribuição. A separação e o empacotamento manuais também são normalmente mais lentos do que os sistemas automatizados e a flutuação rápida das necessidades de pessoal torna ainda mais difícil atender aos volumes de envio. Essa flutuação sazonal resulta em uma grande rotatividade da equipe e aumenta a probabilidade de erros onerosos.

Soluções baseadas em câmeras habilitadas por IoT podem proporcionar benefícios transformadores ao habilitar um loop de comentários digitais. Os dados do centro de distribuição geram informações úteis que, por sua vez, resultam em dados melhores.

Os benefícios de um centro de distribuição digital incluem:

* As câmeras monitoram as mercadorias à medida que elas chegam e passam pelo sistema transportador.
* Identificação automática de bens com falha.
* Acompanhamento eficiente de pedidos.
* Custos reduzidos, produtividade aprimorada e uso otimizado.

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![Dashboard do Centro de Distribuição Digital](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Para saber mais, confira o tutorial [Implantar e seguir passo a passo o modelo de aplicativo da central de distribuição digital](./tutorial-iot-central-digital-distribution-center.md).

## <a name="in-store-analytics---condition-monitoring"></a>Análise na loja – Monitoramento de condição

Para muitos varejistas, as condições ambientais em suas lojas são um importante diferencial em relação aos seus concorrentes. Os varejistas querem manter condições agradáveis em suas lojas, para o benefício de seus clientes.  

Como um criador de soluções, você pode usar o modelo de aplicativo de monitoramento da condição de análise na loja no IoT Central para criar uma solução de ponta a ponta. O modelo de aplicativo permite que você se conecte digitalmente a um ambiente de loja de varejo e o monitore usando diferentes tipos de dispositivos de sensor. Esses dispositivos de sensor geram telemetria que você pode converter em informações de negócios, que por sua vez ajudam os varejistas a reduzir os custos operacionais e a criar uma ótima experiência para os respectivos clientes.

Use o modelo de aplicativo para:

* Conectar uma variedade de sensores de IoT a uma instância de aplicativo do IoT Central.
* Monitore e gerencie a integridade da rede do sensor, bem como quaisquer eventuais dispositivos de gateway no ambiente.
* Criar regras personalizadas em torno das condições ambientais em uma loja para disparar alertas para gerentes de loja.
* Transforme as condições do ambiente em sua loja em insights que podem ser aproveitados pela equipe da loja de varejo para aprimorar a experiência do cliente.
* Exporte os insights agregados para aplicativos de negócios novos ou existentes para fornecer informações úteis e oportunas à equipe de varejo.

O modelo de aplicativo é fornecido com um conjunto de modelos de dispositivo e usa um conjunto de dispositivos simulados para popular o painel. 

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![Monitoramento de condição de análise na loja](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Para saber mais, confira o tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

## <a name="in-store-analytics---checkout"></a>Análise na loja – finalizar compra

Para alguns varejistas, a experiência de finalização de compra em suas lojas é um importante diferencial em relação aos seus concorrentes. Os varejistas desejam fornecer uma experiência de finalização de compra tranquila em suas lojas para incentivar os clientes a retornarem.  

Como um criador de soluções, você pode usar o modelo de aplicativo de finalização de compra de análise na loja do IoT Central para criar uma solução que forneça informações sobre a área próxima à zona de finalização de compra de uma loja para a equipe de varejo. Por exemplo, os sensores podem fornecer informações sobre comprimentos de fila e tempos de espera médios para cada fileira de finalização de compra.

Use o modelo de aplicativo para:

* Conectar uma variedade de sensores de IoT a uma instância de aplicativo do IoT Central.
* Monitore e gerencie a integridade da rede do sensor, bem como quaisquer eventuais dispositivos de gateway no ambiente.
* Criar regras personalizadas em torno das condições de finalização de compra em uma loja para disparar alertas para o pessoal da loja de varejo.
* Transforme as condições de finalização de compra na loja em insights que podem ser aproveitados pela equipe da loja de varejo para aprimorar a experiência do cliente.
* Exporte os insights agregados para aplicativos de negócios novos ou existentes para fornecer informações úteis e oportunas à equipe de varejo.

O modelo de aplicativo é fornecido com um conjunto de modelos de dispositivo e usa um conjunto de dispositivos simulados para popular o painel com os dados de ocupação de fileira. 

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![Finalização de compra de análise na loja](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Para saber mais, confira o tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

## <a name="smart-inventory-management"></a>Gerenciamento de estoque inteligente

"Estoque" é o estoque de mercadorias mantidas por um varejista. O gerenciamento de estoque é crítico para garantir que o produto certo esteja no lugar certo no momento certo. Um varejista deve equilibrar os custos de armazenar um estoque grande demais com os custos de não ter itens suficientes em estoque para atender à demanda.

Os dados de IoT gerados de avisos, câmeras e marcas de RFID (identificação de frequência de rádio) fornecem oportunidades para aprimorar os processos de gerenciamento de estoque. Você pode combinar a telemetria obtida de sensores e dispositivos IoT com outras fontes de dados, como informações sobre clima e tráfego em sistemas de business intelligence baseados em nuvem.

Os benefícios do gerenciamento inteligente de estoque incluem:

* Reduzir o risco de que o estoque dos itens se esgote e garantir o nível desejado de atendimento ao cliente. 
* Análise detalhada e insights sobre a precisão do inventário quase em tempo real.
* Ferramentas para ajudar a decidir a quantidade certa de estoque suficiente para atender aos pedidos dos clientes.

Esse modelo de aplicativo se concentra na conectividade do dispositivo e na configuração e gerenciamento de dispositivos que leem BLE (Bluetooth de baixa energia) e RFID.

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![Painel de Gerenciamento de Estoque Inteligente](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Para saber mais, confira o tutorial [Implantar e usar um modelo de aplicativo de gerenciamento de estoque inteligente](./tutorial-iot-central-smart-inventory-management.md).

## <a name="micro-fulfillment-center"></a>Microcentral de atendimento

No cenário de varejo, cada vez mais competitivo, os varejistas enfrentam constantemente a pressão para fechar a lacuna entre a demanda e o atendimento. Uma nova tendência que surgiu para lidar com a demanda crescente do consumidor é colocar o estoque próximo aos clientes finais e às lojas que eles visitam.

O modelo de aplicativo da microcentral de atendimento do IoT Central permite que os integradores de solução monitorem e gerenciem todos os aspectos de suas centrais de atendimento totalmente automatizadas. O modelo inclui um conjunto de sensores de monitoramento de condições simuladas e operadoras robóticas para acelerar o processo de desenvolvimento da solução. Esses dispositivos de sensor capturam sinais úteis que podem ser convertidos em informações de negócios, permitindo que os varejistas reduzam os custos operacionais e criem experiências para os próprios clientes.

O modelo de aplicativo permite que você: 

- Conecte diretamente diferentes tipos de sensores de IoT, como robôs ou sensores de monitoramento de condições, a uma instância do aplicativo IoT Central.
- Monitore e gerencie a integridade da rede do sensor e os dispositivos de gateway no ambiente.
- Criar regras personalizadas em torno das condições ambientais em uma central de atendimento para disparar alertas apropriados.
- Transforme as condições do ambiente em sua central de atendimento informações que podem ser aproveitadas pela equipe da loja de varejo.
- Exporte os insights agregados para aplicativos de negócios novos ou existentes para o benefícios dos membros da equipe de varejo.

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![Microcentral de atendimento](./media/overview-iot-central-retail/MFC-Dashboard.png)

Para saber mais, confira o tutorial [Implantar e seguir passo a passo o modelo de aplicativo da microcentral de atendimento](./tutorial-micro-fulfillment-center.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a criar uma solução de varejo:

* Introdução ao tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md), que explica passo a passo como criar uma solução usando um dos modelos de aplicativo de análise na loja.
* [Implantar e usar um modelo de aplicativo de logística conectada](./tutorial-iot-central-connected-logistics.md).
* [Implantar e usar um modelo de aplicativo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center.md).
* [Implantar e usar um modelo de aplicativo de gerenciamento de estoque inteligente](./tutorial-iot-central-smart-inventory-management.md).
* [Implantar e seguir passo a passo o modelo de aplicativo da microcentral de atendimento](./tutorial-micro-fulfillment-center.md).
* Saiba mais sobre o IoT Central na [Visão geral do IoT Central](../preview/overview-iot-central.md).
