---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420947"
---
Este artigo explica como carregar um VHD da sua máquina local para um disco gerenciado do Azure ou copiar um disco gerenciado para outra região, usando o AzCopy. Esse processo, o upload direto, também permite carregar um VHD de até 32 TiB de tamanho diretamente em um disco gerenciado. Atualmente, o upload direto é suportado para HDD padrão, SSD padrão e discos gerenciados premium SSD. Ainda não é suportado para ultra discos.

Se você está fornecendo uma solução de backup para VMs IaaS no Azure, recomendamos usar o upload direto para restaurar backups de clientes em discos gerenciados. Ao carregar um VHD de uma fonte externa ao Azure, as velocidades dependeriam da largura de banda local. Ao carregar ou copiar de uma VM Azure, então sua largura de banda seria a mesma dos HDDs padrão.