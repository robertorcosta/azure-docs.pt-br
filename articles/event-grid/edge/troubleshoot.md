---
title: Solução de problemas - Azure Event Grid IoT Edge | Microsoft Docs
description: Solução de problemas no Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100151"
---
# <a name="common-issues"></a>Problemas comuns

Se você tiver problemas usando o Azure Event Grid no IoT Edge em seu ambiente, use este artigo como um guia para solução de problemas e resolução.

## <a name="view-event-grid-module-logs"></a>Exibir registros do módulo Event Grid

Para solucionar problemas, talvez seja necessário acessar os registros do módulo Event Grid. Para fazer isso, na VM onde o módulo é implantado execute o seguinte comando:

No Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

No Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Não é possível fazer solicitações HTTPS

* Primeiro certifique-se de que o módulo Event Grid tenha **entrada:serverAuth:tlsPolicy** definido **como rigoroso** ou **habilitado**.

* Se as comunicações módulo-módulo, certifique-se de que você está fazendo a chamada na porta **4438** e o nome do módulo corresponde ao que é implantado. 

  Por exemplo, se o módulo Event Grid foi implantado com nome **https://eventgridmodule:4438** **eventgridmodule,** então sua URL deve ser . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for de um módulo não IoT, certifique-se de que a porta Event Grid esteja mapeada na máquina Host durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Não é possível fazer solicitações HTTP

* Primeiro certifique-se de que o módulo Event Grid tenha **entrada:serverAuth:tlsPolicy** definido para **habilitar** ou **desativar**.

* Se suas comunicações módulo-módulo, certifique-se de que você está fazendo a chamada na porta **5888** e o nome do módulo corresponde ao que é implantado. 

  Por exemplo, se o módulo Event Grid foi implantado com nome **http://eventgridmodule:5888** **eventgridmodule,** então sua URL deve ser . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for de um módulo não IoT, certifique-se de que a porta Event Grid esteja mapeada na máquina Host durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Cadeia de certificados foi emitida por uma autoridade que não é confiável

Por padrão, o módulo Event Grid é configurado para autenticar clientes com certificado emitido pelo daemon de segurança IoT Edge. Certifique-se de que o cliente está apresentando um certificado que está enraizado nesta cadeia.

A classe **IoTSecurity** mostra [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) como recuperar certificados do daemon ioT Edge Security e usá-lo para configurar chamadas de saída.

Se não for ambiente de produção, você tem a opção de desativar a autenticação do cliente. Consulte [Segurança e Autenticação](security-authentication.md) para obter detalhes sobre como fazer isso.

## <a name="debug-events-not-received-by-subscriber"></a>Eventos de depuração não recebidos pelo assinante

As razões típicas para isso são:

* O evento nunca foi publicado com sucesso. Um HTTP StatusCode de 200(OK) deve ser recebido na postagem de um evento no módulo Event Grid.

* Verifique a assinatura do evento para verificar:
    * A URL do ponto final é válida
    * Quaisquer filtros na assinatura não estão fazendo com que o evento seja "descartado".

* Verifique se o módulo do assinante está em execução

* Faça logon na VM onde o módulo Event Grid é implantado e visualize seus registros.

* Ative o registro por entrega definindo **o corretor:logDeliverySuccess=true** e reimplantando o módulo Event Grid e refazendo a solicitação. Ativar o registro por entrega pode afetar o throughput e a latência, portanto, uma vez que a depuração esteja completa, nossa recomendação é devolver isso ao **corretor:logDeliverySuccess=false** e reimplantando o módulo Event Grid.

* Ative as métricas definindo **métricas:reportertype=console** e reimplante o módulo Event Grid. Qualquer operação depois disso resultará em métricas sendo registradas no console do módulo Event Grid, que pode ser usado para depurar ainda mais. Nossa recomendação é ativar métricas apenas para depuração e uma vez concluída para desatimá-la definindo **métricas:reportertype=none** e reimplantando o módulo Event Grid.

## <a name="next-steps"></a>Próximas etapas

Reporte quaisquer problemas, sugestões com o uso [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)do Event Grid no IoT Edge em .