---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361538"
---
### <a name="bastion-tier"></a>Nível bastião

O host bastião é um componente opcional que você pode usar como um servidor de salto para acessar as instâncias de aplicativo e banco de dados. O VM do host bastião pode ter um endereço IP público atribuído a ele, embora a recomendação seja configurar uma conexão ExpressRoute ou VPN site-to-site com sua rede local para acesso seguro. Além disso, apenas O SSH (porta 22, Linux) ou RDP (porta 3389, Windows Server) deve ser aberto para tráfego de entrada. Para alta disponibilidade, implante um host bastião em duas zonas de disponibilidade ou em um único conjunto de disponibilidade.

Você também pode habilitar o encaminhamento de agentes SSH em suas VMs, o que permite que você acesse outras VMs na rede virtual encaminhando as credenciais do seu host bastião. Ou, use o túnel SSH para acessar outras instâncias.

Aqui está um exemplo de encaminhamento de agentes:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Este comando se conecta ao bastião `ssh` e, em seguida, é executado imediatamente novamente, para que você obtenha um terminal na instância de destino. Você pode precisar especificar um usuário diferente da raiz na instância de destino se o cluster estiver configurado de forma diferente. O `-A` argumento encaminha a conexão do agente para que sua chave privada na máquina local seja usada automaticamente. Observe que o encaminhamento do agente é `ssh` uma cadeia, então o segundo comando também inclui `-A` para que quaisquer conexões SSH subseqüentes iniciadas a partir da instância de destino também usem sua chave privada local.