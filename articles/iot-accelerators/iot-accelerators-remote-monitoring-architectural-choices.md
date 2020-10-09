---
title: Opções de arquitetura da solução de Monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve as opções técnicas e de arquitetura feitas no Monitoramento Remoto
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "61447159"
---
# <a name="remote-monitoring-architectural-choices"></a>Opções de arquitetura de Monitoramento Remoto

O acelerador de solução de Monitoramento Remoto Azure IoT é um código-fonte aberto, MIT licenciado, acelerador de solução. Para ajudar a acelerar o processo de desenvolvimento de IoT, ele mostra os cenários de IoT comuns, como:

- Conectividade do dispositivo
- Gerenciamento de dispositivo
- Processamento de fluxo

A solução de Monitoramento Remoto segue a arquitetura de referência do [Azure IoT recomendada publicada recomendada](https://aka.ms/iotrefarchitecture).

Esse artigo descreve as principais escolhas arquitetônicas e técnicas feitas em cada um dos subsistemas de Monitoramento Remoto. No entanto, as escolhas técnicas da Microsoft feitas na solução de Monitoramento Remoto não são a única forma de implementar uma solução de monitoramento remoto de IoT. Você deve considerar a implementação técnica como uma linha de base para a criação de um aplicativo com êxito e você deve modificá-lo para:

- Ajustar as habilidades disponíveis e experiência em sua organização.
- Atender às necessidades do seu aplicativo vertical.

## <a name="architectural-choices"></a>Opções de arquitetura

A arquitetura que a Microsoft recomenda para um aplicativo IoT é nativa da nuvem, microsserviço e baseada sem servidor. Você deve compilar os diferentes subsistemas de um aplicativo de IoT como serviços discretos que podem ser implantados e a escala de forma independente. Esses atributos permitem maior escala, mais flexibilidade na atualização dos subsistemas individuais e fornecem a flexibilidade de escolher uma tecnologia adequada para cada subsistema.

Você pode implementar microsserviços usando mais de uma tecnologia. Por exemplo, você pode escolher qualquer uma das opções a seguir para implementar um microsserviço:

- Use uma tecnologia de contêiner, como o Docker com a tecnologia sem servidor como o Azure Functions.
- Hospede seus microsserviços nos serviços de PaaS, como Serviços de Aplicativo Azure AD.

## <a name="technology-choices"></a>Opções de tecnologia

Esta seção fornece detalhes sobre as opções de tecnologia feitas na solução de Monitoramento Remoto para cada um dos subsistemas principais.

![Diagrama Principal](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Gateway de Nuvem

O Hub IoT do Azure é usado como o gateway de nuvem de solução de monitoramento remoto. O [Hub IoT](https://azure.microsoft.com/services/iot-hub/) oferece comunicação segura e bidirecional com os dispositivos.

Para conectividade do dispositivo IoT, você pode usar:

- Os [SDKs do dispositivo Hub IoT](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) para implementar um aplicativo cliente nativo para o seu dispositivo. Os SDKs oferecem wrappers em torno da API REST do Hub IoT e lidam com cenários como a repetição.
- Integração com o Azure IoT Edge para implantar e gerenciar os módulos personalizados em execução em contêineres em seus dispositivos.
- Integração com gerenciamento automático de dispositivos no Hub IoT para gerenciar dispositivos conectados em massa.

### <a name="stream-processing"></a>Processamento de fluxo

Para o processamento de fluxo, a solução de Monitoramento Remoto usa o Azure Stream Analytics para o processamento de regras complexas. Para clientes que desejam regras mais simples, há um microsserviço personalizado com suporte para o processamento de regras simples, embora essa configuração não faça parte da implantação imediata. A arquitetura de referência recomenda o Azure Functions para processamento de regra simples e o Azure Stream Analytics para o processamento de regra complexa.

### <a name="storage"></a>Armazenamento

Para armazenamento, o acelerador de solução de Monitoramento Remoto usa o Azure Time Series Insights e o Azure Cosmos DB. O Azure Time Series Insights armazena as mensagens provenientes do Hub IoT a partir de seus dispositivos conectados. O acelerador de solução usa o Azure Cosmos DB para todos os outros armazenamentos, como armazenamento frio, definições de regras, alertas e configurações.

O Azure Cosmos DB é a solução de armazenamento warm de uso geral recomendado para aplicativos de IoT. No entanto, as soluções como o Azure Time Series Insights e o Azure Data Lake são apropriadas para muitos casos de uso. Com o Azure Time Series Insights, você pode obter informações mais detalhadas sobre os dados dos sensores de séries temporais detectando tendências e anomalias. Esse recurso permite realizar análises de causa raiz e evitar períodos de inatividade dispendiosos.

> [!NOTE]
> Os Time Series Insights não estão disponíveis atualmente na nuvem do Azure China. As novas implantações do acelerador de solução de monitoramento remoto na nuvem do Azure China usam o Cosmos DB para todo o armazenamento.

### <a name="business-integration"></a>Integração de negócios

A integração de negócios na solução de Monitoramento Remoto é limitada à geração de alertas, que são colocados no armazenamento warm. Conecte a solução com os Aplicativos Lógicos do Azure para implementar cenários de integração mais profundos de negócios.

### <a name="user-interface"></a>Interface do Usuário

A interface do usuário da Web é criada com JavaScript React. O React oferece uma estrutura de interface do usuário da Web do setor usada com frequência e é semelhante a outras estruturas populares, como o Angular.

### <a name="runtime-and-orchestration"></a>Runtime e orquestração

A solução de Monitoramento Remoto usa contêineres Docker para executar os subsistemas com Kubernetes como o orquestrador para escala horizontal. Essa arquitetura permite definições de escala individuais para cada subsistema. No entanto, essa arquitetura incorre custos de DevOps para manter as máquinas virtuais e contêineres, atualizados e protegidos.

Alternativas ao Docker incluem microsserviços de hospedagem nos serviços PaaS como Serviço de Aplicativo do Azure. Alternativas ao Kubernetes incluem orquestradores como o Service Fabric, DC/SO ou Swarm.

## <a name="next-steps"></a>Próximas etapas

* Implantar a solução de Monitoramento Remoto [aqui](https://www.azureiotsolutions.com/).
* Explore o código do GitHub em [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Saiba mais sobre a Arquitetura de Referência do IoT [aqui](https://aka.ms/iotrefarchitecture).
