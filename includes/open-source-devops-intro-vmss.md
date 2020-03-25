---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: 3b639e0dcd852ddf34c4ce29292256b1285b2574
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172611"
---
Os [conjuntos de dimensionamento de máquinas virtuais do Azure](../articles/virtual-machine-scale-sets/overview.md) são um recurso do Azure que permite configurar um grupo de VMs idênticas e com balanceamento de carga. Não há nenhum custo adicional para os conjuntos de dimensionamento e eles são criados usando máquinas virtuais. Você paga apenas pelos recursos de computação subjacentes, como as instâncias de VM, os balanceadores de carga ou o armazenamento do Managed Disks. Com conjuntos de dimensionamento, as camadas de automação e gerenciamento são fornecidas para executar e dimensionar seus aplicativos. Em vez disso, você poderia criar e gerenciar VMs individuais manualmente. No entanto, há dois benefícios principais ao usar conjuntos de dimensionamento. Eles são criados no Azure e eles dimensionam automaticamente suas máquinas virtuais para atender às necessidades do aplicativo.