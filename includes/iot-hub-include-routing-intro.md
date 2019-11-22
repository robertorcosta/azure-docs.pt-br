---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 863989f8e2cb90fe5ec0921ea6e080b61fc1b4ae
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808858"
---
O [roteamento de mensagens](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) permite enviar dados de telemetria de seus dispositivos IoT para pontos de extremidade internos compatíveis com o Hub de Eventos ou para pontos de extremidade personalizados, como Armazenamento de Blobs, Filas do Barramento de Serviço, Tópicos do Barramento de Serviço e Hubs de Eventos. Para configurar o roteamento de mensagens personalizado, crie [consultas de roteamento](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) para personalizar a rota que corresponde a uma determinada condição. Uma vez configurado, os dados de entrada são roteados automaticamente para os pontos de extremidade pelo Hub IoT. Se uma mensagem não corresponder a nenhuma das consultas de roteamentos definidas, ela será roteada para o ponto de extremidade padrão.

Neste tutorial de duas partes, você aprenderá a configurar e usar essas consultas personalizadas de roteamentos com o Hub IoT. Você vai rotear mensagens de um dispositivo IoT para um dos vários serviços, incluindo o armazenamento de blobs e uma fila do Barramento de Serviço. As mensagens na fila do Barramento de Serviço são escolhidas por um aplicativo lógico e enviadas por email. As mensagens que não têm roteamento de mensagens personalizada definido são enviadas para o ponto de extremidade padrão e, em seguida, captadas pelo Azure Stream Analytics e exibidas em uma visualização do Power BI.

Para concluir as Partes 1 e 2 deste tutorial, execute as seguintes tarefas:

**Parte I: Criar recursos, configurar o roteamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos – um hub IoT, uma conta de armazenamento, uma fila do Barramento de Serviço e um dispositivo simulado. Isso pode ser feito usando o portal do Azure, um modelo do Azure Resource Manager, a CLI do Azure ou o Azure PowerShell.
> * Configure os pontos de extremidade e as rotas de mensagem no Hub IoT para a conta de armazenamento e a fila do Barramento de Serviço.

**Parte II: Enviar mensagens ao hub, exibir resultados roteados**
> [!div class="checklist"]
> * Crie um aplicativo lógico que é disparado e envia e-mail quando uma mensagem é adicionada à fila de Barramento de Serviço.
> * Baixe e execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub para as diferentes opções de roteamentos.
> * Crie uma visualização do Power BI para os dados enviados para o ponto de extremidade padrão.
> * Exiba os resultados...
> * ... na fila do Barramento de Serviço e emails.
> * ... na conta de armazenamento.
> * ...na visualização do Power BI.

## <a name="prerequisites"></a>Pré-requisitos

* Para a Parte 1 deste tutorial:
  - Você precisa ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Para a Parte 2 deste tutorial:
  - Você deve ter concluído a Parte 1 deste tutorial e ter os recursos ainda disponíveis.
  - Instalar o [Visual Studio](https://www.visualstudio.com/).
  - Tenha acesso a uma conta do Power BI para analisar a análise de fluxo do ponto de extremidade padrão. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Ter uma conta do Office 365 para enviar e-mails de notificação.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
