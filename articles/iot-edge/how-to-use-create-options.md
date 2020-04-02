---
title: Criar gravaçãoOpde para módulos - Azure IoT Edge | Microsoft Docs
description: Como usar createOpções no manifesto de implantação para configurar módulos em tempo de execução
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550338"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Como configurar opções de criação de contêineres para módulos IoT Edge

O parâmetro **createOptions** no manifesto de implantação permite configurar os contêineres do módulo em tempo de execução. Esse parâmetro expande seu controle sobre os módulos e permite tarefas como permitir ou restringir o acesso do módulo aos recursos do dispositivo host ou configurar a rede.

Os módulos IoT Edge são implementados como recipientes compatíveis com Docker no seu dispositivo IoT Edge. O Docker oferece muitas opções para criar contêineres, e essas opções também se aplicam aos módulos IoT Edge. Para obter mais informações, consulte [opções de criação de contêiner Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Opções de criação de formato

O manifesto de implantação do IoT Edge aceita criar opções formatadas como JSON. Por exemplo, pegue as opções de criação que estão automaticamente incluídas para cada módulo edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Este exemplo do edgeHub usa o parâmetro **HostConfig.PortBindings** para mapear portas expostas no contêiner para uma porta no dispositivo host.

Se você usar as extensões Azure IoT Tools para Visual Studio ou Visual Studio Code, você pode escrever as opções de criação no formato JSON no arquivo **deployment.template.json.** Então, quando você usar a extensão para construir a solução IoT Edge ou gerar o manifesto de implantação, ele irá stringify o JSON para você no formato que o tempo de execução do IoT Edge espera. Por exemplo: 

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Uma dica para escrever opções `docker inspect` de criação é usar o comando. Como parte do seu processo de desenvolvimento, execute o módulo localmente usando `docker run <container name>`. Uma vez que você tenha o módulo `docker inspect <container name>`funcionando da maneira que você quer, execute . Este comando produz os detalhes do módulo no formato JSON. Encontre os parâmetros configurados e copie o JSON. Por exemplo: 

[![Resultados do docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) inspecione edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Cenários comuns

As opções de criação de contêineres permitem muitos cenários, mas aqui estão alguns que aparecem com mais frequência ao construir soluções de IoT Edge:

* [Dar aos módulos acesso ao armazenamento de host](how-to-access-host-storage-from-module.md)
* [Mapear a porta do host para a porta do módulo](#map-host-port-to-module-port)
* [Restringir a memória do módulo e o uso da CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapear a porta do host para a porta do módulo

Se o seu módulo precisa se comunicar com um serviço fora da solução IoT Edge e não estiver usando roteamento de mensagens para fazê-lo, então você precisa mapear uma porta de host para uma porta de módulo.

>[!TIP]
>Este mapeamento de porta não é necessário para a comunicação módulo-módulo no mesmo dispositivo. Se o módulo A precisar consultar uma API hospedada no módulo B, ele poderá fazê-lo sem qualquer mapeamento de porta. O módulo B precisa expor uma porta em `EXPOSE 8080`seu arquivo de docker, por exemplo: . Em seguida, o módulo A pode consultar a API `http://ModuleB:8080/api`usando o nome do módulo B, por exemplo: .

Primeiro, certifique-se de que uma porta dentro do módulo esteja exposta para ouvir as conexões. Você pode fazer isso usando uma instrução [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) no arquivo docker. Por exemplo, `EXPOSE 8080`. A instrução de expor é padrão ao protocolo TCP, se não especificada, ou você pode especificar UDP.

Em seguida, use a configuração **PortBindings** no grupo **HostConfig** do [contêiner Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) para mapear a porta exposta no módulo para uma porta no dispositivo host. Por exemplo, se você expôs a porta 8080 dentro do módulo e deseja mapear isso para a porta 80 do dispositivo host, as opções de criação no arquivo template.json se pareceriam com o seguinte exemplo:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Uma vez stringified para o manifesto de implantação, a mesma configuração se pareceria com o seguinte exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Restringir a memória do módulo e o uso da CPU

Você pode declarar quanto dos recursos do host um módulo pode usar. Este controle é útil para garantir que um módulo não possa consumir muita memória ou uso de CPU e impedir que outros processos sejam executados no dispositivo. Você pode gerenciar essas configurações com [opções de criação de contêiner Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) no grupo **HostConfig,** incluindo:

* **Memória**: Limite de memória em bytes. Por exemplo, 268435456 bytes = 256 MB.
* **MemorySwap**: Limite total de memória (memória + swap). Por exemplo, 536870912 bytes = 512 MB
* **Período da CPU**: O comprimento de um período de CPU em microsegundos. O valor padrão é de 100000, então, por exemplo, um valor de 25000 limita um contêiner a 25% dos recursos da CPU.

No formato template.json, esses valores se pareceriam com o seguinte exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Uma vez stringified para o manifesto de implantação final, esses valores se pareceriam com o seguinte exemplo:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de opções de criação em ação, consulte as seguintes amostras de IoT Edge:

* [Visão personalizada e Borda IoT do Azure em um Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Amostra de armazenamento de blob do Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
