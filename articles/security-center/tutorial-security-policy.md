---
title: Trabalhar com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança na central de segurança do Azure.
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
ms.openlocfilehash: 4ac6ac52d6d950d814a37e94ea2801c2ba8e4170
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521194"
---
# <a name="working-with-security-policies"></a>Trabalhando com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. 

## <a name="introduction-to-security-policies"></a>Introdução às políticas de segurança

Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir que você esteja em conformidade com os requisitos de segurança de sua empresa ou reguladores.

A central de segurança do Azure faz suas recomendações de segurança com base em suas políticas escolhidas. As políticas da central de segurança são baseadas em iniciativas de políticas criadas no Azure Policy. Você pode usar [Azure Policy](../governance/policy/overview.md) para gerenciar suas políticas e definir políticas em grupos de gerenciamento e em várias assinaturas.

A central de segurança oferece as seguintes opções para trabalhar com políticas de segurança:

* **Exibir e editar a política padrão interna** – quando você habilita a central de segurança, uma iniciativa interna denominada ' ASC padrão ' é automaticamente atribuída a todas as assinaturas registradas da central de segurança (camadas gratuitas ou Standard). Para personalizar essa iniciativa, você pode habilitar ou desabilitar políticas individuais dentro dela. Consulte a lista de [políticas de segurança internas](security-center-policy-definitions.md) para entender as opções disponíveis de forma integrada.

* **Adicione suas próprias políticas personalizadas** -se desejar personalizar as iniciativas de segurança aplicadas à sua assinatura, você poderá fazer isso na central de segurança. Em seguida, você receberá recomendações se os computadores não seguirem as políticas que você criar. Para obter instruções sobre como criar e atribuir políticas personalizadas, consulte [usando políticas de segurança personalizadas](custom-security-policies.md).

* **Adicionar políticas de conformidade regulatória** – o painel de conformidade regulatória da central de segurança mostra o status de todas as avaliações em seu ambiente no contexto de um padrão ou regulamento específico (como o cis do Azure, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Para saber mais, confira [Melhorar sua conformidade regulatória](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Gerenciando suas políticas de segurança

Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Security Center**, selecione **Security policy**.

    ![O painel Gerenciamento de Política](./media/security-center-policies/security-center-policy-mgt.png)

   Na tela **Gerenciamento de políticas**, você pode ver o número de grupos de gerenciamento, assinaturas e workspaces, bem como a estrutura do grupo de gerenciamento.

1. Selecione a assinatura ou o grupo de gerenciamento cujas políticas você deseja visualizar.

1. A página política de segurança para essa assinatura ou grupo de gerenciamento é exibida. Ele mostra as políticas disponíveis e atribuídas.

   ![tela de política](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se houver um rótulo "MG herdado" junto com a política padrão, significa que a política foi atribuída a um grupo de gerenciamento e herdada pela assinatura que você está exibindo.


1. Escolha entre as opções disponíveis nesta página:

    1. Para trabalhar com as políticas do setor, clique em **adicionar mais padrões**. Para obter mais informações, consulte [atualizar para pacotes de conformidade dinâmica](update-regulatory-compliance-packages.md).

    1. Para atribuir e gerenciar iniciativas personalizadas, clique em **Adicionar iniciativas personalizadas**. Para obter mais informações, consulte [usando políticas de segurança personalizadas](custom-security-policies.md).

    1. Para exibir e editar a política padrão, clique em **Exibir política efetiva** e prossiga conforme descrito abaixo. 

       ![tela de política](./media/security-center-policies/policy-screen.png)
       
       Essa tela de **política de segurança** reflete a ação executada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
       
       * Use os links na parte superior para abrir uma **atribuição** de política que se aplica à assinatura ou ao grupo de gerenciamento. Esses links permitem que você acesse a atribuição e edite ou desabilite a política. Por exemplo, se você vir que uma atribuição de política específica está efetivamente negando o Endpoint Protection, use o link para editar ou desabilitar a política.
       
       * Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. As configurações de cada política que se aplicam ao escopo são levadas em consideração e o resultado cumulativo das ações executadas pela política é mostrado. Por exemplo, se em uma atribuição da política estiver desabilitada, mas em outra definida como AuditIfNotExist, o efeito cumulativo aplicará AuditIfNotExist. O efeito mais ativo sempre tem precedência.
       
       * O efeito das políticas pode ser: Anexar, Auditar, AuditIfNotExists, Negar, DeployIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?

Você pode editar as políticas de segurança no portal Azure Policy usando a API REST ou o Windows PowerShell.

A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários recebem a função de *proprietário*, *colaborador*ou *leitor* para a assinatura do recurso. Além dessas funções, há duas funções de central de segurança específicas:

- **Leitor de segurança**: tem direitos de exibição para a Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não podem fazer alterações.
- **Administrador de segurança**: têm os mesmos direitos de exibição que o *leitor de segurança* e também podem atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="disable-security-policies"></a>Desabilitar políticas de segurança
Se a política de segurança padrão estiver gerando uma recomendação que não é relevante para o seu ambiente, você poderá interrompê-la desabilitando a definição de política que envia a recomendação.
Para obter mais informações sobre recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

1. Na central de segurança, na seção **conformidade do & de política** , clique em **política de segurança**.

   ![gerenciamento de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique na assinatura ou grupo de gerenciamento para o qual você deseja desabilitar a recomendação.

   > [!NOTE]
   > Lembre-se de que um grupo de gerenciamento aplica suas políticas a suas assinaturas. Portanto, se você desabilitar a política de uma assinatura e a assinatura pertencer a um grupo de gerenciamento que ainda usa a mesma política, você continuará a receber as recomendações de política. A política ainda será aplicada a partir do nível de gerenciamento e as recomendações ainda serão geradas.

1. Clique na política atribuída.

   ![desabilitar política](./media/tutorial-security-policy/security-policy.png)

1. Na seção **parâmetros** , procure a política que invoca a recomendação que você deseja desabilitar e, na lista suspensa, selecione **desabilitada**

   ![desabilitar política](./media/tutorial-security-policy/disable-policy.png)

1. Clique em **Salvar**.

   > [!NOTE]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre as políticas de segurança. Para obter informações relacionadas, consulte os seguintes artigos:

* Para obter instruções sobre como definir políticas usando o PowerShell, consulte [início rápido: criar uma atribuição de política para identificar recursos sem conformidade usando o módulo Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Para obter instruções sobre como editar uma política de segurança no Azure Policy, consulte [criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

* Para obter instruções sobre como definir uma política em assinaturas ou em grupos de gerenciamento usando Azure Policy, consulte [o que é Azure Policy?](../governance/policy/overview.md)