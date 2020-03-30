---
title: Anotações do controlador de entrada de gateway de aplicativo
description: Este artigo fornece documentação sobre as anotações específicas do Controlador de Entrada de Gateway de Aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335816"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anotações para controlador de entrada de gateway de aplicativo 

## <a name="introductions"></a>Introduções

O recurso Kubernetes Ingress pode ser anotado com pares de tecla/valor arbitrários. A AGIC conta com anotações para programar recursos do Application Gateway, que não são configuráveis através do YAML ingress. As anotações de ingress são aplicadas a todas as configurações HTTP, pools de backend e ouvintes derivados de um recurso de ingress.

## <a name="list-of-supported-annotations"></a>Lista de anotações suportadas

Para que um recurso ingress seja observado pela AGIC, `kubernetes.io/ingress.class: azure/application-gateway`ele deve ser **anotado** com . Só então a AGIC trabalhará com o recurso Ingress em questão.

| Chave de anotação | Tipo de valor | Valor Padrão | Valores Permitidos
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(segundos) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(segundos) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefixo de caminho backend

Esta anotação permite que o caminho de back-end especificado em um recurso de ingestão seja reescrito com prefixo especificado nesta anotação. Isso permite que os usuários exponham serviços cujos pontos finais são diferentes dos nomes de ponto final usados para expor um serviço em um recurso de ingestão.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
No exemplo acima, definimos um recurso de `go-server-ingress-bkprefix` ingestão nomeado `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` com uma anotação. A anotação informa o gateway do aplicativo para criar uma configuração `/hello` `/test/`HTTP, que terá uma substituição de prefixo de caminho para .

> [!NOTE] 
> No exemplo acima temos apenas uma regra definida. No entanto, as anotações são aplicáveis a todo o recurso de entrada, portanto, se um usuário tivesse definido várias regras, o prefixo de caminho back-end seria configurado para cada um dos caminhos especificados. Assim, se um usuário quer regras diferentes com prefixos de caminhos diferentes (mesmo para o mesmo serviço) ele precisaria definir diferentes recursos de entrada.

## <a name="tls-redirect"></a>Redirecionamento do TLS

O Gateway de [aplicativo pode ser configurado](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) para redirecionar automaticamente URLs HTTP para suas contrapartes HTTPS. Quando esta anotação estiver presente e o TLS estiver configurado corretamente, o controlador Kubernetes Ingress criará uma [regra de roteamento com uma configuração de redirecionamento](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) e aplicará as alterações no Gateway de aplicativo. O redirecionamento criado `301 Moved Permanently`será HTTP .

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Drenagem de conexão

`connection-draining`: Esta anotação permite que os usuários especifiquem se permitem a drenagem da conexão.
`connection-draining-timeout`: Esta anotação permite que os usuários especifiquem um tempo após o qual o Application Gateway encerrará as solicitações para o ponto final de backend de drenagem.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Afinidade baseada em cookies

Esta anotação permite especificar se permite permitir afinidade baseada em cookies.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Tempo Limite da Solicitação

Esta anotação permite especificar o tempo de intervalo da solicitação em segundos após o qual o Gateway do aplicativo falhará com a solicitação se a resposta não for recebida.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Use IP privado

Esta anotação nos permite especificar se exponha esse ponto final no IP privado do Gateway de aplicativo.

> [!NOTE]
> * O Application Gateway não suporta vários IPs na mesma porta (exemplo: 80/443). A entrada com anotação `appgw.ingress.kubernetes.io/use-private-ip: "false"` e `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` outra com on fará com que a AGIC falhe na atualização do Gateway de aplicativo.
> * Para o Application Gateway que não tem um `appgw.ingress.kubernetes.io/use-private-ip: "true"` IP privado, ingressa com será ignorado. Isso se refletirá nos registros do controlador e nos eventos `NoPrivateIP` de entrada para aqueles ingressante saem com aviso.


### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protocolo backend

Esta anotação nos permite especificar o protocolo que o Application Gateway deve usar enquanto conversa com os Pods. Protocolos suportados: `http`,`https`

> [!NOTE]
> * Embora os certificados auto-assinados sejam suportados no Application `https` Gateway, atualmente, o AGIC só suporta quando pods estão usando certificado assinado por um CA bem conhecido.
> * Certifique-se de não usar a porta 80 com HTTPS e a porta 443 com HTTP nos Pods.

### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```