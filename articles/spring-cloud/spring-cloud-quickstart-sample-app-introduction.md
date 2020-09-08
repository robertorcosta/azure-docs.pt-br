---
title: Guia de início rápido – Introdução ao aplicativo de exemplo Piggy Metrics – Azure Spring Cloud
description: Descreve o aplicativo de exemplo Piggy Metrics usado na implantação do Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046775"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Introdução ao aplicativo de exemplo Piggy Metrics

Neste guia de início rápido, usamos um exemplo de finanças pessoais chamado Piggy Metrics para mostrar como implantar um aplicativo no serviço Azure Spring Cloud. O Piggy Metrics demonstra o padrão de arquitetura de microsserviço e realça o detalhamento de serviços. Você verá como ele é implantado no Azure com funcionalidades avançadas do Azure Spring Cloud, incluindo descoberta de serviço, Config Server, logs, métricas e rastreamento distribuído.

Para seguir os exemplos de implantação do Azure Spring Cloud, você precisa apenas da localização do código-fonte, que é fornecido conforme necessário.

## <a name="functional-services"></a>Serviços funcionais
O Piggy Metrics é decomposto em três microsserviços principais. Todos eles são aplicativos implantáveis independentemente organizados por domínios empresariais.

* **Serviço de conta (A ser implantado)** : Contém a validação e a lógica de entrada do usuário gerais: resultados/itens de despesas, economia e configurações de conta.
* **O serviço de estatísticas (Não usado neste guia de início rápido)** : Executa cálculos em parâmetros de estatísticas principais e captura séries temporais para cada conta. O Datapoint contém valores, normalizados para a moeda base e o período de tempo. Esses dados são usados para rastrear a dinâmica do fluxo de caixa no tempo de vida da conta.
* **Serviço de notificação (Não usado neste guia de início rápido)** : Armazena as informações de contato dos usuários e as configurações de notificação, como a frequência de lembrete e de backup. O trabalho agendado coleta informações necessárias de outros serviços e envia mensagens de email para os clientes assinados.

## <a name="infrastructure-services"></a>Serviços de infraestrutura
Há vários padrões comuns em sistemas distribuídos que ajudam a fazer os serviços principais funcionarem. O Azure Spring Cloud fornece ferramentas poderosas que aprimoram o comportamento dos aplicativos Spring Boot para implementar estes padrões: 

* **Serviço de configuração (Hospedado pelo Azure Spring Cloud)** : A Configuração do Azure Spring Cloud é um serviço de configurações centralizado escalável horizontalmente para sistemas distribuídos. Ela usa um repositório conectável que atualmente dá suporte ao armazenamento local, Git e Subversion.
* **Descoberta de serviço (Hospedado pelo Azure Spring Cloud)** : Ela permite a detecção automática de locais de rede para instâncias de serviço, que podem ter endereços atribuídos dinamicamente por causa de dimensionamento automático, falhas e atualizações.
* **Serviço de autenticação (A ser implantado)** : as responsabilidades de autorização são extraídas completamente para um servidor separado, que concede tokens OAuth2 para os serviços de recursos de back-end. O Servidor de Autenticação realiza a autorização do usuário e protege a comunicação entre computadores dentro de um perímetro.
* **Gateway de API (A ser implantado)** : Os três serviços principais expõem uma API externa ao cliente. Em sistemas reais, o número de funções pode crescer muito rapidamente com a complexidade do sistema. Centenas de serviços podem estar envolvidos na renderização de uma página da Web complexa. O Gateway de API é um ponto de entrada único no sistema, usado para lidar com solicitações e roteá-las para o serviço de back-end apropriado ou para invocar vários serviços de back-end, agregando os resultados. 

## <a name="sample-usage-of-piggy-metrics"></a>Exemplo de uso de Piggy Metrics
Para obter detalhes completos de implementação, confira [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). Os exemplos fazem referência ao código-fonte, conforme necessário.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Provisionar a instância do Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
