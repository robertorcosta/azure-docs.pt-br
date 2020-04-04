---
title: Domínios suspensos nos serviços de domínio azure AD | Microsoft Docs
description: Saiba mais sobre os diferentes estados de saúde para um domínio gerenciado pelo Azure AD DS e como restaurar um domínio suspenso.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 42b26911c12b1e7c62444a6fb2ee68720b02a56b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654600"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Entenda os estados de saúde e resolva domínios suspensos nos Serviços de Domínio do Diretório Ativo do Azure

Quando os Serviços de Domínio Active Directory do Azure (Azure AD DS) não conseguem atender a um domínio gerenciado por um longo período de tempo, ele coloca o domínio gerenciado em um estado suspenso. Se um domínio gerenciado permanecer em um estado suspenso, ele será automaticamente excluído. Para manter o domínio gerenciado pelo Azure AD DS saudável e evitar a suspensão, resolva quaisquer alertas o mais rápido possível.

Este artigo explica por que os domínios gerenciados estão suspensos e como recuperar um domínio suspenso.

## <a name="overview-of-managed-domain-states"></a>Visão geral dos estados de domínio gerenciados

Através do ciclo de vida de um domínio gerenciado pelo Azure AD DS, existem diferentes estados que indicam sua saúde. Se o domínio gerenciado relatar um problema, resolva rapidamente a causa básica para impedir que o estado continue a se degradar.

![A progressão de estados que um domínio gerenciado pelo Azure AD DS leva para a suspensão](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Um domínio gerenciado pelo Azure AD DS pode estar em um dos seguintes estados:

* [Em execução](#running-state)
* [Precisa de atenção](#needs-attention-state)
* [Suspenso](#suspended-state)
* [Excluído](#deleted-state)

## <a name="running-state"></a>Estado em execução

Um domínio gerenciado pelo Azure AD DS que está configurado corretamente e funcionando sem problemas está no estado *em execução.* Este é o estado desejado para um domínio gerenciado.

### <a name="what-to-expect"></a>O que esperar

* A plataforma Azure pode monitorar regularmente a saúde do domínio gerenciado.
* Os controladores de domínio para o domínio gerenciado são corrigidos e atualizados regularmente.
* As alterações do Azure Active Directory são sincronizadas regularmente com o domínio gerenciado.
* Backups regulares são feitos para o domínio gerenciado.

## <a name="needs-attention-state"></a>Necessidades Estado de atenção

Um domínio gerenciado pelo Azure AD DS com um ou mais problemas que precisam ser corrigidos está no estado *de atenção necessidades.* A página de saúde do domínio gerenciado lista os alertas e indica onde há um problema. Alguns alertas são transitórios e são automaticamente resolvidos pela plataforma Azure. Para outros alertas, você pode corrigir o problema seguindo as etapas de resolução fornecidas. Se houver um alerta crítico, [abra um pedido de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

Um exemplo de alerta é quando há um grupo de segurança de rede restritivo. Nesta configuração, a plataforma Azure pode não ser capaz de atualizar e monitorar o domínio gerenciado. Um alerta é gerado, e o estado muda para *Precisa de atenção.*

Para obter mais informações, consulte [Como solucionar problemas de alertas para um domínio gerenciado pelo Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerenciado pelo Azure AD DS estiver no estado *de Atenção às Necessidades,* a plataforma Azure pode não ser capaz de monitorar, corrigir, atualizar ou fazer backup de dados regularmente. Em alguns casos, como com uma configuração de rede inválida, os controladores de domínio para o domínio gerenciado podem ser inalcançáveis.

* O domínio gerenciado está em um estado insalubre e o monitoramento contínuo da saúde pode parar até que o alerta seja resolvido.
* Os controladores de domínio para o domínio gerenciado não podem ser corrigidos ou atualizados.
* As alterações do Azure Active Directory podem não ser sincronizadas no domínio gerenciado.
* Os backups para o domínio gerenciado podem não ser feitos.
* Se você resolver alertas não críticos que estão afetando o domínio gerenciado, a saúde deve retornar ao estado *de execução.*
* Alertas críticos são acionados para problemas de configuração onde a plataforma Azure não pode alcançar os controladores de domínio. Se esses alertas críticos não forem resolvidos dentro de 15 dias, o domínio gerenciado entra no estado *suspenso.*

## <a name="suspended-state"></a>Estado suspenso

Um domínio gerenciado pelo Azure AD DS entra no estado **suspenso** por uma das seguintes razões:

* Um ou mais alertas críticos ainda não foram resolvidos em 15 dias.
    * Os alertas críticos podem ser causados por um erro de configuração que bloqueia o acesso aos recursos necessários ao Azure AD DS. Por exemplo, o alerta [AADDS104: Erro de rede][alert-nsg] não foi resolvido por mais de 15 dias no domínio gerenciado.
* Há um problema de faturamento com a assinatura do Azure ou a assinatura do Azure expirou.

Os domínios gerenciados são suspensos quando a plataforma Azure não pode gerenciar, monitorar, corrigir ou fazer backup do domínio. Um domínio gerenciado permanece em estado *suspenso* por 15 dias. Para manter o acesso ao domínio gerenciado, resolva os alertas críticos imediatamente.

### <a name="what-to-expect"></a>O que esperar

O comportamento a seguir é experimentado quando um domínio Gerenciado pelo Azure AD DS está no estado *suspenso:*

* Os controladores de domínio para o domínio gerenciado são desprovisionados e não podem ser alcançados dentro da rede virtual.
* O acesso LDAP seguro ao domínio gerenciado pela internet, se ativado, para de funcionar.
* Há falhas na autenticação do domínio gerenciado, no logon em VMs com domínio ou na conexão com LDAP/LDAPS.
* Os backups para o domínio gerenciado não são mais feitos.
* A sincronização com o Azure Active Directory para.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como saber se o seu domínio gerenciado está suspenso?

Você vê um [alerta][resolve-alerts] na página Azure AD DS Health no portal Azure que observa que o domínio está suspenso. O estado do domínio também mostra *Suspenso*.

### <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso

Para restaurar a saúde de um domínio gerenciado pelo Azure AD DS que está no estado *suspenso,* complete as seguintes etapas:

1. No portal Azure, procure e selecione **serviços de domínio**.
1. Escolha o domínio gerenciado pelo Azure AD DS na lista, como *aaddscontoso.com,* e selecione **Saúde**.
1. Selecione o alerta, como *AADDS503* ou *AADDS504,* dependendo da causa da suspensão.
1. Escolha o link de resolução fornecido no alerta e siga os passos para resolvê-lo.

Um domínio gerenciado só pode ser restaurado na data do último backup. A data do seu último backup é exibida na página **Saúde** do domínio gerenciado. Todas as alterações que ocorreram depois do último backup não serão restauradas. Backups para um domínio gerenciado são armazenados por até 30 dias. Backups mais antigos do que 30 dias serão excluídos.

Depois de resolver alertas quando o domínio gerenciado estiver no estado *suspenso,* [abra uma solicitação de suporte do Azure][azure-support] para retornar a um estado saudável. Se houver um backup em menos de 30 dias, o suporte ao Azure poderá restaurar o domínio gerenciado.

## <a name="deleted-state"></a>Estado excluído

Se um domínio gerenciado pelo Azure AD DS permanecer no estado *suspenso* por 15 dias, ele será excluído. Este processo é irrecuperável.

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerenciado pelo Azure AD DS entra no estado *Excluído,* o seguinte comportamento é visto:

* Todos os recursos e os backups para o domínio gerenciado serão excluídos.
* Você não pode restaurar o domínio gerenciado e precisa criar um domínio gerenciado de substituição para reutilizar o Azure AD DS.
* Depois que ele for excluído, não são cobrados para o domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Para manter o domínio gerenciado pelo Azure AD DS saudável e minimizar o risco de ele ficar suspenso, saiba como [resolver alertas para o seu domínio gerenciado][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
