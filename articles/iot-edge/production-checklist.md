---
title: Preparar dispositivos e implantações para produção – Azure IoT Edge | Microsoft Docs
description: Learn how to take your Azure IoT Edge solution from development to production, including setting up your devices with the appropriate certificates and making a deployment plan for future code updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457311"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Prepare-se para implantar sua solução IoT Edge em produção

Quando você estiver pronto para levar sua solução IoT Edge do desenvolvimento para a produção, verifique se ela está configurada para desempenho contínuo.

The information provided in this article isn't all equal. Para ajudá-lo a priorizar, cada seção começa com listas que dividem o trabalho em duas seções: **importante** para ser concluído antes da produção ou **útil** para você saber.

## <a name="device-configuration"></a>Configuração do dispositivo

Os dispositivos IoT Edge podem ser de um Raspberry Pi a um laptop para uma máquina virtual em execução em um servidor. Você pode ter acesso ao dispositivo fisicamente ou por meio de uma conexão virtual ou pode ficar isolado por longos períodos de tempo. Either way, you want to make sure that it's configured to work appropriately. 

* **Importante**
    * Instalar certificados de produção
    * Tenha um plano de gerenciamento de dispositivos
    * Use o Moby como o mecanismo de contêiner

* **Utilidade**
    * Escolha o protocolo upstream

### <a name="install-production-certificates"></a>Instalar certificados de produção

Cada dispositivo IoT Edge em produção precisa de um certificado de autoridade de certificação (CA) do dispositivo instalado. Esse certificado de CA é então declarado para o runtime do IoT Edge no arquivo config.yaml. Para facilitar o desenvolvimento e o teste, o runtime do IoT Edge cria certificados temporários se nenhum certificado for declarado no arquivo config.yaml. No entanto, esses certificados temporários expiram após três meses e não são seguros para cenários de produção. 

Para reconhecer a função do certificado de CA do dispositivo, consulte [Como o IoT Edge do Azure usa certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados em um dispositivo IoT Edge e referenciá-los no arquivo config.yaml, consulte [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). As etapas para configurar os certificados são as mesmas, independentemente de o dispositivo ser usado como gateway ou não. Esse artigo fornece scripts para gerar certificados de amostra somente para teste. Não use esses certificados de amostra em produção. 

### <a name="have-a-device-management-plan"></a>Tenha um plano de gerenciamento de dispositivos

Antes de colocar qualquer dispositivo em produção, você deve saber como gerenciar futuras atualizações. Para um dispositivo IoT Edge, a lista de componentes a atualizar pode incluir:

* Firmware do dispositivo
* Bibliotecas do sistema operacional
* Mecanismo de contêiner, como Moby
* Daemon do IoT Edge
* Certificados de AC

For more information, see [Update the IoT Edge runtime](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon IoT Edge exigem acesso físico ou SSH ao dispositivo IoT Edge. If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>Use o Moby como o mecanismo de contêiner

A container engine is a prerequisite for any IoT Edge device. Apenas o Moby-Engine é suportado na produção. Outros mecanismos de contêiner, como o Docker, funcionam com o IoT Edge e não há problema em usar esses mecanismos para desenvolvimento. O mecanismo de moby pode ser redistribuído quando usado com o Azure IoT Edge e a Microsoft fornece serviços para esse mecanismo.

### <a name="choose-upstream-protocol"></a>Escolha o protocolo upstream

The protocol (and therefore the port used) for upstream communication to IoT Hub can be configured for both the IoT Edge agent and the IoT Edge hub. O protocolo padrão é AMQP, mas você pode querer mudar isso dependendo da sua configuração de rede. 

Os dois módulos de runtime possuem uma variável de ambiente **UpstreamProtocol**. Os valores válidos para a variável são: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure the UpstreamProtocol variable for the IoT Edge agent in the config.yaml file on the device itself. For example, if your IoT Edge device is behind a proxy server that blocks AMQP ports, you may need to configure the IoT Edge agent to use AMQP over WebSocket (AMQPWS) to establish the initial connection to IoT Hub. 

Depois que o dispositivo IoT Edge se conectar, certifique-se de continuar configurando a variável UpstreamProtocol para os dois módulos de runtime em implantações futuras. Um exemplo desse processo é fornecido em [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implantação

* **Utilidade**
    * Seja consistente com o protocolo upstream
    * Set up host storage for system modules
    * Reduce memory space used by the IoT Edge hub
    * Não use versões de depuração de imagens de módulo

### <a name="be-consistent-with-upstream-protocol"></a>Seja consistente com o protocolo upstream

If you configured the IoT Edge agent on your IoT Edge device to use a different protocol than the default AMQP, then you should declare the same protocol in all future deployments. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor proxy que bloqueia portas AMQP, você provavelmente configurou o dispositivo para conectar-se por AMQP sobre WebSocket (AMQPWS). When you deploy modules to the device, configure the same AMQPWS protocol for the IoT Edge agent and IoT Edge hub, or else the default AMQP will override the settings and prevent you from connecting again. 

You only have to configure the UpstreamProtocol environment variable for the IoT Edge agent and IoT Edge hub modules. Quaisquer módulos adicionais adotam qualquer protocolo que esteja configurado nos módulos de runtime. 

Um exemplo desse processo é fornecido em [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Set up host storage for system modules

The IoT Edge hub and agent modules use local storage to maintain state and enable messaging between modules, devices, and the cloud. For better reliability and performance, configure the system modules to use storage on the host filesystem.

For more information, see [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduce memory space used by IoT Edge hub

If you're deploying constrained devices with limited memory available, you can configure IoT Edge hub to run in a more streamlined capacity and use less disk space. These configurations do limit the performance of the IoT Edge hub, however, so find the right balance that works for your solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimize o desempenho em dispositivos restritos

The IoT Edge hub is optimized for performance by default, so it attempts to allocate large chunks of memory. Essa configuração pode causar problemas de estabilidade em dispositivos menores, como o Raspberry Pi. If you're deploying devices with constrained resources, you may want to set the **OptimizeForPerformance** environment variable to **false** on the IoT Edge hub. 

When **OptimizeForPerformance** is set to **true**, the MQTT protocol head uses the PooledByteBufferAllocator which has better performance but allocates more memory. The allocator does not work well on 32 bit operating systems or on devices with low memory. Additionally, when optimized for performance, RocksDb allocates more memory for its role as the local storage provider. 

Para obter mais informações, consulte [Problemas de estabilidade em dispositivos restritos por recursos](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Desabilitar protocolos não utilizados

Another way to optimize the performance of the IoT Edge hub and reduce its memory usage is to turn off the protocol heads for any protocols that you're not using in your solution. 

Protocol heads are configured by setting boolean environment variables for the IoT Edge hub module in your deployment manifests. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todas as três variáveis possuem *dois sublinhados* e podem ser configuradas como verdadeiras ou falsas. 

#### <a name="reduce-storage-time-for-messages"></a>Reduza o tempo de armazenamento de mensagens

The IoT Edge hub module stores messages temporarily if they cannot be delivered to IoT Hub for any reason. You can configure how long the IoT Edge hub holds on to undelivered messages before letting them expire. If you have memory concerns on your device, you can lower the **timeToLiveSecs** value in the IoT Edge hub module twin. 

O valor padrão do parâmetro timeToLiveSecs é de 7200 segundos, ou seja, duas horas. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Não use versões de depuração de imagens de módulo

Ao passar de cenários de teste para cenários de produção, lembre-se de remover as configurações de depuração dos manifestos de implantação. Verifique se nenhuma das imagens do módulo nos manifestos de implantação possui o sufixo **\.debug**. Se você adicionou criar opções para expor portas nos módulos para depuração, remova essas opções de criação também. 

## <a name="container-management"></a>Gerenciamento de contêiner

* **Importante**
    * Gerenciar o acesso ao seu registro de contêiner
    * Use tags para gerenciar versões

### <a name="manage-access-to-your-container-registry"></a>Gerenciar o acesso ao seu registro de contêiner

Antes de implantar módulos nos dispositivos de produção do IoT Edge, assegure-se de controlar o acesso ao seu registro de contêiner para que pessoas de fora não possam acessar ou fazer alterações em suas imagens de contêiner. Use um registro de contêiner privado, não público, para gerenciar imagens de contêiner. 

Nos tutoriais e em outra documentação, instruímos você a usar as mesmas credenciais de registro de contêiner no dispositivo IoT Edge que você usa na sua máquina de desenvolvimento. Essas instruções destinam-se apenas a ajudá-lo a configurar ambientes de teste e desenvolvimento mais facilmente e não devem ser seguidas em um cenário de produção. O Registro de Contêiner do Azure [autenticação com entidades de serviço](../container-registry/container-registry-auth-service-principal.md) quando aplicativos ou serviços extraem imagens de contêiner de maneira automática ou não assistida, como fazem os dispositivos IoT Edge. Crie uma entidade de serviço com acesso somente leitura ao seu registro de contêiner e forneça esse nome de usuário e senha no manifesto de implantação.

### <a name="use-tags-to-manage-versions"></a>Use tags para gerenciar versões

A tag is a docker concept that you can use to distinguish between versions of docker containers. As tags são sufixos como **1.0**, que vão no final de um repositório contêiner. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0**. As tags são mutáveis e podem ser alteradas para apontar para outro contêiner a qualquer momento, portanto, sua equipe deve concordar com uma convenção a seguir à medida que você atualiza as imagens do seu módulo no futuro. 

As tags também ajudam a impor atualizações nos seus dispositivos IoT Edge. Quando você envia uma versão atualizada de um módulo para seu registro de contêiner, incremente a tag. Em seguida, envie uma nova implantação para seus dispositivos com a tag incrementada. O mecanismo do contêiner reconhecerá a tag incrementada como uma nova versão e baixará a versão mais recente do módulo para o seu dispositivo. 

Para obter um exemplo de uma convenção de tag, consulte [ Atualizar o runtime do IoT Edge ](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge usa tags de rolagem e tags específicas para rastrear versões. 

## <a name="networking"></a>Rede

* **Utilidade**
    * Revisar configuração de entrada/saída
    * Allow connections from IoT Edge devices
    * Configurar a comunicação por meio de um proxy

### <a name="review-outboundinbound-configuration"></a>Revisar configuração de entrada/saída

Os canais de comunicação entre o Hub IoT e o IoT Edge são sempre configurados para serem enviados. Para a maioria dos cenários do IoT Edge, apenas três conexões são necessárias. O mecanismo de contêiner precisa se conectar ao registro do contêiner (ou registros) que contém as imagens do módulo. O runtime do IoT Edge precisa se conectar ao Hub IoT para recuperar informações de configuração do dispositivo e enviar mensagens e telemetria. E, se você usar o provisionamento automático, o daemon do IoT Edge precisará se conectar ao Serviço de provisionamento de dispositivos. Para obter mais informações, consulte [Regras de configuração de firewall e porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Allow connections from IoT Edge devices

If your networking setup requires that you explicitly permit connections made from IoT Edge devices, review the following list of IoT Edge components:

* **O agente do IoT Edge** abre uma conexão persistente AMQP / MQTT ao Hub IoT, possivelmente por meio de WebSockets. 
* **O hub IoT Edge** abre uma única conexão AMQP persistente ou várias conexões MQTT para o Hub IoT, possivelmente por meio de WebSockets. 
* **O daemon do IoT Edge** faz chamadas de intermitente HTTPS para o Hub IoT. 

Em todos os três casos, o nome DNS deve corresponder ao padrão \*Azure-Devices.NET. 

Além disso, o **mecanismo do Container** faz chamadas para registros de contêiner por HTTPS. Para recuperar as imagens do contêiner do runtime do IoT Edge, o nome DNS é mcr.microsoft.com. O mecanismo de contêiner se conecta a outros registros conforme configurado na implantação. 

Esta lista de verificação é um ponto de partida para regras de firewall:

   | URL (\* = curinga) | Portas TCP de Saída | Uso |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro de contêiner da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso DPS (opcional) |
   | \*.azurecr.io | 443 | Personal and third-party container registries |
   | \*.blob.core.windows.net | 443 | Download Azure Container Registry image deltas from blob storage  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso do Hub IoT |
   | \*. docker.io  | 443 | Docker Hub access (optional) |

Some of these firewall rules are inherited from Azure Container Registry. For more information, see [Configure rules to access an Azure container registry behind a firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação por meio de um proxy

Se os dispositivos forem implantados em uma rede que usa um servidor proxy, eles precisarão se comunicar por meio do proxy para acessar o Hub IoT e os registros de contêiner. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gerenciamento de soluções

* **Utilidade**
    * Configurar logs e diagnósticos
    * Considere testes e pipelines de CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar logs e diagnósticos

On Linux, the IoT Edge daemon uses journals as the default logging driver. Você pode usar a ferramenta de linha de comando `journalctl` para consultar os logs do daemon. No Windows, o daemon IoT Edge usa diagnósticos do PowerShell. Use `Get-IoTEdgeLog` para consultar logs do daemon. IoT Edge modules use the JSON driver for logging, which is the  default.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Ao testar uma implantação do IoT Edge, você geralmente pode acessar seus dispositivos para recuperar logs e solucionar problemas. Em um cenário de implantação, talvez você não tenha essa opção. Pense em como você coletará informações sobre seus dispositivos em produção. Considere como você coletará informações sobre seus dispositivos em produção. Uma opção é usar um módulo de registro que coleta informações dos outros módulos e as envia para a nuvem. Um exemplo de um módulo de registro é [log-logan-analytics](https://github.com/veyalla/logspout-loganalytics), ou você pode criar o seu próprio. 

### <a name="place-limits-on-log-size"></a>Place limits on log size

By default the Moby container engine does not set container log size limits. Over time this can lead to the device filling up with logs and running out of disk space. Consider the following options to prevent this:

**Option: Set global limits that apply to all container modules**

You can limit the size of all container logfiles in the container engine log options. The following example sets the log driver to `json-file` (recommended) with limits on size and number of files:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Add (or append) this information to a file named `daemon.json` and place it the right location for your device platform.

| Plataforma | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

The container engine must be restarted for the changes to take effect.

**Option: Adjust log settings for each container module**

You can do so in the **createOptions** of each module. Por exemplo:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Additional options on Linux systems**

* Configure the container engine to send logs to `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) by setting `journald` as the default logging driver. 

* Periodically remove old logs from your device by installing a logrotate tool. Use a seguinte especificação de arquivo: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Considere testes e pipelines de CI/CD

Para o cenário de implantação do IoT Edge mais eficiente, considere a integração de sua implantação de produção em seus pipelines de teste e CI/CD. O Azure IoT Edge suporta várias plataformas de CI/CD, incluindo o DevOps do Azure. Para obter mais informações, consulte [Integração contínua e implantação contínua no Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [implantação automática do IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta [Integração contínua e implantação contínua](how-to-ci-cd.md).
