---
title: Monitorar seu dispositivo do Azure Stack Edge | Microsoft Docs
description: Descreve como usar o portal do Azure e a interface do usuário da Web local para monitorar o Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569427"
---
# <a name="monitor-your-azure-stack-edge"></a>Monitorar sua borda de Azure Stack

Este artigo descreve como monitorar seu Azure Stack Edge. Para monitorar seu dispositivo, use o portal do Azure ou a IU da Web local. Use o portal do Azure para exibir eventos do dispositivo, configurar e gerenciar alertas e exibir métricas. Use a IU da Web local em seu dispositivo físico para exibir o status de hardware dos vários componentes do dispositivo.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Exibir eventos do dispositivo e os alertas correspondentes
> * Exibir o status de hardware dos componentes do dispositivo
> * Exibir métricas de capacidade e transação do dispositivo
> * Configurar e gerenciar alertas

## <a name="view-device-events"></a>Exibir eventos do dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Exibir o status de hardware

Execute as etapas a seguir na IU da Web local para exibir o status de hardware dos componentes do dispositivo.

1. Conecte-se à IU da Web local do dispositivo.
2. Acesse **Manutenção > Status do hardware**. Exiba a integridade dos vários componentes do dispositivo. 

    ![Exibir o status de hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Métricas de exibição

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gerenciar alertas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Próximas etapas 

Saiba como [Gerenciar a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).