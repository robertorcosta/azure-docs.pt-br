---
title: Conectar o ia Vectra detectar dados ao Azure sentinela | Microsoft Docs
description: Saiba como conectar o ia Vectra detectar dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 964bab2b81f6ce69e77fd69cab55f6d1894e4edb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368359"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Conectar o ia Vectra detectar ao Azure Sentinel

Este artigo explica como conectar seu dispositivo de [detecção de Vectra de ia](https://www.vectra.ai/product/cognito-detect) para o Azure Sentinel. O conector de detecção de dados do ia Vectra permite que você traga facilmente seu Vectra de ia para detectar dados no Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Configurar seu dispositivo de detecção de Vectra de ia para enviar mensagens CEF  

Configurar o ia Vectra detectar para encaminhar mensagens de syslog formatadas para o seu Log Analytics espaço de trabalho por meio do encaminhador syslog configurado na [etapa 1: implantar o encaminhador de log](connect-cef-agent.md).

1. Na interface Vectra, navegue até configurações > notificações e escolha Editar configuração de syslog. Siga as instruções abaixo para configurar a conexão:

    - Adicionar um novo destino (o nome do host do [encaminhador de log](connect-cef-agent.md))
    - Definir a porta como **514**
    - Definir o protocolo como **UDP**
    - Definir o formato para **CEF**
    - Definir tipos de log (selecione todos os tipos de log disponíveis)
    - Clique em **salvar**

2. Você pode clicar no botão **testar** para forçar o envio de alguns eventos de teste para o encaminhador de log.

3. Para usar o esquema relevante em Log Analytics para os eventos de detecção de Vectra de ia, pesquise **CommonSecurityLog**.

4. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o ia Vectra a detectar dispositivos ao Azure Sentinel. Para aproveitar ao máximo os recursos integrados a esse conector de dados, clique na guia **próximas etapas** na página conector de dados. Lá, você encontrará algumas consultas de exemplo prontas para que possa começar a localizar informações úteis.

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
