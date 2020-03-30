---
title: Conecte a Defesa de Ameaça Móvel Zimperium ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar o Zimperium Mobile Threat Defense ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587933"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Conecte sua defesa de ameaça móvel zimperium ao Azure Sentinel


> [!IMPORTANT]
> O conector de dados Zimperium Mobile Threat Defense no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector Zimperium Mobile Threat Defense oferece a capacidade de conectar o registro de ameaças do Zimperium com o Azure Sentinel para visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre o cenário de ameaças móveis da sua organização e melhora os recursos de operação de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configure e conecte o Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense pode integrar e exportar logs diretamente para **o Azure Sentinel**.

1. No portal Azure Sentinel, clique em Conectores de dados e selecione **Zimperium Mobile Threat Defense**.
2. Selecione **Abrir a página do conector**.
3. Siga as instruções na página do conector **Zimperium Mobile Threat Defense,** resumidas da seguinte forma.
 1. Em zConsole, clique em **Gerenciar** na barra de navegação.
 2. Clique na guia **Integrações** .
 3. Clique no botão **Relatóriode ameaças** e, em seguida, no botão **Adicionar integrações.**
 4. Crie a Integração selecionando o **Microsoft Azure Sentinel** das integrações disponíveis e insira o ID do espaço de trabalho e a chave principal para se conectar ao Azure Sentinel.
 5. A opção de selecionar um nível de filtro para os dados de ameaça para empurrar para o Azure Sentinel também está disponível. 

4. Para obter informações adicionais, consulte o portal de suporte ao [cliente Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem no Log Analytics em CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Para usar o esquema relevante no Log Analytics para o Zimperium Mobile Threat Defense, procure ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar o Zimperium Mobile Threat Defense ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).

- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

Para saber mais sobre o Zimperium, consulte o seguinte:

- [Zimperium](https://zimperium.com)

- [Blog de Segurança Móvel Zimperium](https://blog.zimperium.com)

- [Portal de Suporte ao Cliente Zimperium](https://support.zimperium.com)

