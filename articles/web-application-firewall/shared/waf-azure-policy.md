---
title: Firewall e Azure Policy do aplicativo Web do Azure
description: O WAF (firewall do aplicativo Web) do Azure combinado com Azure Policy pode ajudar a reforçar os padrões organizacionais e a avaliar a conformidade em escala para recursos do WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 12ad18edbb434bdfaec2ae817ea079a843661ef6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111330"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall e Azure Policy do aplicativo Web do Azure

O WAF (firewall do aplicativo Web) do Azure combinado com Azure Policy pode ajudar a reforçar os padrões organizacionais e a avaliar a conformidade em escala para recursos do WAF. A política do Azure é uma ferramenta de governança que fornece uma exibição agregada para avaliar o estado geral do ambiente, com a capacidade de fazer uma busca detalhada na granularidade por recurso, por política. O Azure Policy também ajuda a trazer seus recursos para conformidade por meio da correção em massa de recursos existentes e correção automática para novos recursos.

## <a name="azure-policies-for-web-application-firewall"></a>Políticas do Azure para o Firewall do aplicativo Web

Há várias políticas internas do Azure para gerenciar recursos do WAF. Uma análise das políticas e suas funcionalidades são as seguintes:

1. O **WAF (firewall do aplicativo Web) deve ser habilitado para o serviço de porta frontal do Azure**: os serviços de porta frontal do Azure são avaliados se houver um WAF presente na criação de recursos. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um serviço de porta frontal do Azure não tem um WAF e permite que os usuários vejam o que o serviço de porta frontal do Azure não está em conformidade. Deny impede que qualquer serviço de porta frontal do Azure seja criado se um WAF não estiver anexado. Desabilitado desativa esta política.

2. O **Firewall do aplicativo Web (WAF) deve ser habilitado para o gateway de aplicativo**: os gateways de aplicativo serão avaliados se houver um WAF presente na criação de recursos. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um gateway de aplicativo não tem um WAF e permite que os usuários vejam qual gateway de aplicativo não está em conformidade. Deny impede que qualquer gateway de aplicativo seja criado se um WAF não estiver anexado. Desabilitado desativa esta política.

3. **O WAF (firewall do aplicativo Web) deve usar o modo especificado para o serviço de porta frontal do Azure**: exige que o uso do modo ' detecção ' ou ' prevenção ' esteja ativo em todas as políticas de firewall do aplicativo Web para o serviço de porta de recepção do Azure. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um WAF não se ajusta ao modo especificado. Deny impede que qualquer WAF seja criada se não estiver no modo correto. Desabilitado desativa esta política.

4. **O WAF (firewall do aplicativo Web) deve usar o modo especificado para o gateway de aplicativo**: exige que o uso do modo ' detecção ' ou ' prevenção ' esteja ativo em todas as políticas de firewall do aplicativo Web para o gateway de aplicativo. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um WAF não se ajusta ao modo especificado. Deny impede que qualquer WAF seja criada se não estiver no modo correto. Desabilitado desativa esta política.


## <a name="launch-an-azure-policy"></a>Iniciar um Azure Policy


1.  Na home page do Azure, digite política na barra de pesquisa e clique no ícone de Azure Policy

2.  No serviço de política do Azure, em **criação**, selecione **atribuições**.

![Firewall do aplicativo Web do Azure](../media/waf-azure-policy/policy-home.png)

3.  Na página atribuições, selecione o ícone **atribuir política** na parte superior.

![Firewall do aplicativo Web do Azure](../media/waf-azure-policy/assign-policy.png)

4.  Na guia elementos básicos da página atribuir política, atualize os seguintes campos:
    1.  **Escopo**: Selecione quais assinaturas e grupos de recursos do Azure devem ser afetados pelo Azure Policy.
    2.  **Exclusões**: selecione todos os recursos do escopo a serem excluídos da política 
    3.  **Definição de política**: selecione o Azure Policy a ser aplicado ao escopo com exclusões. Digite "Firewall do aplicativo Web" na barra de pesquisa para escolher o Azure Policy de firewall do aplicativo Web relevante.

![Firewall do aplicativo Web do Azure](../media/waf-azure-policy/policy-listing.png)


5.  Selecione a guia **parâmetros** e atualize os parâmetros de políticas. Para esclarecer ainda mais o que o parâmetro faz, passe o mouse sobre o ícone de informações ao lado do nome do parâmetro para obter mais esclarecimentos.

6.  Selecione **examinar + criar** para finalizar sua política do Azure. A política do Azure levará aproximadamente 15 minutos até ficar ativa para novos recursos.
