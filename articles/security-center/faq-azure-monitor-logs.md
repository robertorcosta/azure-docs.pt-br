---
title: Faq do Azure Security Center - perguntas sobre agentes existentes do Log Analytics
description: Essa perguntas perguntas para clientes que já usam o agente Log Analytics e considerando o Azure Security Center, um produto que ajuda você a prevenir, detectar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436162"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Perguntas frequentes para clientes que já usam registros do Monitor do Azure<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Central de Segurança substitui todas as conexões existentes entre as VMs e os workspaces?

Se uma VM já tiver o agente Log Analytics instalado como uma extensão do Azure, o Security Center não substituirá a conexão existente no espaço de trabalho. Em vez disso, a Central de Segurança usará o workspace existente. A VM será protegida desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho ao qual está relatando. 

Uma solução do Security Center é instalada no espaço de trabalho selecionado na tela de Coleta de Dados, se ainda não estiver presente, e a solução é aplicada apenas às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics. [Direcionamento de Solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite a aplicação de um escopo às suas soluções.

> [!TIP]
> Se o agente Log Analytics estiver instalado diretamente na VM (não como uma extensão do Azure), o Security Center não instalará o agente Log Analytics e o monitoramento de segurança será limitado.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A Central de Segurança instala soluções em meus workspaces existentes do Log Analytics? Quais são as implicações de cobrança?
Quando a Central de Segurança identificar que uma VM já está conectada a um workspace que você criou, a Central de Segurança habilitará as soluções neste workspace de acordo com seu tipo de preço. As soluções são aplicadas apenas às VMs do Azure relevantes, por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md), portanto, a cobrança permanece igual.

- **Camada gratuita** – A Central de Segurança instala a solução 'SecurityCenterFree' no workspace. Você não será cobrado pelo nível Livre.
- **Camada Standard**: a Central de Segurança instala a solução 'Security' no workspace.

   ![Soluções no workspace padrão](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho workspaces em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o agente Log Analytics instalado como uma extensão do Azure, o Security Center usará o espaço de trabalho conectado existente. Uma solução da Central de Segurança será instalado no workspace, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando o Security Center instala o agente Log Analytics em VMs, ele usa os espaços de trabalho padrão criados pelo Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho a solução de segurança em meus workspaces. Quais são as implicações de cobrança?
A solução Security & Audit é usada para habilitar os recursos de nível padrão do Security Center para VMs do Azure. Se a solução de Segurança e Auditoria já estiver instalada em um workspace, a Central de Segurança usará a solução existente. Não há nenhuma alteração na cobrança.