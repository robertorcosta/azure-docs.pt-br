---
title: Conecte-se além da segurança proteger dados para o Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de segurança mais seguro para obter logs comsegurados no Azure Sentinel. Exiba dados comseguros em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102771"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Conecte-se à segurança mais segura para o Azure Sentinel

> [!IMPORTANT]
> O conector de dados de segurança mais seguro no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Além da segurança, o conector do SECURE Security permite que você conecte facilmente todos os seus logs de solução de segurança comseguros com sua Sentinela do Azure, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre o beseguro e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Configurar e conectar-se com segurança

o beseguro pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione além da segurança se comportar **(versão prévia)** e **abra a página conector** .

1. Siga as etapas abaixo para configurar sua solução de besegurança para enviar resultados da verificação, status da verificação e logs de trilha de auditoria para o Azure Sentinel.

    **Acesse o menu integração:**
    1. Clique na opção de menu ' mais '

    1. Selecionar servidor

    1. Selecionar integração

    1. Habilitar o Azure Sentinel 

    **Forneça as configurações do adseguro com o Azure Sentinel.**
      - Copie a *ID do espaço de trabalho* e os valores de *chave primária* da página do conector do Azure Sentinel, Cole-os na configuração de besegurança e clique em **Modificar** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** , na seção **CustomLogs** , em uma ou mais das seguintes tabelas:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Para consultar os logs do comseguro no Log Analytics, insira um dos nomes de tabela acima na parte superior da janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a se conectar ao adseguro ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
