---
title: Adicione testes de saúde aos seus pods AKS
description: Este artigo fornece informações sobre como adicionar testes de saúde (prontidão e/ou vida) a pods AKS com um Gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795588"
---
# <a name="add-health-probes-to-your-service"></a>Adicione sondas de saúde ao seu serviço
Por padrão, o controlador Ingress irá provisionar um teste HTTP GET para os pods expostos.
As propriedades do teste podem ser personalizadas adicionando um [teste de prontidão ou de vida](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) à sua `deployment` / `pod` especificação.

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

Referência da API do Kubernetes:
* [Sondas de contêineres](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet Action](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`e `livenessProbe` são suportados quando `httpGet`configurados com .
> * A sondagem em uma porta diferente da exposta na cápsula não é suportada no momento.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` não são suportados.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem `readinessProbe` ou`livenessProbe`
Se os testes acima não forem fornecidos, então o Controlador de Ingress faz uma suposição de que o serviço é acessível em `Path` especificado para `backend-path-prefix` anotação `path` `ingress` ou o especificado na definição para o serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para sonda de saúde
Para qualquer propriedade que não possa ser inferida pelo teste de prontidão/vida, os valores padrão são definidos.

| Propriedade do teste do gateway do aplicativo | Valor Padrão |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |