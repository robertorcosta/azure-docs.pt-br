---
title: Imagens do Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Saiba mais sobre imagens do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605472"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de uso.

> [!NOTE]
> Todas as imagens do RHEL estão disponíveis nas nuvens públicas do Azure e do Azure governamental. Eles não estão disponíveis nas nuvens do Azure na China.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis no Azure. Salvo indicação em contrário, todas as imagens são particionadas por LVM e anexadas a repositórios RHEL regulares (não EUS, não E4S). As seguintes imagens estão disponíveis no momento para uso geral:

Oferta| SKU | Particionamento | Provisionamento | Anotações
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente do Linux |
|             | 6,8      | RAW    | Agente do Linux |
|             | 6.9      | RAW    | Agente do Linux |
|             | 6.10     | RAW    | Agente do Linux |
|             | 7-RAW    | RAW    | Agente do Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7-LVM    | LVM    | Agente do Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS). Se você estiver procurando uma imagem RHEL padrão a ser implantada, use esse conjunto de imagens e/ou sua contraparte de geração 2.
|             | 7lvm-Gen2| LVM    | Agente do Linux | Família de imagens da geração 2, RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS). Se você estiver procurando uma imagem RHEL padrão a ser implantada, use esse conjunto de imagens e/ou sua contraparte de geração 1.
|             | 7-RAW-CI | RAW-CI | inicialização de nuvem  | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7.2      | RAW    | Agente do Linux |
|             | 7.3      | RAW    | Agente do Linux |
|             | 7.4      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de abril de 2019.
|             | 74-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.5      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de junho de 2019.
|             | 75-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.6      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de maio de 2019.
|             | 76-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7,7      | LVM    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 8-LVM    | LVM    | Agente do Linux | Família de imagens RHEL 8. x. Anexado a repositórios regulares.
|             | 8-LVM-Gen2| LVM    | Agente do Linux | Hyper-V geração 2-família de imagens RHEL 8. x. Anexado a repositórios regulares.
|             | 8        | LVM    | Agente do Linux | Imagens RHEL 8,0
|             | 8-Gen2   | LVM    | Agente do Linux | Hyper-V geração 2-RHEL 8,0 imagens.
|             | 8.1      | LVM    | Agente do Linux | Imagens RHEL 8,1. Atualmente anexado a repositórios regulares.
|             | 81gen2   | LVM    | Agente do Linux | Hyper-V geração 2-RHEL 8,1 imagens. Atualmente anexado a repositórios regulares.
RHEL-SAP      | 7.4      | LVM    | Agente do Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 74sap-Gen2| LVM    | Agente do Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 75sap-Gen2| LVM    | Agente do Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7,6 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 76sap-Gen2| LVM    | Agente do Linux | RHEL 7,6 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7,7       | LVM    | Agente do Linux | RHEL 7,7 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
RHEL-SAP-HANA | 6.7       | RAW    | Agente do Linux | RHEL 6,7 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.2       | LVM    | Agente do Linux | RHEL 7,2 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente do Linux | RHEL 7,3 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
RHEL-SAP-APPS | 6,8       | RAW    | Agente do Linux | RHEL 6,8 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente do Linux | RHEL 7,3 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
RHEL-HA       | 7.4       | LVM    | Agente do Linux | RHEL 7,4 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7,5 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7,6 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base.
RHEL-SAP-HA   | 7.4          | LVM    | Agente do Linux | RHEL 7,4 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 74sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,4 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.5          | LVM    | Agente do Linux | RHEL 7,5 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.6          | LVM    | Agente do Linux | RHEL 7,6 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 76sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,6 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7,7          | LVM    | Agente do Linux | RHEL 7,7 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 77sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,7 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
RHEL-BYOS     |RHEL-lvm74| LVM    | Agente do Linux | As imagens RHEL 7,4 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm75| LVM    | Agente do Linux | As imagens RHEL 7,5 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm76| LVM    | Agente do Linux | As imagens RHEL 7,6 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm77| LVM    | Agente do Linux | As imagens RHEL 7,7 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm8 | LVM    | Agente do Linux | As imagens RHEL 8 BYOS (a versão secundária do RHEL é mostrada no valor da versão da imagem), não anexada a nenhuma fonte de atualizações, não cobrará um RHEL Premium.

> [!NOTE]
> A oferta de produto RHEL-SAP-HANA é considerada fim da vida por Red Hat. As implantações existentes continuarão a funcionar normalmente, mas a Red Hat recomenda que os clientes migrem das imagens RHEL-SAP-HANA para as imagens RHEL-SAP-HA que incluem os repositórios SAP HANA, bem como o complemento de alta disponibilidade. Mais detalhes sobre as ofertas de nuvem do SAP do Red Hat estão disponíveis [aqui](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
