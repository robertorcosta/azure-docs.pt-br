---
title: Habilite a afinidade baseada em cookies com o Application Gateway
description: Este artigo fornece informações sobre como ativar a afinidade baseada em cookies com um Gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795985"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Habilite a afinidade baseada em cookies com um gateway de aplicativo
Como descrito na Documentação do [Gateway do Aplicativo Azure,](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)o Application Gateway suporta afinidade baseada em cookies, o que significa que ele pode direcionar o tráfego subseqüente de uma sessão de usuário para o mesmo servidor para processamento.

## <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
