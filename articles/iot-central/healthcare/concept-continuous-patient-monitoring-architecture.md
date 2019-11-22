---
title: Arquitetura contínua de monitoramento de pacientes no Azure IoT Central | Microsoft Docs
description: Saiba mais sobre a arquitetura de uma solução de monitoramento contínuo de pacientes.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027485"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura do monitoramento contínuo do paciente

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Soluções de monitoramento contínuo de pacientes podem ser criadas aproveitando o modelo de aplicativo fornecido e usando a arquitetura descrita abaixo como orientação.

>[!div class="mx-imgBorder"] 
>![Arquitetura de CPM](media/cpm-architecture.png)

1. Dispositivos médicos com comunicação via BLE (Bluetooth de baixa energia)
1. Gateway de celular que recebe dados do BLE e os envia para IoT Central
1. Exportação contínua dos dados de saúde do paciente para a API do Azure para FHIR&reg;
1. Aprendizado de máquina baseado em dados interoperáveis
1. Painel da equipe de atendimento criada com base nos dados de FHIR

## <a name="details"></a>Detalhes
Esta seção descreve cada parte do diagrama da arquitetura com mais detalhes.

### <a name="ble-medical-devices"></a>Dispositivos médicos BLE
Muitos dispositivos médicos vestíveis usados no espaço de IoT da área da saúde são dispositivos Bluetooth de baixa energia, o que significa que eles não podem se comunicar diretamente com a nuvem e precisam passar por um gateway. Essa arquitetura sugere o uso de um aplicativo de celular como esse gateway.

### <a name="mobile-phone-gateway"></a>Gateway de celular
A função principal do aplicativo de celular é ingerir os dados de BLE dos dispositivos médicos e transmiti-los para o Azure IoT Central. Além disso, o aplicativo pode ajudar a orientar os pacientes por meio de um fluxo de configuração e provisionamento do dispositivo e pode ajudá-los a ter uma visão de seus dados de saúde pessoal. Outras soluções poderão usar um gateway de tablet ou um gateway estático se estiverem dentro de um quarto de hospital para atingir o mesmo fluxo de comunicação.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportar para a API do Azure para FHIR&reg;
O Azure IoT Central é compatível com o HIPAA e tem a certificação HITRUST&reg;, mas você também pode querer enviar dados relacionados à saúde do paciente à API do Azure para FHIR. A [API do Azure para FHIR](../../healthcare-apis/overview.md) é uma API compatível, totalmente gerenciada e baseada em padrões para dados de serviços de saúde clínica que habilita a criação de sistemas de engajamento com seus dados de saúde. Ela possibilita a troca rápida de dados por meio das APIs de FHIR e tem suporte de uma oferta de PaaS (plataforma como serviço) gerenciada na nuvem. Usando a funcionalidade de exportação de dados contínua do IoT Central, você pode enviar dados para a API do Azure para FHIR.

### <a name="machine-learning"></a>Aprendizado de máquina
Depois de agregar seus dados e convertê-los para o formato FHIR, você pode criar modelos de machine learning que podem enriquecer insights e permitir a tomada de decisões mais inteligente para sua equipe de atendimento. Uma variedade de serviços podem ser usados para criar, treinar e implantar modelos de machine learning. Mais informações sobre como usar as ofertas de aprendizado de máquina do Azure podem ser encontradas em nossa [documentação sobre aprendizado de máquina](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Dashboard do provedor
Os dados localizados na API do Azure para FHIR podem ser usados para criar um dashboard de insights do paciente ou podem ser integrados diretamente a um EMR para ajudar as equipes de atendimento a visualizarem o status do paciente. As equipes podem usar esse dashboard para tomar medidas de atendimento para pacientes que precisam de assistência e para identificar sinais de alerta de piora de forma antecipada. Para saber como criar um dashboard do provedor no Power BI em tempo real, siga nosso [guia de instruções](howto-health-data-triage.md).

## <a name="next-steps"></a>Próximas etapas
* [Saiba como implantar um modelo de aplicativo de monitoramento contínuo de pacientes](tutorial-continuous-patient-monitoring.md)