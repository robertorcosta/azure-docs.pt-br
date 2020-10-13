---
title: A solução de problemas do Windows não pôde concluir a configuração do sistema
titlesuffix: Azure Virtual Machines
description: Este artigo fornece etapas para resolver problemas em que o processo Sysprep impede a inicialização de uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: bde091b4a4559c3574ee122d74574d1f9477f3fd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977041"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>A solução de problemas do Windows não pôde concluir a configuração do sistema

Este artigo fornece etapas para resolver problemas em que o processo Sysprep impede a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Quando você usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, verá que a captura de tela exibe um erro de instalação do Windows enquanto a instalação do Windows está iniciando serviços. O erro exibirá a mensagem:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![A Figura 1 exibe um erro de instalação do Windows com a mensagem: "o Windows não pôde concluir a configuração do sistema. Para tentar retomar a configuração, reinicie o computador. A instalação está iniciando os serviços "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Causa

Esse erro é causado quando o sistema operacional (SO) não consegue concluir o [processo do Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Esse erro ocorrerá quando você tentar uma inicialização inicial de uma VM generalizada. Se você encontrar esse problema, recrie a imagem generalizada, pois a imagem está em um estado não implantável e não pode ser recuperada.

## <a name="solution"></a>Solução

Para corrigir esse problema, siga as [diretrizes do Azure sobre como preparar/capturar uma imagem](../windows/upload-generalized-managed.md) e preparar uma nova imagem generalizada.