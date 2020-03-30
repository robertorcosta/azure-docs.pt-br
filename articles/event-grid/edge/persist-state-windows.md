---
title: Persist state in Windows - Azure Event Grid IoT Edge | Microsoft Docs
description: Persista estado no Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086614"
---
# <a name="persist-state-in-windows"></a>Persista estado no Windows

Tópicos e assinaturas criados no módulo Event Grid são armazenados no sistema de arquivos de contêiner por padrão. Sem persistência, se o módulo for reimplantado, todos os metadados criados seriam perdidos. Para preservar os dados através de implantações e reinicializações, você precisa persistir os dados fora do sistema de arquivos de contêineres. 

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a seção de eventos persiste para permitir a persistência do evento também.

Este artigo fornece as etapas necessárias para implantar o módulo Event Grid com persistência em implantações do Windows.

> [!NOTE]
>O módulo Event Grid é executado como um usuário de baixo privilégio **ContainerUser** no Windows.

## <a name="persistence-via-volume-mount"></a>Persistência via montagem de volume

[Os volumes docker](https://docs.docker.com/storage/volumes/) são usados para preservar dados através de implantações. Para montar um volume, você precisa criá-lo usando comandos docker, dar permissões para que o contêiner possa ler, gravar para ele e, em seguida, implantar o módulo.

1. Crie um volume executando o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Obtenha o diretório host para o quais o volume mapeia executando o comando abaixo

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Saída da amostra:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Adicione o grupo **Usuários** ao valor apontado pelo **Ponto de Montagem** da seguinte forma:
    1. Iniciar o Explorador de Arquivos.
    1. Navegue até a pasta apontada pelo **Ponto de Montagem**.
    1. Clique com o botão direito do mouse e selecione **Propriedades**.
    1. Selecione **Segurança**.
    1. Em *Grupo ou nomes de usuário, selecione **Editar**.
    1. Selecione **Adicionar,** inserir, `Users`selecionar **'''Nomes' e**selecionar **Ok**.
    1. Em *Permissões para Usuários,* **selecione Modificar**e selecione **Ok**.
1. Use **Binds** para montar este volume e reimplantar o módulo Event Grid do portal Azure

   Por exemplo,

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >Não altere a segunda parte do valor de vinculação. Ele aponta para um local específico no módulo. Para o módulo Event Grid no windows, tem que ser **C:\\app\\metadataDb**.


    Por exemplo,

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>Persistência através da montagem do diretório host

Em vez de montar um volume, você pode criar um diretório no sistema host e montar esse diretório.

1. Crie um diretório no sistema de arquivos host executando o seguinte comando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Por exemplo,

   ```sh
   mkdir C:\myhostdir
   ```
1. Use **Binds** para montar seu diretório e reimplantar o módulo Event Grid do portal Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Não altere a segunda parte do valor de vinculação. Ele aponta para um local específico no módulo. Para o módulo Event Grid no windows, tem que ser **C:\\app\\metadataDb**.

    Por exemplo,

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
## <a name="persist-events"></a>Persistir eventos

Para ativar a persistência do evento, você deve primeiro ativar a persistência de eventos através do suporte de volume ou do suporte do diretório do host usando as seções acima.

Coisas importantes a serem anotadas sobre eventos persistentes:

* Os eventos persistentes são ativados por assinatura de evento e são opt-in uma vez que um volume ou diretório tenha sido montado.
* A persistência do evento é configurada em uma Assinatura de Evento no momento da criação e não pode ser modificada uma vez que a Assinatura de Evento é criada. Para alternar a persistência do evento, você deve excluir e recriar a Assinatura de Evento.
* Eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto, a diferença de velocidade é altamente dependente das características da unidade. A troca entre velocidade e confiabilidade é inerente a todos os sistemas de mensagens, mas só se torna perceptível em grande escala.

Para habilitar a persistência do `persistencePolicy` evento `true`em uma assinatura de evento, definido como:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```