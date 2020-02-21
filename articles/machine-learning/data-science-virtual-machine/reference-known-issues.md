---
title: 'Referência: problemas conhecidos & solução de problemas'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenha uma lista dos problemas conhecidos, soluções alternativas e solução de problemas do Azure Máquina Virtual de Ciência de Dados
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526532"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conhecidos e solução de problemas do Máquina Virtual de Ciência de Dados do Azure

Este artigo ajuda você a encontrar e corrigir erros ou falhas encontrados ao usar o Máquina Virtual de Ciência de Dados do Azure.

## <a name="python-package-installation-issues"></a>Problemas de instalação do pacote do Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalando pacotes com dependências de quebras de pip no Linux

Use `sudo pip install` em vez de `pip install` ao instalar pacotes.

## <a name="disk-encryption-issues"></a>Problemas de criptografia de disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A criptografia de disco falha no Ubuntu DSVM

No momento, não há suporte para Azure Disk Encryption (ADE) no DSVM do Ubuntu. Como alternativa, considere configurar a [criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>A ferramenta aparece desabilitada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>O Hyper-V não funciona no DSVM do Windows

Esse é um comportamento esperado, como para o desempenho de inicialização, desabilitamos alguns serviços. Para reabilitar, abra a barra de pesquisa no DSVM do Windows, digite "serviços" e, em seguida, defina todos os serviços do Hyper-V como "manual" e defina "gerenciamento de máquina virtual do Hyper-V" como "automático".

Sua tela final deve ter a seguinte aparência:

   ![Habilitar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

