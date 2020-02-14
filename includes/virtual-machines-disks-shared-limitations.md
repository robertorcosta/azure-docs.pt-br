---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202210"
---
Durante a visualização, os discos gerenciados que têm discos compartilhados habilitados estão sujeitos às seguintes limitações:

- Atualmente, disponível apenas com SSDs Premium.
- Atualmente, só tem suporte na região EUA Central ocidental.
- Todas as máquinas virtuais que compartilham um disco devem ser implantadas nos mesmos [grupos de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Só pode ser habilitado em discos de dados, não em discos do sistema operacional.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O cache de host ReadOnly não está disponível para SSDs Premium com `maxShares>1`.
- Conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais só podem ser usados com `FaultDomainCount` definido como 1.
- O backup do Azure e o suporte a Azure Site Recovery ainda não estão disponíveis.

Se você estiver interessado em tentar discos compartilhados, [Inscreva-se para nossa versão prévia](https://aka.ms/shareddisksignup).
