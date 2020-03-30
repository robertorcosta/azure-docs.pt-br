---
title: 'Referência: Problemas conhecidos & solução de problemas'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenha uma lista dos problemas conhecidos, solução de solução de problemas e solução de problemas para a Máquina Virtual de Ciência de Dados do Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206513"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conhecidos e solução de problemas da Máquina Virtual de Data Science do Azure

Este artigo ajuda você a encontrar e corrigir erros ou falhas que você pode encontrar ao usar a Máquina Virtual de Data Science do Azure.

## <a name="python-package-installation-issues"></a>Problemas de instalação do pacote Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalar pacotes com pip quebra dependências no Linux

Use `sudo pip install` em `pip install` vez de quando instalar pacotes.

## <a name="disk-encryption-issues"></a>Problemas de criptografia de disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A criptografia de disco falha no Ubuntu DSVM

A Ade (Ade) não é suportada no Ubuntu DSVM. Como solução de solução, considere configurar a [criptografia do lado do servidor dos discos gerenciados do Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Ferramenta parece desativada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V não funciona no Windows DSVM

Que o Hyper-V inicialmente não funciona no Windows é comportamento esperado. Para o desempenho do inicial, desativamos alguns serviços. Para habilitar o Hyper-V:

1. Abra a barra de pesquisa em seu Windows DSVM
1. Digite "Serviços",
1. Defina todos os serviços Hyper-V como "Manual"
1. Definir "Hyper-V Virtual Machine Management" como "Automático"

Sua tela final deve ser assim:

   ![Habilitar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

