---
title: Análise de Sentimento Kubernetes config e implantar etapas
titleSuffix: Azure Cognitive Services
description: Análise de Sentimento Kubernetes config e implantar etapas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877792"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Implantar o contêiner de análise de sentimento em um cluster AKS

1. Abra o Azure CLI e entre no Azure.

    ```azurecli
    az login
    ```

1. Faça login no cluster AKS. `your-cluster-name` Substitua `your-resource-group` e com os valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Depois que este comando é executado, ele relata uma mensagem semelhante à seguinte:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se você tiver várias assinaturas disponíveis para você `az aks get-credentials` em sua conta do Azure e o comando retornar com um erro, um problema comum é que você está usando a assinatura errada. Defina o contexto da sessão CLI do Azure para usar a mesma assinatura com a que você criou os recursos e tente novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto escolhido. Este exemplo usa o Visual Studio Code.

    ```console
    code .
    ```

1. Dentro do editor de texto, crie um novo arquivo chamado *sentiment.yaml*e cole o Seguinte YAML nele. Certifique-se `billing/value` de `apikey/value` substituir e com suas próprias informações.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Salve o arquivo e feche o editor de texto.
1. Execute o comando `apply` Kubernetes com o arquivo *sentiment.yaml* como seu alvo:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Depois que o comando aplica com sucesso a configuração de implantação, uma mensagem aparece semelhante à seguinte saída:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verifique se a cápsula foi implantada:

    ```console
    kubectl get pods
    ```

    A saída para o status de execução do pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída para o status de execução do serviço de *sentimento* no pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
