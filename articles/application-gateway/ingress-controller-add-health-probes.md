---
title: Adicionar investigações de integridade ao seu pods AKS
description: Este artigo fornece informações sobre como adicionar investigações de integridade (prontidão e/ou vida) a AKS pods com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795588"
---
# <a name="add-health-probes-to-your-service"></a>Adicionar investigações de integridade ao seu serviço
Por padrão, o controlador de entrada provisionará uma investigação HTTP GET para o pods exposto.
As propriedades da investigação podem ser personalizadas com a adição de uma [investigação de preparação ou dinâmica](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) à sua `deployment` / `pod` especificação.

## <a name="with-readinessprobe-or-livenessprobe"></a>Com `readinessProbe` ou`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Referência da API do kubernetes:
* [Investigações de contêiner](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Ação HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`e `livenessProbe` têm suporte quando configurado com `httpGet`o.
> * No momento, não há suporte para investigação em uma porta diferente daquela exposta no pod.
> * `HttpHeaders`, `InitialDelaySeconds`, `SuccessThreshold` não tem suporte.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem `readinessProbe` ou`livenessProbe`
Se as investigações acima não forem fornecidas, o controlador de entrada fará uma suposição de que o serviço pode ser `Path` acessado no `backend-path-prefix` especificado `path` para a anotação `ingress` ou o especificado na definição do serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para investigação de integridade
Para qualquer propriedade que não pode ser inferida pela investigação de preparação/vida, os valores padrão são definidos.

| Propriedade de investigação do gateway de aplicativo | Valor Padrão |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |