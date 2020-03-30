---
title: Perguntas frequentes para o Azure Security Center for IoT| Microsoft Docs
description: Encontre respostas para as perguntas mais frequentes sobre o Azure Security Center para recursos e serviços de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 639a3f89e470a832279add8d2ed7cf49441611f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571793"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Perguntas frequentes sobre a Central de Segurança do Azure para IoT  

Este artigo fornece uma lista de perguntas e respostas frequentes sobre o Azure Security Center for IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure fornece suporte para a segurança de IoT?

O Azure fornece uma visão integrada para monitorar e gerenciar sua segurança de IoT como parte de sua solução de segurança global através do Azure Security Center. Se você é um desenvolvedor de aplicativos, você pode usar a exibição do IoT Hub para gerenciar a segurança do aplicativo IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor única do Azure para a segurança de IoT?

O Azure Security Center for IoT permite que as empresas ampliem sua visão de segurança cibernética existente para toda a sua solução de IoT. O Azure fornece uma visão final da sua solução de negócios, permitindo que você tome ações e decisões relacionadas aos negócios com base na postura de segurança da empresa e nos dados coletados. A segurança combinada usando o Azure IoT, o Azure IoT Edge e o Azure Security Center permitem que você crie a solução desejada com a segurança necessária.

## <a name="who-is-azure-security-center-for-iot-made-for"></a>Para quem é o Centro de Segurança Azure para IoT? 

O Azure Security Center for IoT é integrado dentro do Azure IoT Hub Security e fornece gerenciamento para as operações diárias de segurança de soluções de negócios. O Azure Security Center for IoT também está integrado aos recursos do Azure Security Center e fornece uma visão integrada para monitorar e gerenciar sua segurança de IoT como parte de sua solução de segurança global.

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Como o Azure Security Center para IoT se compara à concorrência?

Enquanto outras soluções fornecem um conjunto de recursos que permitem que os clientes criem suas próprias soluções, o Azure Security Center for IoT fornece uma solução única de segurança de IoT de ponta a ponta que fornece uma visão ampla sobre a segurança de todos os seus Azure relacionados Recursos. O Azure permite uma implantação rápida e integração total com os gêmeos do módulo IoT Hub para facilitar a integração com as ferramentas de gerenciamento de dispositivos existentes.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Eu tenho que ser um cliente do Azure Security Center para usar este serviço?

Não, mas é recomendado. Sem o Azure Security Center, o Azure Security Center for IoT recebe dados limitados de recursos conectados e fornece uma análise limitada de sua superfície de ataque potencial, ameaças e potenciais ataques. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Eu tenho que ser um cliente de IoT do Azure?

Sim. O Azure Security Center for IoT conta com conectividade e infra-estrutura do Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Tenho que instalar um agente?

A instalação do agente em seus dispositivos IoT não é obrigatória para habilitar o Microsoft Azure Security Center para IoT. Você pode escolher entre as três opções a seguir, ganhando diferentes níveis de recursos de monitoramento e gerenciamento de segurança de acordo com sua seleção:

1. Instale o Centro de Segurança Azure para agente de segurança IoT com ou sem modificações. Essa opção fornece o mais alto nível de insights aprimorados de segurança sobre o comportamento e o acesso do dispositivo.

2. Crie seu próprio agente e implemente o Microsoft Azure Security Center para esquema de mensagem de segurança IoT. Essa opção permite o uso do Microsoft Azure Security Center para ferramentas de análise de IoT em cima do agente de segurança do dispositivo.

3. Não há instalação de agentes de segurança em seus dispositivos IoT. Essa opção permite o monitoramento de comunicação do IoT Hub, com recursos reduzidos de monitoramento e gerenciamento de segurança. 

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>O que o Centro de Segurança Azure para agente ioT faz?

O Azure Security Center para agente IoT fornece cobertura de ameaça de nível de dispositivo para configuração, comportamento e acesso do dispositivo (digitalizando a configuração), & conectividade. O Azure Security Center para agente de segurança IoT não verifica dados ou atividades relacionadas aos negócios.

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>Onde posso conseguir o Centro de Segurança Azure para agente de segurança IoT?

O Azure Security Center para agente de segurança IoT é de código aberto e está disponível no GitHub em versões de 32 bits e 64 bits para Windows e Linux: https://github.com/Azure/Azure-Security-IoT.

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>Onde o Azure Security Center para agente IoT é instalado? 

Informações detalhadas sobre instalação e implantação de https://github.com/Azure/Azure-IoT-Securityagentes podem ser encontradas no GitHub: .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e pré-requisitos do agente?

O Azure Security Center for IoT suporta uma grande variedade de plataformas. Consulte [plataformas de dispositivos suportados](how-to-deploy-agent.md) para verificar o suporte para seus dispositivos específicos. 

## <a name="which-data-is-collected-by-the-agent"></a>Quais dados são coletados pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos & linha de base do SO são coletadas pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Quantos dados o agente vai gerar?

A geração de dados do agente é impulsionada pela configuração do dispositivo, aplicativo, tipo de conectividade e agente do cliente. Devido à alta variabilidade entre dispositivos e soluções de IoT, recomendamos primeiro implantar o agente em um laboratório ou configuração de teste para observar, aprender e definir a configuração específica que se ajusta às suas necessidades, ao mesmo tempo em que mensura a quantidade de dados gerados. Após iniciar o serviço, o Azure Security Center para agente IoT fornece recomendações operacionais para otimizar o throughput do agente para ajudá-lo no processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como posso controlar meu faturamento?

O Azure Security Center for IoT fornece varreduras de agentes configuráveis, buffers de dados e a capacidade de criar alertas personalizados que aumentam ou reduzem a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens de agentes usam a cota do IoT Hub?

Sim. Os dados transmitidos pelo agente são contados na sua cota do IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Próximos passos Instalei um agente e não vejo atividades ou registros...

1. Verifique se o [tipo de agente se encaixa na plataforma de Sistema Operacional designada do seu dispositivo](how-to-deploy-agent.md)

1. Confirme se o [agente está sendo executado no dispositivo.](how-to-agent-configuration.md)

2. Verifique se o [serviço foi habilitado com sucesso](quickstart-onboard-iot-hub.md) para a **segurança** em seu Hub IoT. 

3. Verifique se o dispositivo está [configurado no IoT Hub com o módulo Azure Security Center for IoT](quickstart-create-security-twin.md).  

Se as atividades ou logs ainda estiverem indisponíveis, entre em contato com o parceiro do Azure Security Center para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão com a internet parar de funcionar?

O agente continua a executar e armazenar dados enquanto o dispositivo estiver em execução. Os dados são armazenados no cache da mensagem de segurança de acordo com a configuração do tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança voltam a ser enviados. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo for reiniciado, o agente de segurança se recuperará?

O agente de segurança foi projetado para ser reexecutado automaticamente a cada reinicialização do dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos de máquina como qualquer outro aplicativo/processo e não deve interromper a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente é executado está associado à sua configuração e configuração. Recomendamos testar a configuração do seu agente em um ambiente contido, juntamente com a interoperabilidade com seus outros aplicativos e funcionalidades de IoT, antes de tentar implantar em um ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou fazendo manutenção no dispositivo. Posso desligar o agente?

O agente não pode ser desligado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existe uma maneira de testar se o agente está trabalhando corretamente? 

Se o agente parar de se comunicar ou não enviar mensagens de segurança, um dispositivo será gerado alerta **silencioso.**

## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprios alertas?

Sim. Você pode definir um alerta personalizado em um conjunto pré-determinado de comportamentos, como endereço IP e portas abertas. Consulte [Criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como fazê-los. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver registros? Posso personalizar logs?

- Exibir alertas e recomendações usando seu espaço de trabalho log analytics conectado. Configure o tamanho e a duração do armazenamento no espaço de trabalho.

- Os dados brutos do seu agente de segurança também podem ser armazenados em sua conta do Log Analytics. Considere tamanho, duração, requisitos de armazenamento e custos associados antes de alterar a configuração desta opção. 

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar o Azure Security Center para IoT à identidade do módulo? Para que é utilizado?

O módulo Azure Security Center for IoT é usado para a configuração e o gerenciamento do agente.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar com o Azure Security Center for IoT, consulte os seguintes artigos:


- Leia o Centro de Segurança do Azure para [visão geral de](overview.md) IoT
- Verifique os [pré-requisitos](service-prerequisites.md) do Serviço
- Saiba mais sobre como [começar](getting-started.md)
- Entenda [o Azure Security Center para alertas de segurança de IoT](concept-security-alerts.md)

