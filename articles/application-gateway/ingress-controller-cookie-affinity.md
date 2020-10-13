---
title: Habilitar afinidade baseada em cookie com o gateway de aplicativo
description: Este artigo fornece informações sobre como habilitar a afinidade baseada em cookie com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807963"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Habilitar afinidade baseada em cookie com um gateway de aplicativo
Conforme descrito na [documentação do gateway de aplicativo Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), o gateway de aplicativo dá suporte à afinidade baseada em cookie, o que significa que ele pode direcionar o tráfego subsequente de uma sessão de usuário para o mesmo servidor para processamento.

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
