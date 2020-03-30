---
title: Guia do Azure Security Center for IoT para configuração com o Azure Sentinel (visualização)| Microsoft Docs
description: Explica como configurar o Azure Sentinel para receber dados do seu Azure Security Center para solução IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303538"
---
> [!IMPORTANT]
> O Azure Security Center for IoT data connector no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Conecte seus dados do Azure Security Center for IoT ao Azure Sentinel (visualização) 

Neste guia, saiba como conectar seu Azure Security Center para dados De IoT ao Azure Sentinel.  

> [!div class="checklist"]
> * Pré-requisitos 
> * Configurações de conexão
> * Exibição de alerta do Log Analytics 

Conecte alertas do Azure Security Center para IoT e transmita-os diretamente para o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões **de leitura** e **gravação** do Workspace.
- **O Azure Security Center for IoT** deve ser **habilitado** em seus Hubs de IoT relevantes.
- Você deve ter permissões **de leitura** e **gravação** no **Azure IoT Hub** que deseja conectar.
- Você também deve ter **permissões de leitura** e **gravação** no grupo de recursos **Do Azure IoT Hub**.

> [!NOTE]
> Você deve ter o licenciamento de nível Padrão do Azure Security Center em execução em sua assinatura para enviar alertas gerais de recursos do Azure. Com o licenciamento de nível gratuito necessário para o Azure Security Center for IoT, apenas o Azure Security Center para alertas relacionados à IoT será encaminhado para o Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Conecte-se ao Azure Security Center para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no Centro de Segurança Do Azure para azulejo **IoT.**
1. Na parte inferior do painel direito, clique em **Abrir a página do conector**. 
1. Clique **em Conectar**, ao lado de cada assinatura do IoT Hub cujos alertas e alertas de dispositivo você deseja transmitir no Azure Sentinel. 
    - Se o Azure Security Center for IoT não estiver habilitado nesse Hub, você verá uma mensagem de aviso Habilitar. Clique no **link Ativar** para iniciar e ativar o serviço. 
1. Você pode decidir se deseja que os alertas do Azure Security Center for IoT gerem automaticamente incidentes no Azure Sentinel. Em **Criar incidentes,** **selecione Habilitar** a regra para criar automaticamente incidentes a partir dos alertas gerados.  Essa regra pode ser alterada ou editada as regras **do Analytics** > **Active.**

> [!NOTE]
>Pode levar 10 segundos ou mais para atualizar a lista do hub depois de fazer alterações de conexão. 

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

Neste documento, você aprendeu como conectar o Azure Security Center para IoT ao Azure Sentinel. Para saber mais sobre detecção de ameaças e acesso a dados de segurança, consulte os seguintes artigos:

- Aprenda a usar o Azure Sentinel para [obter visibilidade sobre seus dados e ameaças potenciais](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Saiba como [acessar seus dados de segurança de IoT](how-to-security-data-access.md)