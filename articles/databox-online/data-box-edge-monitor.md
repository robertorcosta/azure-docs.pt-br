---
title: Monitorar o dispositivo do Azure Data Box Edge | Microsoft Docs
description: Descreve como usar o portal do Azure e a IU da Web local para monitorar o Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756726"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorar o Azure Data Box Edge

Este artigo descreve como monitorar o Azure Data Box Edge. Para monitorar seu dispositivo, use o portal do Azure ou a IU da Web local. Use o portal do Azure para exibir eventos do dispositivo, configurar e gerenciar alertas e exibir métricas. Use a IU da Web local em seu dispositivo físico para exibir o status de hardware dos vários componentes do dispositivo.

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

    ![Exibir o status de hardware](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Métricas de exibição

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gerenciar alertas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Próximas etapas 

Saiba como [Gerenciar a largura de banda](data-box-edge-manage-bandwidth-schedules.md).