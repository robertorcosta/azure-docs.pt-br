---
title: Conectar dados de logon único do Okta ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de logon único do Okta ao Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530919"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Conectar seu logon único do Okta ao Azure Sentinel com o Azure function

O conector de SSO (logon único) do Okta permite que você conecte facilmente todos os seus logs de solução de segurança de [SSO (logon único) do Okta](https://www.okta.com/products/single-sign-on/) com o Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre o logon único do Okta e o Azure Sentinel usa Azure Functions para efetuar pull de dados de log usando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configurar e conectar o logon único do Okta

Azure Functions pode integrar e efetuar pull de eventos e logs diretamente do logon único do Okta e encaminhá-los para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector **de logon único do Okta** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página **logon único do Okta** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados são exibidos em Log Analytics na tabela **Okta_CL** .

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o logon único do Okta ao Azure Sentinel usando os aplicativos de funções do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

