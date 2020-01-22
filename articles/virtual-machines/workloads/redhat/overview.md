---
title: Visão geral de cargas de trabalho do Red Hat no Azure | Microsoft Docs
description: Saiba mais sobre as ofertas de produtos do Red Hat disponíveis no Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 8ca249a5f6c300a39548e4e16927d7a20acae1a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942614"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de trabalho do Red Hat no Azure
As cargas de trabalho do Red Hat são compatíveis por meio de uma variedade de ofertas no Azure. As imagens do Red Hat Enterprise Linux (RHEL) estão no núcleo das cargas de trabalho do RHEL, assim como a RHUI (Infraestrutura de atualização do Red Hat).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Imagens do Red Hat Enterprise Linux (RHEL)
O Azure oferece uma ampla gama de imagens do RHEL no Azure. Essas imagens são disponibilizadas por meio de dois modelos de licenciamento diferentes: PAYG (Pagamento Conforme o Uso) e BYOS (traga sua própria assinatura). As novas imagens do RHEL no Azure são publicadas quando novas versões do RHEL são lançadas e atualizadas em todo o ciclo de vida conforme necessário.

### <a name="pay-as-you-go-payg-images"></a>Imagens de PAYG (Pagamento Conforme o Uso)
O Azure oferece uma variedade de imagens PAYG do RHEL. Essas imagens vêm adequadamente qualificadas para o RHEL e são anexadas a uma fonte de atualizações (Infraestrutura de Atualização do Red Hat). Essas imagens cobrarão um valor Premium para o direito e as atualizações do RHEL. As variantes da imagem PAYG do RHEL incluem:
* RHEL Standard
* RHEL for SAP
* RHEL for SAP com serviços de alta disponibilidade e atualização.

Você pode optar por usar as imagens PAYG caso não queira se preocupar com o pagamento separado para o número apropriado de assinaturas.

### <a name="red-hat-gold-images-rhel-byos"></a>Imagens do Red Hat Gold (`rhel-byos`)
O Azure também oferece imagens do Red Hat Gold. Essas imagens podem ser úteis para clientes que têm assinaturas do Red Hat existentes e desejam usá-las no Azure. Você precisa habilitar suas assinaturas existentes do Red Hat para ter acesso ao Red Hat Cloud antes de poder usá-las no Azure. O acesso a essas imagens é concedido automaticamente quando suas assinaturas do Red Hat são habilitadas para Acesso à Nuvem e atendem aos requisitos de qualificação. O uso dessas imagens permite que um cliente evite a cobrança dupla que poderia ocorrer usando as imagens PAYG.
* [Saiba como habilitar suas assinaturas do Red Hat para Acesso à Nuvem com o Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Saiba como localizar imagens do Red Hat Gold no portal do Azure, na CLI ou no Cmdlet do PowerShell](./byos.md)

> [!NOTE]
> Observação sobre a cobrança dupla: A cobrança dupla incorre quando um usuário paga duas vezes por assinaturas do RHEL. Isso geralmente acontece quando um cliente usa o gerenciador de assinaturas para anexar um direito em uma VM RHEL PAYG. Por exemplo, um cliente que usa o gerenciador de assinaturas para anexar um direito para pacotes do SAP em uma imagem RHEL PAYG será indiretamente cobrado duas vezes, pois pagará duas vezes pelo RHEL: uma por meio do valor do PAYG Premium e uma vez por meio de sua assinatura do SAP. Isso não ocorrerá para usuários da imagem BYOS.

## <a name="red-hat-update-infrastructure-rhui"></a>RHUI (Infraestrutura de Atualização do Red Hat)
O Azure fornece a Infraestrutura de Atualização do Red Hat somente para VMs (máquinas virtuais) RHEL PAYG. A RHUI é efetivamente um espelho das CDNs do Red Hat, mas é acessível apenas para VMs RHEL PAYG do Azure. Você terá acesso aos pacotes apropriados dependendo da imagem RHEL implantada. Por exemplo, uma imagem RHEL for SAP terá acesso aos pacotes SAP, além dos pacotes básicos do RHEL.

### <a name="rhui-update-behavior"></a>Comportamento de atualização da RHUI
As imagens RHEL conectadas à RHUI serão, por padrão, atualizadas para a versão secundária mais recente do RHEL quando um `yum update` for executado. Esse comportamento significa que uma VM RHEL 7.4 poderá ser atualizada para RHEL 7.7 se uma operação `yum update` for executada nela. Esse comportamento é intencional da RHUI. No entanto, esse comportamento de atualização pode ser mitigado alternando de repositórios RHEL regulares para os [repositórios de EUS (Suporte de Atualização Estendida)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [imagens RHEL no Azure](./redhat-images.md)
* Saiba mais sobre a [Infraestrutura de Atualização do Red Hat](./redhat-rhui.md)
* Saiba mais sobre a [Oferta do Red Hat Gold Image (`rhel-byos`)](./byos.md)
