---
title: Crie ou edite um grupo dinâmico e obtenha status - Azure AD | Microsoft Docs
description: Como criar ou atualizar uma regra de associação em grupo no portal Azure e verificar seu status de processamento.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266371"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Crie ou atualize um grupo dinâmico no Azure Active Directory

No Azure Active Directory (Azure AD), você pode usar regras para determinar a adesão ao grupo com base nas propriedades do usuário ou do dispositivo. Este artigo diz como criar uma regra para um grupo dinâmico no portal Azure.
A adesão dinâmica é suportada para grupos de segurança ou grupos do Office 365. Quando uma regra de membro de grupo é aplicada, os atributos do usuário e do dispositivo são avaliados para correspondências com a regra de membro. Quando um atributo muda para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de membros. Os usuários e dispositivos são adicionados ou removidos se atenderem às condições de um grupo. Grupos de segurança podem ser usados para dispositivos ou usuários, mas grupos do Office 365 podem ser apenas grupos de usuários.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras suporta a construção até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não suportar a regra que você deseja criar, você pode usar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra dos relatórios diretos
- Definindo [a precedência do operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas;](groups-dynamic-membership.md#rules-with-complex-expressions) por exemplo,`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Você pode ver uma mensagem quando o construtor de regras não é capaz de exibir a regra. O construtor de regras não altera de forma alguma a sintaxe, validação ou processamento de regras dinâmicas de grupo.

![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

Para exemplos de sintaxe, propriedades suportadas, operadores e valores para uma regra de associação, consulte [Regras de associação dinâmicas para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com uma conta que esteja no administrador Global, administrador intune ou função de administrador de usuário no inquilino.
1. Procurar e selecionar **Grupos**.
1. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Selecione o comando para adicionar um novo grupo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na página **Grupo,** digite um nome e uma descrição para o novo grupo. Selecione um **tipo de associação** para usuários ou dispositivos e, em seguida, **selecione Adicionar consulta dinâmica**. O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua consulta de membros:
   1. Selecione **Obter propriedades de extensão personalizadas**
   1. Digite o ID do aplicativo e, em seguida, **selecione Propriedades de atualização**.
1. Depois de criar a regra, **selecione Salvar**.
1. Selecione **Criar** na página **do grupo Novo** para criar o grupo.

Se a regra inserida não for válida, uma explicação de por que a regra não pôde ser processada será exibida em uma notificação do Azure no portal. Leia atentamente para entender como corrigir a regra.

## <a name="to-update-an-existing-rule"></a>Para atualizar uma regra existente

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com uma conta que esteja no administrador Global, administrador do grupo, administrador do Intune ou função de administrador de usuário no inquilino.
1. Selecionar **grupos Todos** > **os grupos**.
1. Selecione um grupo para abrir seu perfil.
1. Na página de perfil do grupo, selecione **Regras de associação dinâmicas**. O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua regra de adesão:
   1. Selecione **Obter propriedades de extensão personalizadas**
   1. Digite o ID do aplicativo e, em seguida, **selecione Propriedades de atualização**.
1. Depois de atualizar a regra, **selecione Salvar**.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar email de boas-vindas

Quando um novo grupo office 365 é criado, uma notificação de e-mail bem-vinda é enviada aos usuários que são adicionados ao grupo. Posteriormente, se algum atributo de um usuário ou dispositivo mudar, todas as regras dinâmicas de grupo na organização são processadas para alterações de membros. Os usuários que são adicionados também recebem a notificação de boas-vindas. Você pode desligar esse comportamento no [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Verifique o status do processamento para obter uma regra

Veja o status do processamento de associação e a data da última atualização na página **Visão Geral** do grupo.
  
  ![exibição do status de grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

- **Avaliando**: a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
- **Processando**: as atualizações estão sendo processadas.
- **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
- **Erro de processamento**: O processamento não pôde ser concluído por causa de um erro na avaliação da regra de adesão.
- **Atualização em pausa**: as atualizações da regra de associação dinâmica foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

- &lt;**Data e hora**&gt;: A última vez que a adesão foi atualizada.
- **Em Andamento**: as atualizações estão em andamento no momento.
- **Desconhecido**: A última hora de atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada para todos os grupos dentro do inquilino por mais de 24 horas, um alerta será mostrado no topo de **todos os grupos**.

![processamento alertas de mensagem de erro](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
