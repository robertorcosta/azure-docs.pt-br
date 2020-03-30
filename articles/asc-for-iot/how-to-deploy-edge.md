---
title: Implantar o Azure Security Center para módulo IoT Edge| Microsoft Docs
description: Saiba como implantar um Centro de Segurança Azure para agente de segurança IoT no IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964032"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implante um módulo de segurança no seu dispositivo IoT Edge


**O módulo Azure Security Center for IoT** fornece uma solução de segurança abrangente para seus dispositivos IoT Edge.
O módulo de segurança coleta, agrega e analisa dados brutos de segurança do sistema operacional e do sistema de contêineres em recomendações e alertas de segurança acionáveis.
Para saber mais, consulte [o módulo de segurança do IoT Edge](security-edge-architecture.md).

Neste artigo, você aprenderá como implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar módulo de segurança

Use as seguintes etapas para implantar um Módulo de Segurança Azure para IoT edge para IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. Em seu IoT Hub, certifique-se de que seu dispositivo está [registrado como um dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. O Azure Security Center para módulo IoT Edge requer que a [estrutura AuditD](https://linux.die.net/man/8/auditd) esteja instalada no dispositivo IoT Edge.

    - Instale a estrutura executando o seguinte comando no dispositivo IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Verifique se o AuditD está ativo executando o seguinte comando: 
   
    `sudo systemctl status auditd`<br>
    - A resposta esperada é:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Implantação usando o portal Azure

1. Do portal Azure, **marketplace**aberto.

1. Selecione **Internet das Coisas,** procure **o Azure Security Center para IoT** e selecione-o.

   ![Selecione o Centro de Segurança Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique **em Criar** para configurar a implantação. 

1. Escolha a **assinatura** azure do seu Hub IoT e selecione o **seu IoT Hub**.<br>Selecione **Implantar em um dispositivo** para segmentar um único dispositivo ou **selecione Implantar em Escala** para segmentar vários dispositivos e clique em **Criar**. Para obter mais informações sobre como implantar em escala, consulte [Como implantar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se você selecionou **Implantar em Escala,** adicione o nome e os detalhes do dispositivo antes de continuar na guia **Adicionar módulos** nas instruções a seguir.     

Complete cada etapa para concluir sua implantação de IoT Edge para o Azure Security Center for IoT. 

#### <a name="step-1-modules"></a>Passo 1: Módulos

1. Selecione o módulo **AzureSecurityCenterforIoT.**
1. Na guia Configurações do **módulo,** altere o **nome** para **azureiotsecurity**.
1. Na guia Variáveis de **Meio Ambiente,** adicione uma variável se necessário (por exemplo, nível de depuração).
1. Na guia **Opções de criação de contêiner,** adicione a seguinte configuração:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. Na guia **Configurações duplas** do módulo, adicione a seguinte configuração:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Selecione **Atualização**.

#### <a name="step-2-runtime-settings"></a>Passo 2: Configurações de tempo de execução

1. Selecione **Configurações de tempo de execução**.
1. Em **Edge Hub,** altere a **imagem** para **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Verificar **as opções de criação** está definida para a seguinte configuração: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Selecione **Salvar**.
   
1. Selecione **Avançar**.

#### <a name="step-3-specify-routes"></a>Passo 3: Especifique rotas 

1. Na guia **Especificar rotas,** certifique-se de ter uma rota (explícita ou implícita) que encaminhará mensagens do módulo **de segurança azureiot** para **$upstream** de acordo com os exemplos a seguir. Somente quando a rota estiver no lugar, selecione **Next**.

   Exemplos de rotas:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Selecione **Avançar**.

#### <a name="step-4-review-deployment"></a>Passo 4: Revisão de implantação

- Na guia **'Implantação** de revisão', revise suas informações de implantação e selecione **Criar** para concluir a implantação.

## <a name="diagnostic-steps"></a>Etapas diagnósticas

Se você encontrar um problema, os registros de contêineres são a melhor maneira de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Use as ferramentas e os comandos desta seção para coletar informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os recipientes necessários estão instalados e funcionando conforme o esperado

1. Execute o seguinte comando no seu dispositivo IoT Edge:
    
    `sudo docker ps`
   
1. Verifique se os seguintes contêineres estão em execução:
   
   | Nome | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Se os recipientes mínimos necessários não estiverem presentes, verifique se o manifesto de implantação do IoT Edge está alinhado com as configurações recomendadas. Para obter mais informações, consulte [Implantar módulo IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os registros do módulo em busca de erros
   
1. Execute o seguinte comando no seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Para obter mais registros verbosos, adicione a seguinte variável de `logLevel=Debug`ambiente à implantação do módulo de segurança **azureiot:** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, continue a orientar como fazer para a configuração do módulo. 
> [!div class="nextstepaction"]
> [Guia de configuração de módulo](./how-to-agent-configuration.md)
