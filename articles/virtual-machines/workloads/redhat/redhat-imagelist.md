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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133797"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Imagens do Red Hat Enterprise Linux (RHEL) disponíveis no Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de uso.

> [!NOTE]
> Todas as imagens RHEL estão disponíveis em nuvens públicas do Azure e do Governo Azure. Eles não estão disponíveis nas nuvens do Azure China.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis no Azure. Salvo indicação em contrário, todas as imagens são particionadas por LVM e anexadas a repositórios RHEL regulares (não eus, não E4S). As seguintes imagens estão disponíveis atualmente para uso geral:

Oferta| SKU | Particionamento | Provisionamento | Observações
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente do Linux |
|             | 6,8      | RAW    | Agente do Linux |
|             | 6.9      | RAW    | Agente do Linux |
|             | 6.10     | RAW    | Agente do Linux |
|             | 7-RAW    | RAW    | Agente do Linux | RHEL 7.x família de imagens. <br> Anexado a repositórios regulares por padrão (não eus).
|             | 7-LVM    | LVM    | Agente do Linux | RHEL 7.x família de imagens. <br> Anexado a repositórios regulares por padrão (não eus). Se você estiver procurando uma imagem RHEL padrão para implantar, use este conjunto de imagens e/ou sua contraparte da Geração 2.
|             | 7lvm-gen2| LVM    | Agente do Linux | Geração 2, RHEL 7.x família de imagens. <br> Anexado a repositórios regulares por padrão (não eus). Se você estiver procurando uma imagem RHEL padrão para implantar, use este conjunto de imagens e/ou sua contraparte da Geração 1.
|             | 7-RAW-CI | RAW-CI | inicialização de nuvem  | RHEL 7.x família de imagens. <br> Anexado a repositórios regulares por padrão (não eus).
|             | 7.2      | RAW    | Agente do Linux |
|             | 7.3      | RAW    | Agente do Linux |
|             | 7.4      | RAW    | Agente do Linux | Anexado aos repositórios da EUS por padrão a partir de abril de 2019.
|             | 74-gen2  | RAW    | Agente do Linux | Anexado aos repositórios eus por padrão.
|             | 7.5      | RAW    | Agente do Linux | Anexado aos repositórios da EUS por padrão a partir de junho de 2019.
|             | 75-gen2  | RAW    | Agente do Linux | Anexado aos repositórios eus por padrão.
|             | 7.6      | RAW    | Agente do Linux | Anexado aos repositórios da EUS por padrão a partir de maio de 2019.
|             | 76-gen2  | RAW    | Agente do Linux | Anexado aos repositórios eus por padrão.
|             | 7.7      | LVM    | Agente do Linux | Anexado aos repositórios eus por padrão.
|             | 8        | LVM    | Agente do Linux | RHEL 8.x família de imagens
|             | 8-gen2   | LVM    | Agente do Linux | Hyper-V Geração 2 - RHEL 8.x família de imagens.
RHEL-SAP      | 7.4      | LVM    | Agente do Linux | RHEL 7.4 para SAP HANA e Aplicativos de Negócios. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 74sap-gen2| LVM    | Agente do Linux | RHEL 7.4 para SAP HANA e Aplicativos de Negócios. Imagem da Geração 2. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7.5 para SAP HANA e Aplicativos de Negócios. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 75sap-gen2| LVM    | Agente do Linux | RHEL 7.5 para SAP HANA e Aplicativos de Negócios. Imagem da Geração 2. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7.6 para SAP HANA e Aplicativos de Negócios. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 76sap-gen2| LVM    | Agente do Linux | RHEL 7.6 para SAP HANA e Aplicativos de Negócios. Imagem da Geração 2. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
|             | 7.7       | LVM    | Agente do Linux | RHEL 7.7 para SAP HANA e Aplicativos de Negócios. Anexado aos repositórios E4S, cobrará um prêmio por SAP e RHEL, bem como a taxa de computação base.
RHEL-SAP-HANA | 6.7       | RAW    | Agente do Linux | RHEL 6.7 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.2       | LVM    | Agente do Linux | RHEL 7.2 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente do Linux | RHEL 7.3 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP.
RHEL-SAP-APPS | 6,8       | RAW    | Agente do Linux | RHEL 6.8 para aplicações de negócios SAP. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente do Linux | RHEL 7.3 para aplicações de negócios SAP. Desatualizado em favor das imagens RHEL-SAP.
RHEL-HA       | 7.4       | LVM    | Agente do Linux | RHEL 7.4 com Add-On HA. Cobrará um prêmio por HA e RHEL em cima da taxa de computação base.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7.5 com Add-On HA. Cobrará um prêmio por HA e RHEL em cima da taxa de computação base.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7.6 com Add-On HA. Cobrará um prêmio por HA e RHEL em cima da taxa de computação base.
RHEL-SAP-HA   | 7.4          | LVM    | Agente do Linux | RHEL 7.4 para SAP com HA e Serviços de Atualização. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 74sapha-gen2 | LVM    | Agente do Linux | RHEL 7.4 para SAP com HA e Serviços de Atualização. Imagem da Geração 2. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 7.5          | LVM    | Agente do Linux | RHEL 7.5 para SAP com HA e Serviços de Atualização. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 7.6          | LVM    | Agente do Linux | RHEL 7.6 para SAP com HA e Serviços de Atualização. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 76sapha-gen2 | LVM    | Agente do Linux | RHEL 7.6 para SAP com HA e Serviços de Atualização. Imagem da Geração 2. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 7.7          | LVM    | Agente do Linux | RHEL 7.7 para SAP com HA e Serviços de Atualização. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
|             | 77sapha-gen2 | LVM    | Agente do Linux | RHEL 7.7 para SAP com HA e Serviços de Atualização. Imagem da Geração 2. Anexado aos repositórios E4S. Cobrará um prêmio por repositórios SAP e HA, bem como RHEL, além das taxas de computação base.
rhel-byos     |rhel-lvm74| LVM    | Agente do Linux | As imagens RHEL 7.4 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prêmio RHEL.
|             |rhel-lvm75| LVM    | Agente do Linux | As imagens RHEL 7.5 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prêmio RHEL.
|             |rhel-lvm76| LVM    | Agente do Linux | As imagens RHEL 7.6 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prêmio RHEL.
|             |rhel-lvm77| LVM    | Agente do Linux | As imagens RHEL 7.7 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um prêmio RHEL.
|             |rhel-lvm8 | LVM    | Agente do Linux | Imagens RHEL 8 BYOS (a versão menor RHEL é mostrada no valor da versão da imagem), não anexadas a nenhuma fonte de atualizações, não cobrarão um prêmio RHEL.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as imagens do [Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [Infra-estrutura de atualização](./redhat-rhui.md)do Chapéu Vermelho .
* Saiba mais sobre a [oferta rhel BYOS](./byos.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
