---
title: Solucionar problemas de dados ausentes em logs de atividade | Microsoft Docs
description: Fornece uma resolução para dados ausentes nos logs de atividades do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c335a4d30846f7c1b4dbd6b6aedc4d100a9b43a
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74014286"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Solução de problemas: Dados ausentes nos logs de atividades do Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Não consigo encontrar os logs de auditoria para ações mais recentes no portal do Azure

### <a name="symptoms"></a>Sintomas

Eu executei algumas ações no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Audit Logs`, mas não é possível encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 min |
| Atividade de entrada | &nbsp; | 2 minutos | 5 min | 

### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Não consigo localizar entradas recentes do usuário no log de atividades de entradas do Azure Active Directory

### <a name="symptoms"></a>Sintomas

Eu entrei recentemente no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Sign-ins`, mas não é possível encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 min |
| Atividade de entrada | &nbsp; | 2 minutos | 5 min | 

### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo exibir mais de 30 dias de dados de relatório no portal do Azure

### <a name="symptoms"></a>Sintomas

Não consigo exibir mais de 30 dias de dados de entrada e de auditoria no portal do Azure. Por quê? 

 ![Relatórios](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazenam relatórios de atividades para as durações a seguir:

| Relatório           | &nbsp; |  AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Auditoria de Diretório  | &nbsp; |   7 dias     | 30 dias             | 30 dias             |
| Atividade de Entrada | &nbsp; | Não disponível. Você pode acessar sua própria atividade de entrada por 7 dias na folha de perfil do usuário individual | 30 dias | 30 dias             |

Para saber mais informações, confira [Políticas de retenção de relatório do Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Resolução

Você tem duas opções para manter os dados por mais de 30 dias. Você pode usar as [APIs de emissão de relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio de programação e armazená-los em um banco de dados. Como alternativa, você pode integrar os logs de auditoria em um sistema SIEM de terceiros, como o Splunk ou SumoLogic.

## <a name="next-steps"></a>Próximas etapas

* [API de retenção de relatório](reference-reports-data-retention.md).
* [Azure Active Directory latência de relatório](reference-reports-latencies.md).
* [Azure Active Directory perguntas frequentes sobre relatórios](reports-faq.md).

