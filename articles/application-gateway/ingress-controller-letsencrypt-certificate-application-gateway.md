---
title: Use certificados de LetsEncrypt.org com o Gateway de Aplicativo
description: Este artigo fornece informações sobre como obter um certificado de LetsEncrypt.org e usá-lo em seu Gateway de aplicativo para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957977"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Use certificados com LetsEncrypt.org no Gateway de aplicativo para clusters AKS

Esta seção configura seu AKS para aproveitar [LetsEncrypt.org](https://letsencrypt.org/) e obter automaticamente um certificado TLS/SSL para o seu domínio. O certificado será instalado no Application Gateway, que executará o término do SSL/TLS para o cluster AKS. A configuração descrita aqui utiliza o [complemento do Ceu-Manager](https://github.com/jetstack/cert-manager) Kubernetes, que automatiza a criação e o gerenciamento de certificados.

Siga as etapas abaixo para instalar [o cert-manager](https://docs.cert-manager.io) no cluster AKS existente.

1. Gráfico de Leme

    Execute o seguinte script `cert-manager` para instalar o gráfico de leme. Isso vai:

    - criar um `cert-manager` novo namespace em seu AKS
    - criar os seguintes CRDs: Certificado, Desafio, Emissor de Cluster, Emissor, Pedido
    - instalar o gráfico cert-manager (de [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Recurso de emissor de cluster

    Crie `ClusterIssuer` um recurso. É necessário `cert-manager` representar a `Lets Encrypt` autoridade certificadora onde os certificados assinados serão obtidos.

    Usando o recurso sem `ClusterIssuer` namespaced, o cert-manager emitirá certificados que podem ser consumidos de vários namespaces. `Let’s Encrypt`usa o protocolo ACME para verificar se você controla um determinado nome de domínio e para emitir um certificado. Mais detalhes `ClusterIssuer` sobre a configuração de propriedades [aqui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`instruirão `cert-manager` a emissão `Lets Encrypt` de certificados usando o ambiente de preparação usado para testes (o certificado raiz não presente nas lojas de confiança do navegador/cliente).

    O tipo de desafio padrão no `http01`YAML abaixo é . Outros desafios são documentados em [letsencrypt.org - Tipos de Desafios](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Atualização `<YOUR.EMAIL@ADDRESS>` no YAML abaixo

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Implantar o Aplicativo

    Crie um recurso Ingress `guestbook` para expor o aplicativo usando o Gateway de aplicativo com o Certificado Lets Encrypt.

    Certifique-se de que o Gateway de aplicativos tenha uma configuração ip do Frontend pública com um nome DNS (usando o domínio padrão `azure.com` ou prover um `Azure DNS Zone` serviço e atribuir seu próprio domínio personalizado).
    Observe a anotação `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, que diz ao cert-manager para processar o recurso Ingress marcado.

    > [!IMPORTANT] 
    > Atualize-se `<PLACEHOLDERS.COM>` no YAML abaixo com seu próprio domínio (ou o Gateway de aplicativo, por exemplo , 'kh-aks-ingress.westeurope.cloudapp.azure.com')

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Após alguns segundos, você `guestbook` pode acessar o serviço através do url HTTPS do Application Gateway usando o certificado **de estágio** `Lets Encrypt` emitido automaticamente.
    Seu navegador pode avisá-lo de uma autoridade cert inválida. O certificado de encenação é emitido por `CN=Fake LE Intermediate X1`. Isso é uma indicação de que o sistema funcionou como esperado e você está pronto para o seu certificado de produção.

4. Certificado de Produção

    Uma vez que o certificado de estágio esteja configurado com sucesso, você pode mudar para um servidor ACME de produção:
    1. Substitua a anotação de encenação no recurso Ingress por:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Exclua o `ClusterIssuer` estágio existente criado na etapa anterior e crie um novo substituindo o servidor ACME do ClusterIssuer YAML acima por`https://acme-v02.api.letsencrypt.org/directory`

5. Expiração e Renovação de Certificados

    Antes `Lets Encrypt` de o `cert-manager` certificado expirar, atualizará automaticamente o certificado na loja secreta Kubernetes. Nesse ponto, o Application Gateway Ingress Controller aplicará o segredo atualizado referenciado nos recursos de entrada que está usando para configurar o Gateway de aplicativo.
