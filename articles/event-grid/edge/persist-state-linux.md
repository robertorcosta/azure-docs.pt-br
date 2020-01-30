---
title: Persistir o estado no Linux-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Manter metadados no Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 39b16c6cfd5b94d412827ed88197edbef2da1453
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844625"
---
# <a name="persist-state-in-linux"></a>Persistir o estado no Linux

Os tópicos e as assinaturas criados no módulo grade de eventos são armazenados no sistema de arquivos de contêiner por padrão. Sem persistência, se o módulo for reimplantado, todos os metadados criados serão perdidos. Para preservar os dados entre implantações e reinicializações, você precisa manter os dados fora do sistema de arquivos do contêiner.

Por padrão, somente os metadados são persistidos e os eventos ainda são armazenados na memória para melhorar o desempenho. Siga a seção persistir eventos para habilitar a persistência de eventos também.

Este artigo fornece as etapas para implantar o módulo de grade de eventos com persistência em implantações do Linux.

> [!NOTE]
>O módulo de grade de eventos é executado como um usuário com poucos privilégios com UID `2000` e nome `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Persistência por meio de montagem de volume

 Os [volumes do Docker](https://docs.docker.com/storage/volumes/) são usados para preservar os dados entre implantações. Você pode permitir que o Docker crie automaticamente um volume nomeado como parte da implantação do módulo de grade de eventos. Essa opção é a opção mais simples. Você pode especificar o nome do volume a ser criado na seção de **associações** da seguinte maneira:

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
>Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/metadataDb**.

Por exemplo, a configuração a seguir resultará na criação do volume **egmetadataDbVol** em que os metadados serão persistidos.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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

## <a name="persistence-via-host-directory-mount"></a>Persistência via montagem de diretório de host

Em vez de um volume do Docker, você também tem a opção de montar uma pasta de host.

1. Primeiro, crie um usuário com o nome **eventgriduser** e a ID **2000** no computador host executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de arquivos do host executando o comando a seguir.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, a execução do comando a seguir criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, torne o proprietário do **eventgriduser** desta pasta executando o comando a seguir.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Use **bindings** para montar o diretório e reimplantar o módulo de grade de eventos do portal do Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Por exemplo,

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
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
    >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/Metadata**.


## <a name="persist-events"></a>Persistir eventos

Para habilitar a persistência de evento, primeiro você deve habilitar a persistência de metadados por meio da montagem de volume ou da montagem de diretório de host usando as seções acima.

Coisas importantes a serem observadas sobre eventos persistentes:

* A persistência de eventos é habilitada por assinatura de evento e é opcional quando um volume ou diretório é montado.
* A persistência de evento é configurada em uma assinatura de evento no momento da criação e não pode ser modificada depois que a assinatura do evento é criada. Para alternar a persistência de evento, você deve excluir e recriar a assinatura de evento.
* A persistência de eventos é quase sempre mais lenta do que nas operações de memória, no entanto, a diferença de velocidade depende muito das características da unidade. A compensação entre velocidade e confiabilidade é inerente a todos os sistemas de mensagens, mas geralmente se torna apenas um noticible em grande escala.

Para habilitar a persistência de evento em uma assinatura de evento, defina `persistencePolicy` como `true`:

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