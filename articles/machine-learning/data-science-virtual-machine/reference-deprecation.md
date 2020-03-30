---
title: 'Referência: Depreciação da imagem da máquina virtual de data science'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre depreciações que afetam a Máquina Virtual de Data Science do Azure
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525780"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: Depreciação de Imagens DSVM

Abaixo discutimos sugestões para lidar com as próximas depreciações na Máquina Virtual de Data Science do Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Discos de dados migratórios

Vamos parar de suportar a imagem DSVM do Windows 2012 em 31 de dezembro de 2019. Para migrar um disco de dados do DSVM do Windows 2012 existente para um DSVM do Windows 2016, tome as seguintes etapas:

1. Crie um novo DSVM do Windows 2016, seguindo as instruções [aqui](./provision-vm.md#create-your-dsvm)mostradas .
1. Desconecte os discos de dados existentes da imagem do Windows 2012 usando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Conecte o disco da etapa anterior à imagem do Windows 2016 usando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
