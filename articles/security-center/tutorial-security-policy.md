---
title: Trabalhar com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906869"
---
# <a name="working-with-security-policies"></a>Trabalhando com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. 

## <a name="introduction-to-security-policies"></a>Introdução às políticas de segurança

Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir que você esteja cumprindo os requisitos de segurança de sua empresa ou reguladores.

O Azure Security Center faz suas recomendações de segurança com base nas políticas escolhidas. As políticas do Security Center são baseadas em iniciativas políticas criadas na Política do Azure. Você pode usar [a Diretiva Azure](../governance/policy/overview.md) para gerenciar suas políticas e definir políticas em grupos de gerenciamento e em várias assinaturas.

O Security Center oferece as seguintes opções para trabalhar com políticas de segurança:

* **Exibir e editar a política padrão incorporada** - Quando você habilita o Security Center, uma iniciativa incorporada chamada 'ASC default' é automaticamente atribuída a todas as assinaturas registradas do Security Center (níveis gratuitos ou padrão). Para personalizar essa iniciativa, você pode habilitar ou desativar políticas individuais dentro dela. Consulte a lista de [políticas de segurança incorporadas](security-center-policy-definitions.md) para entender as opções disponíveis fora da caixa.

* **Adicione suas próprias políticas personalizadas** - Se você quiser personalizar as iniciativas de segurança aplicadas à sua assinatura, você pode fazê-lo dentro do Security Center. Em seguida, você receberá recomendações se suas máquinas não seguirem as políticas que você cria. Para obter instruções sobre a construção e a atribuição de políticas personalizadas, consulte [Usando políticas de segurança personalizadas](custom-security-policies.md).

* **Adicionar políticas de conformidade normativa** - O painel de conformidade normativa do Security Center mostra o status de todas as avaliações dentro do seu ambiente no contexto de um determinado padrão ou regulamento (como Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Para obter mais informações, consulte [Melhorar sua conformidade normativa](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Gerenciando suas políticas de segurança

Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Security Center**, selecione **Security policy**.

    ![O painel Gerenciamento de Política](./media/security-center-policies/security-center-policy-mgt.png)

   Na tela **Gerenciamento de políticas**, você pode ver o número de grupos de gerenciamento, assinaturas e workspaces, bem como a estrutura do grupo de gerenciamento.

1. Selecione a assinatura ou o grupo de gerenciamento cujas políticas você deseja visualizar.

1. A página de política de segurança para esse grupo de assinatura ou gerenciamento é exibida. Ele mostra as políticas disponíveis e atribuídas.

   ![tela de política](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se houver um rótulo "MG Herdado" ao lado de sua política padrão, significa que a política foi atribuída a um grupo de gerenciamento e herdada pela assinatura que você está visualizando.


1. Escolha entre as opções disponíveis nesta página:

    1. Para trabalhar com políticas do setor, clique **em Adicionar mais padrões**. Para obter mais informações, consulte [Atualizar para pacotes de conformidade dinâmica](update-regulatory-compliance-packages.md).

    1. Para atribuir e gerenciar iniciativas personalizadas, clique **em Adicionar iniciativas personalizadas**. Para obter mais informações, consulte [Usando políticas de segurança personalizadas](custom-security-policies.md).

    1. Para exibir e editar a diretiva padrão, clique **em Exibir política eficaz** e proceda conforme descrito abaixo. 

       ![tela de política](./media/security-center-policies/policy-screen.png)
       
       Esta tela **de política de segurança** reflete as ações tomadas pelas políticas atribuídas no grupo de assinatura ou gerenciamento selecionado.
       
       * Use os links na parte superior para abrir uma **atribuição de** política que se aplica no grupo de assinatura ou gerenciamento. Esses links permitem que você acesse a atribuição e edite ou desative a diretiva. Por exemplo, se você ver que uma determinada atribuição de diretiva está efetivamente negando proteção de ponto final, use o link para editar ou desativar a diretiva.
       
       * Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. As configurações de cada política que se aplicam ao escopo são levadas em consideração e o resultado cumulativo das ações tomadas pela política é mostrado. Por exemplo, se em uma atribuição da diretiva estiver desativada, mas em outra ela estiver definida como AuditIfNotExist, então o efeito cumulativo aplica AuditIfNotExist. O efeito mais ativo sempre tem precedência.
       
       * O efeito das políticas pode ser: Anexar, Auditar, AuditIfNotExists, Negar, DeployIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?

Você pode editar as políticas de segurança no portal Azure Policy usando a API REST ou o Windows PowerShell.

A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. O que significa que os usuários são atribuídos a função de *proprietário,* *contribuinte*ou *leitor* à assinatura do recurso. Além dessas funções, existem duas funções específicas do Security Center:

- **Leitor de segurança**: tem direitos de exibição para a Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não podem fazer alterações.
- **Administrador de segurança**: têm os mesmos direitos de exibição que o *leitor de segurança* e também podem atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="disable-security-policies"></a>Desativar políticas de segurança
Se a política de segurança padrão estiver gerando uma recomendação que não é relevante para o seu ambiente, você pode pará-la desativando a definição de política que envia a recomendação.
Para obter mais informações sobre recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

1. No Centro de Segurança, da seção **Política & Compliance,** clique em **Política de Segurança**.

   ![gestão de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique no grupo de assinatura ou gerenciamento para o qual deseja desativar a recomendação.

   > [!NOTE]
   > Lembre-se de que um grupo de gerenciamento aplica suas políticas a suas assinaturas. Portanto, se você desabilitar a política de uma assinatura e a assinatura pertencer a um grupo de gerenciamento que ainda usa a mesma política, continuará recebendo as recomendações de política. A política ainda será aplicada do nível de gerenciamento e as recomendações ainda serão geradas.

1. Clique **em Exibir política eficaz**.

   ![política desativação](./media/tutorial-security-policy/view-effective-policy.png)

1. Clique na diretiva atribuída.

   ![política desativação](./media/tutorial-security-policy/security-policy.png)

1. Na seção **PARÂMETROS,** procure a diretiva que invoca a recomendação que deseja desativar e, na lista de itens de saque, **selecione Disabled**

   ![política desativação](./media/tutorial-security-policy/disable-policy.png)

1. Clique em **Salvar**.

   > [!NOTE]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre políticas de segurança. Para obter informações relacionadas, consulte os seguintes artigos:

* Para obter instruções sobre como definir políticas usando o PowerShell, consulte [Quickstart: Crie uma atribuição de diretiva para identificar recursos não compatíveis usando o módulo Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Para obter instruções sobre como editar uma política de segurança na Diretiva Azure, consulte [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

* Para obter instruções sobre como definir uma política entre assinaturas ou em grupos de gerenciamento usando a Política do Azure, consulte [O que é a política do Azure?](../governance/policy/overview.md)