---
title: Conectar a central de segurança do Azure para IoT ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar a central de segurança do Azure para dados de IoT ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: b68745f04a49565090b0f9b14331036f76e5f132
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501284"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Conecte seus dados da central de segurança do Azure para IoT ao Azure Sentinel 


> [!IMPORTANT]
> A central de segurança do Azure para o conector de dados do IoT está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Use a central de segurança do Azure para o conector IoT para transmitir todos os eventos de IoT da central de segurança do Azure para o Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisites

- Permissões de **leitura** e **gravação** no espaço de trabalho no qual o Azure Sentinel é implantado
- A **central de segurança do Azure para IOT** deve ser **habilitada** em seus hubs (s) IOT relevantes
- Permissões de **leitura** e **gravação** no **Hub IOT do Azure** que você deseja conectar
- Permissões de **leitura** e **gravação** no **grupo de recursos do Hub IOT do Azure**

> [!NOTE]
> Embora seja necessário habilitar a licença da camada **Standard** da central de segurança do Azure em sua assinatura para transmitir alertas de recursos de IOT para o Azure Sentinel, você só precisa habilitar a licença de camada **gratuita** da central de segurança do Azure em sua assinatura para exibir alertas de IOT da central de segurança do Azure no Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Conectar-se à central de segurança do Azure para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **central de segurança do Azure para IOT** .
1. No painel inferior direito, clique na **página abrir conector**. 
1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel. 
    - Se a central de segurança do Azure para IoT não estiver habilitada nesse Hub, você verá uma mensagem de aviso de **habilitação** . Clique no link **habilitar** para iniciar o serviço. 
1. Você pode decidir se deseja que os alertas da central de segurança do Azure para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra analítica padrão para criar incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Essa regra pode ser alterada ou editada em **análise** > regras **ativas** .

> [!NOTE]
> Pode levar algum tempo para a lista de Hub ser atualizada depois de fazer alterações na conexão. 

## <a name="log-analytics-alert-display"></a>Log Analytics exibição de alertas

Para usar o esquema relevante no Log Analytics para exibir a central de segurança do Azure para alertas de IoT:

1. Abra **Logs** > **SecurityInsights** > **SecurityAlert**ou pesquise por **SecurityAlert**. 
2. Filtre para ver apenas a central de segurança do Azure para alertas gerados por IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de conectar um hub IoT, os dados do Hub estarão disponíveis no Azure Sentinel aproximadamente 15 minutos mais tarde.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a central de segurança do Azure para dados de IoT ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
