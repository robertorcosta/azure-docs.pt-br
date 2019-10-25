---
title: 'Solução de problemas: dados ausentes nos logs de atividades baixados | Microsoft Docs'
description: Fornece uma resolução para dados ausentes nos logs de atividade do Azure Active Directory baixados.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: db96bd99a25ca7803dbdfe412e2cddbbcb57c0e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817532"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não consigo localizar todos os dados nos logs de atividade do Azure Active Directory que eu baixei

## <a name="symptoms"></a>Sintomas

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatório](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala a 250.000 registros, classificados por mais recentes primeiro. 

## <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](concept-reporting-api.md) para buscar até um milhões de registros em qualquer momento determinado.

## <a name="next-steps"></a>Próximos passos

* [Perguntas frequentes sobre relatórios do Azure Active Directory](reports-faq.md)

