---
title: Tipos e descrições de alertas
description: Examine as descrições de alerta do defender for IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/22/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3c4cc5f7bb9f0c529e603b91ee96c6c1c476f20d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787508"
---
# <a name="defender-for-iot-engine-alerts"></a>Alertas de mecanismo do defender para IoT

Este artigo descreve os alertas que podem ser gerados a partir dos mecanismos do defender for IoT. Os alertas são exibidos na janela alertas, na qual você pode gerenciar o evento de alerta. 

## <a name="policy-engine-alerts"></a>Alertas do mecanismo de política

Os alertas do mecanismo de política descrevem os desvios do comportamento de rede de linha de base aprendido.

| Título  | Descrição | Severidade |
|--|--|--|
| Uso anormal de endereços MAC | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Secundária |
| Software Beckhoff alterado | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Falha no logon do banco de dados | Foi detectada uma tentativa de logon com falha de um dispositivo de origem para um servidor de destino. Isso pode ser o resultado de um erro humano, mas também pode indicar uma tentativa mal-intencionada de comprometer o servidor ou os dados nele. | Principal |
| Versão do firmware Emerson ROC alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| O endereço externo na rede se comunica com a Internet | Um dispositivo de origem definido como parte de sua rede está se comunicando com endereços da Internet. A origem não está autorizada a se comunicar com endereços da Internet. | Crítico |
| Dispositivo de campo descoberto inesperadamente | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Alteração de firmware detectada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Versão do firmware alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Operação não autorizada de Foxboro I/A | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Falha no logon do FTP | Foi detectada uma tentativa de logon com falha de um dispositivo de origem para um servidor de destino. Isso pode ser o resultado de um erro humano, mas também pode indicar uma tentativa mal-intencionada de comprometer o servidor ou os dados nele. | Principal |
| O código de função gerou uma exceção não autorizada | Um dispositivo de origem (escravo) retornou uma exceção para um dispositivo de destino (Mestre). | Principal |
| Configurações de tipo de mensagem ganso | As configurações de mensagem (identificadas por ID de protocolo) foram alteradas em um dispositivo de origem. | Aviso |
| Versão do firmware Honeywell alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Comunicação HTTP ilegal | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso à Internet detectado | Um dispositivo de origem definido como parte de sua rede está se comunicando com endereços da Internet. A origem não está autorizada a se comunicar com endereços da Internet. | Principal |
| Versão do firmware Mitsubishi alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Violação de intervalo de endereços Modbus | Um dispositivo mestre solicitou acesso a um novo endereço de memória subordinada. | Principal |
| Versão do firmware Modbus alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Nova atividade detectada-classe CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-serviço de classe CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-comando CIP PCCC | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-símbolo CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-conexão de e/s de EtherNet/IP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-comando do protocolo EtherNet/IP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-código de mensagem GSM | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-códigos de comando LonTalk | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova descoberta de porta | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Aviso |
| Nova atividade detectada-variável de rede LonTalk | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-solicitação de dados Ovation | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-comando de leitura/gravação (grupo de índice AMS) | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-comando de leitura/gravação (deslocamento do índice do AMS) | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-tipo de mensagem DeltaV não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-operação DeltaV ROC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-tipo de mensagem RPC não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-invocação de procedimento RPC não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-usando o comando do protocolo AMS | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-usando o comando do Siemens SICAM | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-usando o comando do protocolo Suitelink | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-usando sessões de protocolo Suitelink | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada-usando o comando Yokogawa VNetIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Novo ativo detectado | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Nova configuração de dispositivo LLDP | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Nova descoberta de porta | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Aviso |
| Comando não autorizado do Omron ALETAs | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Firmware de PLC S7 Plus alterado | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Configurações de amostra de tipo de mensagem de valores | As configurações de mensagem (identificadas por ID de protocolo) foram alteradas em um dispositivo de origem. | Aviso |
| Suspeita de verificação de integridade ilegal | Uma verificação foi detectada em um dispositivo de origem DNP3 (outstation). Essa verificação não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Comando de link de computador Toshiba não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Secundária |
| Operação de arquivo ABB Totalflow não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação de registro ABB Totalflow não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso não autorizado ao bloco de dados do Siemens S7 | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido em sua rede. | Aviso |
| Acesso não autorizado ao objeto Siemens S7 Plus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso não autorizado à marca de questionamento | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Acesso a objeto BACNet não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Rota de BACNet não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Logon de banco de dados não autorizado | Foi detectada uma tentativa de logon entre um cliente de origem e um servidor de destino. A comunicação entre esses dispositivos não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Operação de banco de dados não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação Emerson ROC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso a arquivos de GE SRTP não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Comando do protocolo GE SRTP não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação de memória do sistema de SRTP do GE não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Atividade HTTP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Servidor HTTP não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido em sua rede. | Principal |
| Ação SOAP HTTP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Agente de usuário HTTP não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido em sua rede. | Principal |
| Conectividade de Internet não autorizada detectada | Um dispositivo de origem definido como parte de sua rede está se comunicando com endereços da Internet. A origem não está autorizada a se comunicar com endereços da Internet. | Crítico |
| Comando Mitsubishi MELSEC não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso de programa MMS não autorizado | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Serviço MMS não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Conexão multicast/difusão não autorizada | Uma conexão multicast/difusão foi detectada entre um dispositivo de origem e outros dispositivos. A comunicação multicast/difusão não é autorizada. | Crítico |
| Consulta de nome não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Atividade OPC UA não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Solicitação/resposta de OPC UA não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| A operação não autorizada foi detectada por uma regra definida pelo usuário | O tráfego foi detectado entre dois dispositivos. Esta atividade não é autorizada com base em uma regra de alerta personalizada definida por um usuário. | Principal |
| Leitura de configuração de PLC não autorizada | O dispositivo de origem não está definido como um dispositivo de programação, mas realizou uma operação de leitura/gravação em um controlador de destino. As alterações de programação só devem ser executadas por dispositivos de programação. Um aplicativo de programação pode ter sido instalado neste dispositivo. | Aviso |
| Gravação de configuração de PLC não autorizada | O dispositivo de origem enviou um comando para ler/gravar o programa de um controlador de destino. Esta atividade não foi vista anteriormente. | Principal |
| Upload de programa PLC não autorizado | O dispositivo de origem enviou um comando para ler/gravar o programa de um controlador de destino. Esta atividade não foi vista anteriormente. | Principal |
| Programação de PLC não autorizada | O dispositivo de origem não está definido como um dispositivo de programação, mas realizou uma operação de leitura/gravação em um controlador de destino. As alterações de programação só devem ser executadas por dispositivos de programação. Um aplicativo de programação pode ter sido instalado neste dispositivo. | Crítico |
| Tipo de quadro refinado não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Comando SAIA S-bus não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Execução da função de controle de S7 de Siemens não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Execução não autorizada do Siemens S7 da função definida pelo usuário | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso de bloqueio do Siemens S7 Plus não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação não autorizada do Siemens S7 Plus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Logon SMB não autorizado | Foi detectada uma tentativa de logon entre um cliente de origem e um servidor de destino. A comunicação entre esses dispositivos não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Operação SNMP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso não autorizado do SSH | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Processo do Windows não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido em sua rede. | Principal |
| Serviço do Windows não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido em sua rede. | Principal |
| A operação não autorizada foi detectada por uma regra definida pelo usuário | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros viola uma regra definida pelo usuário | Principal |
| Extensão elétrica Modbus Schneider não permitida | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido de tipos ASDU | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido do código de função DNP3 | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido de indicação interna (em) | Um dispositivo de origem DNP3 (outstation) relatou uma indicação interna (em) que não foi autorizada como tráfego aprendido em sua rede. | Principal |
| Uso não permitido do código de função Modbus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido em sua rede. A combinação a seguir não é autorizada. | Principal |

## <a name="anomaly-engine-alerts"></a>Alertas do mecanismo de anomalias

| Título | Descrição | Severidade |
|--|--|--|
| Padrão de exceção anormal no subordinado | Um número excessivo de erros foi detectado em um dispositivo de origem. Isso pode ser o resultado de um problema operacional. | Secundária |
| Comprimento de cabeçalho HTTP anormal | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Número anormal de parâmetros no cabeçalho HTTP | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Comportamento periódico anormal no canal de comunicação | Foi detectada uma alteração na frequência de comunicação entre os dispositivos de origem e de destino. | Secundária |
| Término anormal de aplicativos | Um número excessivo de comandos de parada foi detectado em um dispositivo de origem. Isso pode ser resultado de um problema operacional ou uma tentativa de manipular o dispositivo. | Principal |
| Largura de banda de tráfego anormal | Largura de banda anormal detectada em um canal. A largura de banda parece ser significativamente menor/maior do que a detectada anteriormente. Para obter detalhes, trabalhe com o widget largura de banda total. | Aviso |
| Largura de banda de tráfego anormal entre dispositivos | Largura de banda anormal detectada em um canal. A largura de banda parece ser significativamente menor/maior do que a detectada anteriormente. Para obter detalhes, trabalhe com o widget largura de banda total. | Aviso |
| Exame de endereço detectado | Um dispositivo de origem foi detectado na verificação de dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Verificação de endereço ARP detectada | Um dispositivo de origem foi detectado verificando dispositivos de rede usando o ARP (protocolo de resolução de endereço). Este endereço de dispositivo não foi autorizado como endereço de verificação ARP válido. | Crítico |
| Verificação de endereço ARP detectada | Um dispositivo de origem foi detectado verificando dispositivos de rede usando o ARP (protocolo de resolução de endereço). Este endereço de dispositivo não foi autorizado como endereço de verificação ARP válido. | Crítico |
| Falsificação de ARP | Uma quantidade anormal de pacotes foi detectada na rede. Isso pode indicar um ataque, por exemplo, uma falsificação ARP ou um ataque de inundação ICMP. | Aviso |
| Tentativas de logon excessivas | Um dispositivo de origem foi visto executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Número excessivo de sessões | Um dispositivo de origem foi visto executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Taxa de reinicialização excessiva de uma outstation | Um número excessivo de comandos de reinicialização foi detectado em um dispositivo de origem. Isso pode ser resultado de um problema operacional ou uma tentativa de manipular o dispositivo. | Principal |
| Tentativas de logon SMB excessivas | Um dispositivo de origem foi visto executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Inundação ICMP | Uma quantidade anormal de pacotes foi detectada na rede. Isso pode indicar um ataque, por exemplo, uma falsificação ARP ou um ataque de inundação ICMP. | Aviso |
| Conteúdo de cabeçalho HTTP ilegal | O dispositivo de origem iniciou uma solicitação inválida. | Crítico |
| Canal de comunicação inativo | Um canal de comunicação entre dois dispositivos estava inativo durante um período em que a atividade geralmente é vista. Isso pode indicar que o programa que gera esse tráfego foi alterado ou o programa pode estar indisponível. É recomendável revisar a configuração do programa instalado e verificar se ele está configurado corretamente. | Aviso |
| Verificação de endereço de longa duração detectada | Um dispositivo de origem foi detectado na verificação de dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Tentativa de detecção de senha detectada | Um dispositivo de origem foi visto executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Exame de PLC detectado | Um dispositivo de origem foi detectado na verificação de dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Verificação de porta detectada | Um dispositivo de origem foi detectado na verificação de dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Comprimento de mensagem inesperado | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Tráfego inesperado para a porta padrão | O tráfego foi detectado em um dispositivo usando uma porta reservada para outro protocolo. | Principal |

## <a name="protocol-violation-engine-alerts"></a>Alertas do mecanismo de violação de protocolo

| Título | Descrição | Severidade |
|--|--|--|
| Pacotes malformados excessivos em uma única sessão | Um número anormal de pacotes malformados enviados do dispositivo de origem para o dispositivo de destino. Isso pode indicar comunicações erradas ou uma tentativa de manipular o dispositivo de destino. | Principal |
| Atualizações de firmware | Um dispositivo de origem enviou um comando para atualizar o firmware em um dispositivo de destino. Verifique se as atualizações recentes de programação, configuração e firmware feitas no dispositivo de destino são válidas. | Aviso |
| Código de função sem suporte na outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Mensagem BACNet ilegal | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Tentativa de conexão inválida na porta 0 | Um dispositivo de origem tentou se conectar ao dispositivo de destino no número da porta zero (0). Para TCP, a porta 0 é reservada e não pode ser usada. Para UDP, a porta é opcional e um valor de 0 significa nenhuma porta. Normalmente, não há nenhum serviço em um sistema que escuta na porta 0. Esse evento pode indicar uma tentativa de atacar o dispositivo de destino ou indicar que um aplicativo foi programado incorretamente. | Secundária |
| Operação DNP3 inválida | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Operação MODBUS inválida (exceção gerada pelo mestre) | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Operação MODBUS inválida (código de função zero) | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Versão de protocolo ilegal | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Parâmetro incorreto enviado para a outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Inicialização de um código de função obsoleto (inicializar dados) | O dispositivo de origem iniciou uma solicitação inválida. | Secundária |
| Inicialização de um código de função obsoleto (salvar configuração) | O dispositivo de origem iniciou uma solicitação inválida. | Secundária |
| O mestre solicitou uma confirmação de camada de aplicativo | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Exceção Modbus | Um dispositivo de origem (escravo) retornou uma exceção para um dispositivo de destino (Mestre). | Principal |
| O dispositivo escravo recebeu um tipo de ASDU inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu uma causa de comando ilegal de transmissão | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu um endereço comum inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu um parâmetro de endereço de dados inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu um parâmetro de valor de dados inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu um código de função inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo escravo recebeu um endereço de objeto de informação ilegal | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Objeto desconhecido enviado para a outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Uso de um código de função reservado | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Uso de formatação inadequada por outstation | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Uso de sinalizadores de status reservados (em) | Um dispositivo de origem DNP3 (outstation) usou o indicador interno reservado 2,6. É recomendável verificar a configuração do dispositivo. | Aviso |

## <a name="malware-engine-alerts"></a>Alertas do mecanismo de malware

| Título | Descrição| Severidade |
|--|--|--|
| Tentativa de conexão a IP mal-intencionado conhecido | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Mensagem SMB inválida (DoublePulsar Backdoor implante) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Solicitação de nome de domínio mal-intencionado | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Arquivo de teste de malware detectado-êxito do EICAR AV | Um arquivo de teste do EICAR AV foi detectado no tráfego entre dois dispositivos. O arquivo não é um malware. Ele é usado para confirmar se o software antivírus está instalado corretamente; demonstre o que acontece quando um vírus é encontrado e marque procedimentos internos e reações quando um vírus for encontrado. O software antivírus deve detectar o EICAR como se fosse um vírus real. | Principal |
| Suspeita de malware Conficker | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Suspeita de ataque de negação de serviço | Um dispositivo de origem tentou iniciar um número excessivo de novas conexões com um dispositivo de destino. Isso pode ser um ataque de DOS (negação de serviço) contra o dispositivo de destino e pode interromper a funcionalidade do dispositivo, afetar o desempenho e a disponibilidade do serviço ou causar erros irrecuperáveis. | Crítico |
| Suspeita de atividade mal-intencionada | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Suspeita de atividade mal-intencionada (BlackEnergy) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (DarkComet) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Duqu) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (chama) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Havex) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Karagany) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (com luzes) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (consultas de nome) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Suspeita de atividade mal-intencionada (Ivy suspeita) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Regin) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Stuxnet) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (WannaCry) | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Suspeita de malware NotPetya-parâmetros SMB ilegais detectados | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de malware NotPetya-transação SMB ilegal detectada | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |
| Suspeita de execução remota de código com PsExec | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Suspeita de gerenciamento de serviços remotos do Windows | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Arquivo executável suspeito detectado no ponto de extremidade | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Principal |
| Tráfego suspeito detectado | Foi detectada atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por malware conhecido. | Crítico |

## <a name="operational-engine-alerts"></a>Alertas do mecanismo operacional

| Título | Descrição | Severidade |
|--|--|--|
| Um comando S7 Stop PLC foi enviado | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando Iniciar seja enviado. | Aviso |
| Falha na operação de BACNet | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Estado do dispositivo MMS inadequado | Um dispositivo de manufatura virtual (VMD) MMS enviou uma mensagem de status. A mensagem indica que o servidor pode não estar configurado corretamente, parcialmente operacional ou não operacional. | Principal |
| Alteração da configuração do dispositivo | Uma alteração de configuração foi detectada em um dispositivo de origem. | Secundária |
| Estouro de buffer de eventos contínuo na outstation | Um evento de estouro de buffer foi detectado em um dispositivo de origem. O evento pode causar corrupção de dados, falhas do programa ou execução de código mal-intencionado. | Principal |
| Redefinição do controlador | Um dispositivo de origem enviou um comando de redefinição para um controlador de destino. O controlador parou de operar temporariamente e foi iniciado automaticamente. | Aviso |
| Parada do controlador | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando Iniciar seja enviado. | Aviso |
| O dispositivo não pôde receber um endereço IP dinâmico | O dispositivo de origem está configurado para receber um endereço IP dinâmico de um servidor DHCP, mas não recebeu um endereço. Isso indica um erro de configuração no dispositivo ou um erro operacional no servidor DHCP. É recomendável notificar o administrador de rede do incidente | Principal |
| Suspeita de dispositivo ser desconectado (sem resposta) | Um dispositivo de origem não respondeu a um comando enviado a ele. Ele pode ter sido desconectado quando o comando foi enviado. | Principal |
| Falha na solicitação de serviço CIP de EtherNet/IP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Falha no comando de protocolo de encapsulamento EtherNet/IP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Estouro de buffer de eventos na outstation | Um evento de estouro de buffer foi detectado em um dispositivo de origem. O evento pode causar corrupção de dados, falhas do programa ou execução de código mal-intencionado. | Principal |
| A operação de backup esperada não ocorreu | A atividade de transferência de arquivo/backup esperada não ocorreu entre dois dispositivos. Isso pode indicar erros no processo de transferência de backup/arquivo. | Principal |
| Falha do comando GE SRTP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| O comando GE SRTP Stop PLC foi enviado | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando Iniciar seja enviado. | Aviso |
| O bloco de controle ganso requer configuração adicional | Um dispositivo de origem enviou uma mensagem ganso indicando que o dispositivo precisa de comissões. Isso significa que o bloco de controle ganso requer configuração adicional e mensagens ganso são parcialmente ou completamente não operacionais. | Principal |
| A configuração do conjunto de ganso foi alterada | Um conjunto de código de mensagem (identificado pelo ID de protocolo) foi alterado em um dispositivo de origem. Isso significa que o dispositivo relatará um conjunto de diferentes para esta mensagem. | Aviso |
| Status inesperado do controlador Honeywell | Um controlador Honeywell enviou uma mensagem de diagnóstico inesperada indicando uma alteração de status. | Aviso |
| Erro do cliente HTTP | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Endereço IP ilegal | O sistema detectou o tráfego entre um dispositivo de origem e o endereço IP que é um endereço inválido. Isso pode indicar uma configuração incorreta ou uma tentativa de gerar tráfego ilegal. | Secundária |
| Erro de autenticação de Master-Slave | O processo de autenticação entre um dispositivo de origem DNP3 (Mestre) e um dispositivo de destino (outstation) falhou. | Secundária |
| Falha na solicitação de serviço MMS | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Nenhum tráfego detectado na interface do sensor | Um sensor parou de detectar o tráfego de rede em uma interface de rede. | Crítico |
| O servidor OPC UA gerou um evento que requer atenção do usuário | Um servidor OPC UA enviou uma notificação de eventos para um cliente. Esse tipo de evento requer a atenção do usuário | Principal |
| Falha na solicitação de serviço OPC UA | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Outstation reiniciada | Foi detectada uma reinicialização a frio em um dispositivo de origem. Isso significa que o dispositivo foi fisicamente desligado e reconectado. | Aviso |
| Reinicializações de outstation com frequência | Um número excessivo de reinicializações frias foi detectado em um dispositivo de origem. Isso significa que o dispositivo foi fisicamente desligado e revertido em um número excessivo de vezes. | Secundária |
| Configuração de outstation alterada | Uma alteração de configuração foi detectada em um dispositivo de origem. | Principal |
| Configuração corrompida de outstation detectada | Este dispositivo de origem DNP3 (outstation) relatou uma configuração corrompida. | Principal |
| Falha do comando de DCP no refinamento | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Redefinição de fábrica de dispositivos confinados | Um dispositivo de origem enviou um comando de redefinição de fábrica para um dispositivo de destino profino. O comando Redefinir limpa as configurações de dispositivo imfinadas e interrompe sua operação. | Aviso |
| Falha na operação RPC | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Valores de amostra a configuração do conjunto de mensagens foi alterada | Um conjunto de código de mensagem (identificado pelo ID de protocolo) foi alterado em um dispositivo de origem. Isso significa que o dispositivo relatará um conjunto de diferentes para esta mensagem. | Aviso |
| Falha irrecuperável do dispositivo subordinado | Um erro de condição irrecuperável foi detectado em um dispositivo de origem. Esse tipo de erro geralmente indica uma falha de hardware ou uma falha ao executar um comando específico. | Principal |
| Suspeita de problemas de hardware na outstation | Um erro de condição irrecuperável foi detectado em um dispositivo de origem. Esse tipo de erro geralmente indica uma falha de hardware ou uma falha ao executar um comando específico. | Principal |
| Suspeita de dispositivo MODBUS sem resposta | Um dispositivo de origem não respondeu a um comando enviado a ele. Ele pode ter sido desconectado quando o comando foi enviado. | Secundária |
| Tráfego detectado na interface do sensor | Um sensor continuou a detectar o tráfego de rede em uma interface de rede. | Aviso |

## <a name="next-steps"></a>Próximas etapas

Você pode [gerenciar eventos de alerta](how-to-manage-the-alert-event.md).
Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
