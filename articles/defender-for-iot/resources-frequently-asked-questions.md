---
title: Perguntas frequentes sobre o defender for IoT
description: Encontre respostas para as perguntas mais frequentes sobre os recursos e o serviço do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933627"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure defender para IoT

Este artigo fornece uma lista de perguntas frequentes e respostas sobre o defender para IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure dá suporte à segurança da IoT?

O Azure fornece uma visão integrada para monitorar e gerenciar a segurança da IoT como parte de sua solução de segurança geral por meio da Central de Segurança do Azure. Se você for um desenvolvedor de aplicativos, poderá usar a exibição do Hub IoT para gerenciar a segurança do seu aplicativo IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor exclusivo do Azure para segurança da IoT?

O defender para IoT permite que as empresas estendam sua exibição de segurança de ciber existente para toda a sua solução de IoT. O Azure fornece uma exibição de ponta a ponta de sua solução de negócios, permitindo que você tome ações e decisões relacionadas a negócios com base na sua postura de segurança corporativa e nos dados coletados. A segurança combinada usando o Azure IoT, o Azure IoT Edge e a Central de Segurança do Azure permitem que você crie a solução desejada com a segurança de que você precisa.

## <a name="who-is-defender-for-iot-made-for"></a>Para quem é o defender for IoT?

O defender para IoT é integrado à segurança do Hub IoT do Azure e fornece gerenciamento para as operações de segurança do dia a dia da solução de negócios. O defender para IoT também está integrado aos recursos da central de segurança do Azure e fornece uma visão integrada para monitorar e gerenciar a segurança de IoT como parte de sua solução de segurança geral.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Como o defender for IoT se compara à competição?

Enquanto outras soluções fornecem um conjunto de recursos que permitem que os clientes criem suas próprias soluções, o defender for IoT fornece uma solução de segurança de IoT de ponta a ponta exclusiva que fornece uma visão ampla da segurança de todos os seus recursos do Azure relacionados. O Azure habilita a implantação rápida e a integração total com os módulos gêmeos do Hub IoT para integração fácil com as ferramentas de gerenciamento de dispositivos existentes.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>É necessário ser um cliente da Central de Segurança do Azure para usar esse serviço?

Não, mas é recomendado. Sem a central de segurança do Azure, o defender para IoT recebe dados de recursos conectados limitados e fornece uma análise limitada de sua superfície de ataque potencial, ameaças e possíveis ataques.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>É necessário ser um cliente do Azure IoT?

Sim. O defender para IoT depende da conectividade e da infraestrutura do Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>É necessário instalar um agente?

A instalação do agente em seus dispositivos IoT não é obrigatória para habilitar o defender para IoT. Você pode escolher entre as seguintes três opções, cada uma das quais fornecerá diferentes níveis de funcionalidades de monitoramento e gerenciamento de segurança:

1. Instale o agente de segurança do defender para IoT com ou sem modificações. Essa opção fornece o nível mais alto de informações de segurança aprimoradas sobre o comportamento do dispositivo e o acesso a ele.

1. Crie seu próprio agente e implemente o esquema de mensagem do defender for IoT Security. Essa opção habilita o uso do defender para ferramentas de análise de IoT sobre o agente de segurança do dispositivo.

1. Nenhuma instalação do agente de segurança em seus dispositivos IoT. Essa opção habilita o monitoramento de comunicação do Hub IoT, com funcionalidades de monitoramento e gerenciamento de segurança reduzidas.

## <a name="what-does-the-defender-for-iot-agent-do"></a>O que o agente do defender para IoT faz?

O agente do defender para IoT fornece cobertura de ameaça no nível do dispositivo para configuração, comportamento e acesso do dispositivo (examinando a configuração), processa & conectividade. O agente de segurança do defender para IoT não verifica dados ou atividades relacionados ao negócio.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Onde posso obter o agente de segurança do defender para IoT?

O agente de segurança do defender para IoT é de software livre e está disponível no GitHub nas versões do Windows e do Linux de 32 bits e 64 bits: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Onde o agente do defender para IoT é instalado?

Informações detalhadas sobre a instalação e a implantação do agente podem ser encontradas no GitHub: https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e os pré-requisitos do agente?

O defender para IoT dá suporte a uma ampla variedade de plataformas. Confira [Plataformas de dispositivo compatíveis](how-to-deploy-agent.md) para verificar a compatibilidade com seus dispositivos específicos.

## <a name="which-data-is-collected-by-the-agent"></a>Quais dados são coletados pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos e linha de base do sistema operacional são coletados pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Qual o volume de dados que o agente vai gerar?

A geração de dados pelo agente é orientada pelo dispositivo, aplicativo, tipo de conectividade e configuração do agente do cliente. Devido à alta variabilidade entre dispositivos e soluções de IoT, recomendamos primeiro implantar o agente em um laboratório ou configuração de teste para observar, aprender e definir a configuração específica que atende às suas necessidades, medindo simultaneamente a quantidade de dados gerados. Depois de iniciar o serviço, o agente do defender para IoT fornece recomendações operacionais para otimizar a taxa de transferência do agente para ajudá-lo com o processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como posso controlar minha cobrança?

O defender para IoT fornece verificações de agente configuráveis, buffers de dados e a capacidade de criar alertas personalizados que aumentam ou reduzem a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens do agente usam a cota do Hub IoT?

Sim. Os dados transmitidos pelo agente são contabilizados em sua cota do Hub IoT.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Próximos passos Instalei um agente e não vejo atividades nem logs...

1. Verifique se o [tipo do agente se adequa à plataforma de sistema operacional designada do seu dispositivo](how-to-deploy-agent.md)

1. Confirme se o [agente está em execução no dispositivo](how-to-agent-configuration.md).

1. Verifique se o [serviço foi habilitado com êxito](quickstart-onboard-iot-hub.md) para **Segurança** no Hub IoT.

1. Verifique se o dispositivo está [configurado no Hub IOT com o módulo defender para IOT](quickstart-create-security-twin.md).

Se as atividades ou os logs ainda não estiverem disponíveis, entre em contato com seu parceiro do defender para IoT para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão com a Internet é interrompida?

O agente continuará a ser executado e armazenará os dados enquanto o dispositivo estiver em execução. Os dados são armazenados no cache de mensagens de segurança de acordo com a configuração de tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo for reiniciado, o agente de segurança vai se recuperar automaticamente?

O agente de segurança foi projetado para ser re-executado automaticamente com cada reinicialização do dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos do computador como qualquer outro aplicativo/processo e não deve interromper a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente é executado está relacionado à instalação e configuração dele. Antes de tentar implantar o agente em um ambiente de produção, é recomendável testar, em um ambiente contido, a configuração desse agente e a interoperabilidade dele com seus outros aplicativos e funcionalidades de IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou realizando alguma manutenção no dispositivo. Posso desativar o agente?

O agente não pode ser desativado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há alguma forma de testar se o agente está funcionando corretamente?

Se o agente parar de se comunicar ou falhar em enviar mensagens de segurança, um alerta com a mensagem **O dispositivo está silencioso** será gerado.

## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprios alertas?

Sim. Você pode definir um alerta personalizado para um conjunto predeterminado de comportamentos, como endereço IP e portas abertas. Confira [Criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como criá-los.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver logs? Posso personalizar logs?

- Exiba alertas e recomendações usando seu workspace conectado do Log Analytics. Configure o tamanho e a duração do armazenamento no workspace.

- Os dados brutos do seu agente de segurança também podem ser armazenados em sua conta do Log Analytics. Considere o tamanho, a duração, os requisitos de armazenamento e os custos associados antes de alterar a configuração dessa opção.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar o defender para IoT à identidade do módulo? Para que é utilizado?

O módulo defender para IoT é usado para configuração e gerenciamento do agente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar a usar o defender para IoT, consulte os seguintes artigos:

- Leia a [visão geral](overview.md) do defender for IOT
- Verifique os [Pré-requisitos do serviço](service-prerequisites.md)
- Saiba mais sobre como [Começar](getting-started.md)
- Entender o [defender para alertas de segurança de IOT](concept-security-alerts.md)
