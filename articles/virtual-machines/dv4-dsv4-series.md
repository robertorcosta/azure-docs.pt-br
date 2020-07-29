---
title: Dv4 e série Dsv4-máquinas virtuais do Azure
description: Especificações para as VMs da série dv4 e Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 68cd6673283362380fc5a1f4b780f0a22aa53402
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783505"
---
# <a name="dv4-and-dsv4-series"></a>Séries Dv4 e Dsv4

A dv4 e a série Dsv4 são executadas nos &reg; processadores Intel Xeon &reg; Platinum 8272CL (cascadey Lake) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz. 

> [!NOTE]
> Para perguntas frequentes, consulte [tamanhos de VM do Azure sem disco temporário local](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Série dv4

Os tamanhos da série dv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (cascadey Lake). Os tamanhos da série dv4 oferecem uma combinação de vCPU, memória e opções de armazenamento remoto para a maioria das cargas de trabalho de produção. As VMs da série dv4 [apresentam &reg; a tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais. Para usar discos de armazenamento Premium, use os tamanhos de Dsv4. Os medidores de cobrança e preço para os tamanhos de Dsv4 são os mesmos que os da série dv4.


> [!IMPORTANT]
> Esses novos tamanhos estão atualmente em visualização pública apenas. Você pode inscrever-se para essas dv4 e a série Dsv4 [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 


ACU: 195 a 210

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Somente armazenamento remoto | 4 | 2/1000 |
| Standard_D4_v4 | 4 | 16  | Somente armazenamento remoto | 8 | 2/2000 |
| Standard_D8_v4 | 8 | 32 | Somente armazenamento remoto | 16 | 4/4000 |
| Standard_D16_v4 | 16 | 64 | Somente armazenamento remoto | 32 | 8/8000 |
| Standard_D32_v4 | 32 | 128 | Somente armazenamento remoto | 32 | 8/16000 |
| Standard_D48_v4 | 48 | 192 | Somente armazenamento remoto | 32 | 8/24000 |
| Standard_D64_v4 | 64 | 256 | Somente armazenamento remoto | 32 | 8/30000 |

## <a name="dsv4-series"></a>Série Dsv4

Os tamanhos da série Dsv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (cascadey Lake). Os tamanhos da série dv4 oferecem uma combinação de vCPU, memória e opções de armazenamento remoto para a maioria das cargas de trabalho de produção. As VMs da série Dsv4 [apresentam &reg; a tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais.

> [!IMPORTANT]
> Esses novos tamanhos estão atualmente em visualização pública apenas. Você pode inscrever-se para essas dv4 e a série Dsv4 [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195 a 210

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima em cache: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Somente armazenamento remoto | 4 | 19000/120 (50) | 3000/48 | 2/1000 |
| Standard_D4s_v4 | 4 | 16 | Somente armazenamento remoto | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_D8s_v4 | 8 | 32 | Somente armazenamento remoto | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_D16s_v4 | 16 | 64  | Somente armazenamento remoto | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_D32s_v4 | 32 | 128 | Somente armazenamento remoto | 32 | 308000/1936 (800) | 51200/768 | 8/16000 |
| Standard_D48s_v4 | 48 | 192 | Somente armazenamento remoto | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_D64s_v4 | 64 | 256 | Somente armazenamento remoto | 32 | 615000/3872 (1600) | 80000/1200 | 8/30000 |
