---
title: Solução de problemas do Hub IoT do Azure erro 504101 GatewayTimeout
description: Entenda como corrigir o erro 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960666"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Este artigo descreve as causas e soluções para erros de **504101 GatewayTimeout** .

## <a name="symptoms"></a>Sintomas

Ao tentar invocar um método direto do Hub IoT para um dispositivo, a solicitação falha com o erro **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

O Hub IoT encontrou um erro e não pôde confirmar se o método direto foi concluído antes de atingir o tempo limite.

### <a name="cause-2"></a>Causa 2

Ao usar uma versão anterior do SDK do Azure C# iot (< 1.19.0), o link AMQP entre o dispositivo e o Hub IOT pode ser descartado silenciosamente por causa de um bug.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Emita uma nova tentativa.

### <a name="solution-2"></a>Solução 2

Atualize para a versão mais recente do SDK do C# Azure IOT.