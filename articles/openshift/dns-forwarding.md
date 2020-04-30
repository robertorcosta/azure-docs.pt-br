---
title: Configurar o encaminhamento de DNS para o Azure Red Hat OpenShift 4
description: Configurar o encaminhamento de DNS para o Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232625"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configurar o encaminhamento de DNS em um cluster do Azure Red Hat OpenShift 4

Para configurar o encaminhamento de DNS em um cluster do Azure Red Hat OpenShift, você precisará modificar o operador DNS. Essa modificação permitirá que seu pods de aplicativo seja executado dentro do cluster para resolver nomes hospedados em um servidor DNS privado fora do cluster. Essas etapas estão documentadas para OpenShift 4,3 [aqui](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Por exemplo, se você quiser encaminhar todas as solicitações DNS para que *. example.com seja resolvido por um servidor DNS 192.168.100.10, você poderá editar a configuração do operador executando:
 
```bash
oc edit dns.operator/default
```
 
Isso iniciará um editor e você poderá substituir `spec: {}` por:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Próximas etapas
Confira mais informações sobre o encaminhamento de DNS para OpenShift 4,3 [aqui](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).