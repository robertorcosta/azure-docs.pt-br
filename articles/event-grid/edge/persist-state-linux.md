---
title: Persist state in Linux - Azure Event Grid IoT Edge | Microsoft Docs
description: Persistir metadados no Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086662"
---
# <a name="persist-state-in-linux"></a>Persiste estado no Linux

Tópicos e assinaturas criados no módulo Event Grid são armazenados no sistema de arquivos de contêiner por padrão. Sem persistência, se o módulo for reimplantado, todos os metadados criados seriam perdidos. Para preservar os dados através de implantações e reinicializações, você precisa persistir os dados fora do sistema de arquivos de contêineres.

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a seção de eventos persiste para permitir a persistência do evento também.

Este artigo fornece as etapas para implantar o módulo Event Grid com persistência em implantações Linux.

> [!NOTE]
>O módulo Event Grid é executado como um `2000` usuário `eventgriduser`de baixo privilégio com UID e nome .

## <a name="persistence-via-volume-mount"></a>Persistência via montagem de volume

 [Os volumes docker](https://docs.docker.com/storage/volumes/) são usados para preservar os dados através de implantações. Você pode permitir que o docker crie automaticamente um volume nomeado como parte da implantação do módulo Event Grid. Esta opção é a opção mais simples. Você pode especificar o nome de volume a ser criado na seção **'Vincular':**

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Não altere a segunda parte do valor de vinculação. Ele aponta para um local específico dentro do módulo. Para o módulo Event Grid no Linux, ele tem que ser **/app/metadataDb**.

Por exemplo, a seguinte configuração resultará na criação do volume **egmetadataDbVol** onde os metadados serão persistidos.

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
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

Em vez de montar um volume, você pode criar um diretório no sistema host e montar esse diretório.

## <a name="persistence-via-host-directory-mount"></a>Persistência através da montagem do diretório host

Em vez de um volume de docker, você também tem a opção de montar uma pasta host.

1. Primeiro crie um usuário com nome **eventgriduser** e ID **2000** na máquina host executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de arquivos host executando o seguinte comando.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, executar o seguinte comando criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, torne o proprietário do **eventgriduser** desta pasta executando o seguinte comando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Use **Binds** para montar o diretório e reimplantar o módulo Event Grid do portal Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >Não altere a segunda parte do valor de vinculação. Ele aponta para um local específico dentro do módulo. Para o módulo Event Grid no linux, ele tem que ser **/app/metadataDb** e **/app/eventsDb**


## <a name="persist-events"></a>Persistir eventos

Para ativar a persistência do evento, você deve primeiro ativar a persistência de metadados através do suporte de volume ou do diretório do host usando as seções acima.

Coisas importantes a serem anotadas sobre eventos persistentes:

* Os eventos persistentes são ativados por assinatura de evento e são opt-in uma vez que um volume ou diretório tenha sido montado.
* A persistência do evento é configurada em uma Assinatura de Evento no momento da criação e não pode ser modificada uma vez que a Assinatura de Evento é criada. Para alternar a persistência do evento, você deve excluir e recriar a Assinatura de Evento.
* Eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto, a diferença de velocidade é altamente dependente das características da unidade. A troca entre velocidade e confiabilidade é inerente a todos os sistemas de mensagens, mas geralmente só se torna perceptível em grande escala.

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
