---
title: Matriz de compatibilidade do sistema operacional para SAP HANA (instâncias grandes) | Microsoft Docs
description: A matriz de compatibilidade representa a compatibilidade de diferentes versões do sistema operacional com tipos de hardware diferentes (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675634"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemas operacionais compatíveis para instâncias grandes HANA

## <a name="hana-large-instance-type-i"></a>Tipo de instância grande HANA I     
  | Sistema operacional | Disponibilidade        | SKUs                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Não é mais oferecido | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>SKUs de memória persistente
  | Sistema operacional | Disponibilidade | SKUs                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponível    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Tipo de instância grande do HANA II     
  |  Sistema operacional       | Disponibilidade        | SKUs                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Não é mais oferecido | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Documentos relacionados

- Para saber mais sobre os [SKUs disponíveis](hana-available-skus.md)
- Para saber mais sobre [a atualização do sistema operacional](os-upgrade-hana-large-instance.md)
  

  
  
