---
title: Conecte o Centro de Segurança Azure para IoT ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Azure Security Center para dados IoT ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588630"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Conecte seus dados do Azure Security Center for IoT ao Azure Sentinel 


> [!IMPORTANT]
> O Conector de dados Azure Security Center for IoT está atualmente em visualização pública. Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Use o conector Azure Security Center para IoT para transmitir todo o seu Centro de Segurança Azure para eventos de IoT no Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- **Ler** e **gravar** permissões no Espaço de trabalho para o qual o Azure Sentinel é implantado
- **O Azure Security Center for IoT** deve ser **habilitado** em seus hubs de IoT relevantes
- **Ler** e **gravar** permissões no **Azure IoT Hub** que você deseja conectar
- **Ler** e **gravar** permissões no **grupo de recursos Do Hub Azure IoT**

> [!NOTE]
> Embora você deva habilitar a licença de nível padrão do Azure Security **Center** em sua assinatura para transmitir alertas de recursos IoT para o Azure Sentinel, você só precisa habilitar a licença de nível **gratuito** do Azure Security Center em sua assinatura para visualizar o Azure Security Center para alertas de IoT no Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Conecte-se ao Azure Security Center para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no Centro de Segurança Do Azure para azulejo **IoT.**
1. No painel inferior direito, clique em **Abrir a página do conector**. 
1. Clique **em Conectar**, ao lado de cada assinatura do IoT Hub cujos alertas e alertas de dispositivo você deseja transmitir no Azure Sentinel. 
    - Se o Azure Security Center for IoT não estiver habilitado nesse Hub, você verá uma mensagem de aviso **Habilitar.** Clique no link **Ativar** para iniciar o serviço. 
1. Você pode decidir se deseja que os alertas do Azure Security Center for IoT gerem automaticamente incidentes no Azure Sentinel. Em **Criar incidentes,** **selecione Habilitar** a regra analítica padrão para criar incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Essa regra pode ser alterada ou editada sob as regras **do Analytics** > **Active.**

> [!NOTE]
> Pode levar algum tempo para a lista do hub atualizar depois de fazer alterações de conexão. 

## <a name="log-analytics-alert-display"></a>Exibição de alerta do Log Analytics

Para usar o esquema relevante no Log Analytics para exibir o Azure Security Center para alertas de IoT:

1. **Abrir logs** > **SecurityInsights** > **SecurityAlert**ou procurar **o SecurityAlert**. 
2. Filtrar para ver apenas o Azure Security Center para alertas gerados por IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de conectar um IoT Hub, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar o Azure Security Center para dados IoT ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.
