---
title: Azure Security Center para ioT agente de segurança de segurança guia de solução de problemas| Microsoft Docs
description: Solução de problemas trabalhando com o Azure Security Center para agentes de segurança IoT para Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600560"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guia de solução de problemas do agente de segurança (Linux)

Este artigo explica como resolver possíveis problemas no processo de inicialização do agente de segurança.

O Azure Security Center para agente IoT se inicia imediatamente após a instalação. O processo de inicialização do agente inclui a leitura da configuração local, a conexão ao Azure IoT Hub e a recuperação da configuração remota de gêmeos. A falha em qualquer uma dessas etapas pode fazer com que o agente de segurança falhe.

Neste guia de solução de problemas você aprenderá como:
> [!div class="checklist"]
> * Valide se o agente de segurança está executando
> * Obter erros do agente de segurança
> * Entender e remediar erros do agente de segurança 

## <a name="validate-if-the-security-agent-is-running"></a>Valide se o agente de segurança está executando

1. Para validar é que o agente de segurança está executando, aguarde alguns minutos após instalar o agente e execute o seguinte comando. 
     <br>

    **Agente C**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# agente**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Se o comando retornar uma linha vazia, o agente de segurança não foi capaz de iniciar com sucesso.    

## <a name="force-stop-the-security-agent"></a>Força parar o agente de segurança 
Nos casos em que o agente de segurança não puder iniciar, pare o agente com o seguinte comando e continue até a tabela de erro abaixo:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Obter erros do agente de segurança
1. Recupere os erros do agente de segurança executando o seguinte comando:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. O comando get security agent error recupera todos os logs criados pelo Azure Security Center para agente IoT. Use a tabela a seguir para entender os erros e tomar as etapas corretas para a remediação. 

> [!Note]
> Os registros de erro são mostrados em ordem cronológica. Certifique-se de anotar o carimbo de tempo de cada erro para ajudar na sua remediação. 

## <a name="restart-the-agent"></a>Reiniciar o agente

1. Depois de localizar e corrigir um erro do agente de segurança, tente reiniciar o agente executando o seguinte comando. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Repita o processo anterior para recuperar stop e recuperar os erros se o agente continuar a falhar no processo de inicialização. 

## <a name="understand-security-agent-errors"></a>Entenda os erros do agente de segurança

A maioria dos erros do agente de segurança são exibidos no seguinte formato: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Código do Erro | Sub-código de erro | Detalhes do erro | Remediar C | Remediar C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Configuração local | Configuração ausente | Falta uma configuração no arquivo de configuração local. A mensagem de erro deve dizer qual chave está faltando. | Adicione a chave ausente ao arquivo /var/LocalConfiguration.json, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter detalhes.| Adicione a chave ausente ao arquivo General.config, consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |
| Configuração local | Configuração cant parse | Um valor de configuração não pode ser analisado. A mensagem de erro deve dizer qual chave não pode ser análiseda. Um valor de configuração não pode ser analisado porque o valor não está no tipo esperado ou o valor está fora do alcance. | Corrija o valor da chave no arquivo /var/LocalConfiguration.json para que corresponda ao esquema LocalConfiguration, consulte a [referência c#localconfig](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |  Corrija o valor da chave no arquivo General.config para que corresponda ao esquema, consulte o [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) para obter detalhes.|
| Configuração local | Formato de arquivo | Falha ao analisar o arquivo de configuração. | O arquivo de configuração está corrompido, baixe o agente e reinstale. | |
| Configuração remota | Tempo limite | O agente não conseguiu buscar o módulo de segurança duplo no período de tempo. | Certifique-se de que a configuração de autenticação está correta e tente novamente. | O agente não conseguiu buscar o módulo de segurança duplo dentro do período de tempo. | Certifique-se de que a configuração de autenticação está correta e tente novamente. |
| Autenticação | Arquivo não existe | O arquivo no caminho dado não existe. | Certifique-se de que o arquivo existe no caminho dado ou vá para o arquivo **LocalConfiguration.json** e altere a configuração **FilePath.** | Certifique-se de que o arquivo existe no caminho dado ou vá para o arquivo **Authentication.config** e altere a configuração **filePath.**|
| Autenticação | Permissão de arquivo | O agente não tem permissões suficientes para abrir o arquivo. | Dê ao **usuário asciotagent** permissões de leitura no arquivo no caminho dado. | Certifique-se de que o arquivo está acessível. |
| Autenticação | Formato de arquivo | O arquivo dado não está no formato correto. | Certifique-se de que o arquivo está no formato correto. Os tipos de arquivo suportados são .pfx e .pem. | Certifique-se de que o arquivo é um arquivo de certificado válido. |
| Autenticação | Não Autorizado | O agente não foi capaz de autenticar contra o IoT Hub com as credenciais dadas. | Validar a configuração de autenticação no arquivo LocalConfiguration, passar pela configuração de autenticação e certificar-se de que todos os detalhes estão corretos, validar se o segredo no arquivo corresponde à identidade autenticada. | Valide a configuração de autenticação em Authentication.config, passe pela configuração de autenticação e certifique-se de que todos os detalhes estão corretos e valide se o segredo no arquivo corresponde à identidade autenticada.
| Autenticação | Não encontrado | O dispositivo/módulo foi encontrado. | Valide a configuração de autenticação - certifique-se de que o nome do host está correto, o dispositivo existe no IoT Hub e tem um módulo gêmeo de segurança azureiotsecurity. |  Valide a configuração de autenticação - certifique-se de que o nome do host está correto, o dispositivo existe no IoT Hub e tem um módulo gêmeo de segurança azureiotsecurity. |
| Autenticação | Configuração ausente | Uma configuração está faltando no arquivo *Authentication.config.* A mensagem de erro deve dizer qual chave está faltando. | Adicione a chave ausente ao arquivo *LocalConfiguration.json.*| Adicione a chave ausente ao arquivo *Authentication.config,* consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |
| Autenticação | Configuração cant parse | Um valor de configuração não pode ser analisado. A mensagem de erro deve dizer qual chave não pode ser análiseda. Um valor de configuração não pode ser analisado porque ou o valor não é do tipo esperado, ou o valor está fora do alcance. |Corrija o valor da chave no arquivo **LocalConfiguration.json.** |Corrija o valor da chave no arquivo **Authentication.config** para corresponder ao esquema, consulte o [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) para obter detalhes.|
|

## <a name="restart-the-agent"></a>Reiniciar o agente
1. Depois de localizar e corrigir um erro do agente de segurança, reinicie o agente executando o seguinte comando:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Se necessário, repita os processos anteriores para forçar a parada do agente e recupere os erros se o agente continuar a falhar no processo de inicialização. 

## <a name="next-steps"></a>Próximas etapas
- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o Centro de Segurança do Azure para [serviço](quickstart-onboard-iot-hub.md) de IoT
- Leia o Azure Security Center for IoT service [FAQ](resources-frequently-asked-questions.md)
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entenda [os alertas de](concept-security-alerts.md) segurança
