---
title: 'Referência: preterição de imagem de Máquina Virtual de Ciência de Dados'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre as reprovações que afetam o Máquina Virtual de Ciência de Dados do Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754758"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: substituição de imagens DSVM

Abaixo, discutimos sugestões para lidar com as futuras substituições no Máquina Virtual de Ciência de Dados do Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrando discos de dados

Vamos parar de dar suporte à imagem do Windows 2012 DSVM em 31 de dezembro de 2019. Para migrar um disco de dados de seu DSVM do Windows 2012 existente para um DSVM do Windows 2016, execute as seguintes etapas:

1. Crie um novo DSVM do Windows 2016, seguindo as instruções mostradas [aqui](./provision-vm.md#create-your-dsvm).
1. Desanexe discos de dados existentes da imagem do Windows 2012 usando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Anexe o disco da etapa anterior à sua imagem do Windows 2016 usando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Os novos usuários devem usar as imagens mais recentes do Ubuntu ou do Windows. O CentOS continuará disponível para uso com modelos de solução existentes.