---
title: Entendendo o Azure Security Center para agregação de eventos ioT| Microsoft Docs
description: Saiba mais sobre o Azure Security Center para agregação de eventos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928965"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Agregação do Azure Security Center for IoT

O Azure Security Center para agentes de segurança de IoT coleta dados e eventos do sistema do seu dispositivo local e envia esses dados para a nuvem do Azure para processamento e análise. O agente de segurança coleta muitos tipos de eventos de dispositivos, incluindo novos processos e novos eventos de conexão. Tanto o novo processo quanto novos eventos de conexão podem ocorrer legitimamente com freqüência em um dispositivo dentro de um segundo, e embora importante para uma segurança robusta e abrangente, o número de mensagens que os agentes de segurança são forçados a enviar podem chegar rapidamente ou exceder seu Hub IoT limites de cota e custos. No entanto, esses eventos contêm informações de segurança altamente valiosas que são cruciais para proteger seu dispositivo.

Para reduzir a cota adicional e os custos, mantendo seus dispositivos protegidos, o Azure Security Center para Agentes de IoT agrega esses tipos de eventos.

A agregação de eventos está **ligada** por padrão e, embora não seja recomendada, pode ser **desligada** manualmente a qualquer momento.

A agregação está disponível atualmente para os seguintes tipos de eventos:
* ProcessCreate
* ConexãoCriar
* ProcessTerminate (somente Windows)

## <a name="how-does-event-aggregation-work"></a>Como funciona a agregação de eventos?
Quando a agregação de eventos é deixada **em ,** o Azure Security Center para agentes de IoT agregam eventos para o período de intervalo ou janela de tempo.
Uma vez passado o período de intervalo, o agente envia os eventos agregados para a nuvem do Azure para análise suplementar.
Os eventos agregados são armazenados na memória até serem enviados para a nuvem do Azure.

Para reduzir a pegada de memória do agente, sempre que o agente coleta um evento idêntico ao que já está sendo mantido na memória, o agente aumenta a contagem de acertos deste evento específico. Quando a janela de tempo de agregação passa, o agente envia a contagem de acerto de cada tipo específico de evento que ocorreu. A gregação de eventos é simplesmente a agregação das contagens de hits de cada tipo de evento coletado.

Os eventos são considerados idênticos somente quando as seguintes condições são atendidas: 

* ProcessCrie eventos - quando **commandLine**, **executável,** **username**e **userid** são idênticos
* ConexãoCrie eventos - quando **commandLine**, **userId**, **direção,** **endereço local,** **endereço remoto,****protocolo e **porta de destino** são idênticos
* ProcessTerminate eventos - quando o status **executável** e **de saída** são idênticos

### <a name="working-with-aggregated-events"></a>Trabalhando com eventos agregados

Durante a agregação, as propriedades do evento que não são agregadas são descartadas e aparecem em análises de log com um valor de 0. 
* ProcessCreate events - **processId**e **parentProcessId** são definidos como 0
* ConexãoCrie eventos - **processId**e **porta de origem** são definidos como 0

## <a name="event-aggregation-based-alerts"></a>Alertas baseados em agregação de eventos 
Após a análise, o Azure Security Center for IoT cria alertas de segurança para eventos agregados suspeitos. Os alertas criados a partir de eventos agregados aparecem apenas uma vez para cada evento agregado.

O tempo de início da agregação, o tempo de término e a contagem de acessações para cada evento são registrados no campo **Desportos** extras no Log Analytics para uso durante as investigações. 

Cada evento agregado representa um período de 24 horas de alertas coletados. Usando o menu de opções de eventos no canto superior esquerdo de cada evento, você pode **descartar** cada evento agregado individual.    

## <a name="event-aggregation-twin-configuration"></a>Configuração dupla de agregação de eventos
Faça alterações na configuração do Azure Security Center para agregação de eventos IoT dentro do objeto de [configuração](how-to-agent-configuration.md) do agente da identidade dupla do módulo **de segurança azureiotsecurity.**

| Nome da configuração | Valores possíveis | Detalhes | Comentários |
|:-----------|:---------------|:--------|:--------|
| agregaçãoEnabledProcessCreate | booleano | Habilitar/ desativar a agregação de eventos para criar eventos de criação de processos |
| agregaçãoIntervalProcessCriar | Seqüência ISO8601 Timespan | Intervalo de agregação para eventos de criação de processos |
| agregaçãoEnabledConnectionCreate | booleano| Habilitar / desativar a agregação de eventos para criar eventos de conexão |
| agregaçãoIntervalConnectionCriar | Seqüência ISO8601 Timespan | Intervalo de agregação para eventos de criação de conexão |
| agregaçãoEnabledProcessTerminate | booleano | Habilitar/ desativar a agregação de eventos para eventos de término de processo | Somente Windows|
| agregaçãoIntervalProcessTerminate | Seqüência ISO8601 Timespan | Intervalo de agregação para eventos de término de processo | Somente Windows|
|

## <a name="default-configurations-settings"></a>Configurações padrão configurações

| Nome da configuração | Valores padrão |
|:-----------|:---------------|
| agregaçãoEnabledProcessCreate | true |
| agregaçãoIntervalProcessCriar | "PT1H"|
| agregaçãoEnabledConnectionCreate | true |
| agregaçãoIntervalConnectionCriar | "PT1H"|
| agregaçãoEnabledProcessTerminate | true |
| agregaçãoIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Security Center para agregação de agentes de segurança IoT e as opções de configuração de eventos disponíveis.

Para continuar começando com o Azure Security Center para implantação de IoT, use os seguintes artigos:

- Entenda [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](how-to-deploy-agent.md)
- Revise o Azure Security Center para [pré-requisitos de serviço](service-prerequisites.md) de IoT
- Saiba como [ativar o Azure Security Center para serviço sitopo em seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)
