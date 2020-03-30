---
title: Exponha um servidor WebSocket ao Gateway de Aplicativo
description: Este artigo fornece informações sobre como expor um servidor WebSocket ao Application Gateway com o controlador de entrada para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297825"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Exponha um servidor WebSocket ao Gateway de Aplicativo

Como descrito na documentação do Application Gateway v2 - ele [fornece suporte nativo para os protocolos WebSocket e HTTP/2](features.md#websocket-and-http2-traffic). Observe que, tanto para o Application Gateway quanto para o Kubernetes Ingress - não há configuração configurável pelo usuário para ativar ou desativar seletivamente o suporte ao WebSocket.

A implantação do Kubernetes YAML abaixo mostra a configuração mínima usada para implantar um servidor WebSocket, o que é o mesmo que implantar um servidor web regular:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Dado que todos os pré-requisitos são cumpridos, e você tem um Gateway de aplicativo controlado por um Kubernetes Ingress em seu AKS, a implantação `ws.contoso.com` acima resultaria em um servidor WebSockets exposto na porta 80 do IP público do seu Application Gateway e no domínio.

O seguinte comando cURL testaria a implantação do servidor WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Testes de saúde do WebSocket

Se sua implantação não definir explicitamente testes de saúde, o Application Gateway tentará um HTTP GET no ponto final do servidor WebSocket.
Dependendo da implementação do servidor[(aqui é um que amamos),](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)os cabeçalhos específicos do WebSocket podem ser necessários (por`Sec-Websocket-Version` exemplo).
Como o Application Gateway não adiciona cabeçalhos do WebSocket, a resposta do `400 Bad Request`teste de saúde do Application Gateway do servidor WebSocket provavelmente será .
Como resultado, o Application Gateway marcará seus pods como `502 Bad Gateway` insalubres, o que eventualmente resultará em um para os consumidores do servidor WebSocket.
Para evitar isso, você pode precisar adicionar um manipulador HTTP`/health` GET para `200 OK`uma verificação de saúde ao seu servidor (por exemplo, que retorna).
