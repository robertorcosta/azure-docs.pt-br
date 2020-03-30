---
title: Exponha um serviço AKS em HTTP ou HTTPS usando o Gateway de aplicativo
description: Este artigo fornece informações sobre como expor um serviço AKS através de HTTP ou HTTPS usando o Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795583"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Exponha um serviço AKS em HTTP ou HTTPS usando o Gateway de aplicativo 

Esses tutoriais ajudam a ilustrar o uso do [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) para expor um exemplo de serviço Kubernetes através do [Gateway de aplicativo Azure](https://azure.microsoft.com/services/application-gateway/) em HTTP ou HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

- Gráfico `ingress-azure` de leme instalado.
  - [**Implantação greenfield**](ingress-controller-install-new.md): Se você estiver começando do zero, consulte estas instruções de instalação, que delineiam etapas para implantar um cluster AKS com o Application Gateway e instalar o controlador de entrada de gateway de aplicativo no cluster AKS.
  - [**Implantação de campo marrom**](ingress-controller-install-existing.md): Se você tiver um cluster AKS existente e gateway de aplicativo, consulte estas instruções para instalar o controlador de entrada de gateway de aplicativo no cluster AKS.
- Se você quiser usar HTTPS neste aplicativo, você precisará de um certificado x509 e sua chave privada.

## <a name="deploy-guestbook-application"></a>Implantar `guestbook` aplicativo

O aplicativo guestbook é um aplicativo canônico kubernetes que compõe um frontend da Web UI, um backend e um banco de dados Redis. Por padrão, `guestbook` expõe sua aplicação através `frontend` de `80`um serviço com nome na porta . Sem um Recurso Kubernetes Ingress, o serviço não é acessível de fora do cluster AKS. Usaremos o aplicativo e a configuração da Ingress Resources para acessar o aplicativo através de HTTP e HTTPS.

Siga as instruções abaixo para implantar o aplicativo guestbook.

1. Baixe `guestbook-all-in-one.yaml` [a](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml) partir daqui
1. Implantar `guestbook-all-in-one.yaml` em seu cluster AKS executando

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Agora, `guestbook` a aplicação foi implantada.

## <a name="expose-services-over-http"></a>Expor serviços em HTTP

Para expor o aplicativo guestbook, usaremos o seguinte recurso de ingestão:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Essa entrada exporá o `frontend` serviço `guestbook-all-in-one` da implantação como um backend padrão do Gateway de aplicativo.

Salve o recurso de ingestão acima como `ing-guestbook.yaml`.

1. Implantar `ing-guestbook.yaml` executando:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Verifique o registro do controlador de entrada para obter o status de implantação.

Agora, `guestbook` a aplicação deve estar disponível. Você pode verificar isso visitando o endereço público do Gateway de aplicativo.

## <a name="expose-services-over-https"></a>Expor serviços em HTTPS

### <a name="without-specified-hostname"></a>Sem nome de host especificado

Sem especificar o nome do host, o serviço de guestbook estará disponível em todos os nomes de host que apontam para o gateway do aplicativo.

1. Antes de implantar o ingresss, você precisa criar um segredo kubernetes para hospedar o certificado e a chave privada. Você pode criar um segredo kubernetes executando

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Defina a seguinte ingestão. Na ingestão, especifique o nome `secretName` do segredo na seção.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Substitua `<guestbook-secret-name>` no recurso de Ingress acima o nome do seu segredo. Armazene o recurso ingresss acima em um nome `ing-guestbook-tls.yaml`de arquivo .

1. Implantar-guestbook-tls.yaml executando

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Verifique o registro do controlador de entrada para obter o status de implantação.

Agora, `guestbook` o aplicativo estará disponível tanto em HTTP quanto em HTTPS.

### <a name="with-specified-hostname"></a>Com nome de host especificado

Você também pode especificar o nome de host na entrada para várias configurações e serviços TLS.
Ao especificar o nome do host, o serviço de guestbook só estará disponível no host especificado.

1. Defina a seguinte ingestão.
    Na ingresss, especifique o `secretName` nome do segredo na `hosts` seção e substitua o nome de host na seção de acordo.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implantar `ing-guestbook-tls-sni.yaml` executando

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Verifique o registro do controlador de entrada para obter o status de implantação.

Agora, `guestbook` o aplicativo estará disponível tanto em HTTP`<guestbook.contoso.com>` quanto https apenas no host especificado (neste exemplo).

## <a name="integrate-with-other-services"></a>Integrar com outros serviços

A seguinte entrada permitirá que você adicione caminhos adicionais a essa entrada e redirecione esses caminhos para outros serviços:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
