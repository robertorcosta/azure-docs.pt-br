---
title: Use o armazenamento local do dispositivo IoT Edge a partir de um módulo - Azure IoT Edge | Microsoft Docs
description: Use variáveis de ambiente e crie opções para permitir o acesso do módulo ao armazenamento local do dispositivo IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434536"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer acesso de módulos ao armazenamento local de um dispositivo

Além de armazenar dados usando serviços de armazenamento Do Zure ou no armazenamento de contêineres do seu dispositivo, você também pode dedicar armazenamento no próprio dispositivo IoT Edge host para maior confiabilidade, especialmente quando operar off-line.

## <a name="link-module-storage-to-device-storage"></a>Vincular o armazenamento do módulo ao armazenamento do dispositivo

Para habilitar um link do armazenamento do módulo ao armazenamento no sistema host, crie uma variável de ambiente para o seu módulo que aponte para uma pasta de armazenamento no contêiner. Em seguida, use as opções de criação para associar essa pasta de armazenamento a uma pasta no computador host.

Por exemplo, se você quiser habilitar o hub IoT Edge para armazenar mensagens no armazenamento local do seu dispositivo e recuperá-las mais tarde, você pode configurar as variáveis de ambiente e as opções de criação no portal Azure na seção **Configurações de execução.**

1. Para o hub IoT Edge e o agente IoT Edge, adicione uma variável de ambiente chamada **storageFolder** que aponta para um diretório no módulo.
1. Para o hub IoT Edge e o agente IoT Edge, adicione conexões para conectar um diretório local na máquina host a um diretório no módulo. Por exemplo: 

   ![Adicionar opções de criação e variáveis de ambiente para armazenamento local](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ou, você pode configurar o armazenamento local diretamente no manifesto de implantação. Por exemplo: 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>` Substitua `<ModuleStoragePath>` e por seu caminho de armazenamento de host e módulo; ambos os valores devem ser um caminho absoluto.

Por exemplo, em um `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` sistema Linux, significa que o diretório **/etc/iotedge/storage** no sistema host é mapeado para o diretório **/iotedge/storage/** no contêiner. Em um sistema Windows, `"Binds":["C:\\temp:C:\\contemp"]` como outro exemplo, significa que o diretório **C:\\temp** no sistema host é mapeado para o diretório **C:\\contemp** no contêiner.

Além disso, em dispositivos Linux, certifique-se de que o perfil do usuário para o seu módulo tenha as permissões de leitura, gravação e execução necessárias para o diretório do sistema host. Voltando ao exemplo anterior de permitir que o hub IoT Edge armazene mensagens no armazenamento local do seu dispositivo, você precisa conceder permissões ao seu perfil de usuário, uID 1000. (O agente IoT Edge opera como raiz, por isso não precisa de permissões adicionais.) Existem várias maneiras de gerenciar permissões `chown` de diretório em sistemas `chmod` Linux, incluindo o uso para alterar o proprietário do diretório e, em seguida, alterar as permissões, tais como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Você pode encontrar mais detalhes sobre criar opções a partir de [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo adicional de acesso ao armazenamento do host a partir de um módulo, consulte [Armazenar dados na borda com o Azure Blob Storage no IoT Edge](how-to-store-data-blob.md).
