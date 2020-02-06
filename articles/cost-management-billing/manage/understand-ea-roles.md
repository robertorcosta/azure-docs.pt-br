---
title: Entenda as funções de administrador do Enterprise no Azure | Microsoft Docs
description: Aprenda sobre as funções de administrador corporativo no Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2020
ms.author: banders
ms.openlocfilehash: ee918f0f49acf85486999fe9314758286bb57126
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023320"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Entenda as funções administrativas do Contrato Azure Enterprise no Azure

Para ajudar a gerenciar o uso e o gasto de sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador corporativo
- Administrador da empresa (somente leitura)
- Administrador de departamento
- Administrador de departamento (somente leitura)
- Proprietário da conta
 
Essas funções são específicas para gerenciar os Contratos Enterprise do Azure e são complementares às funções internas que o Azure controla para acessar os recursos. Para obter mais informações, consulte [Funções internas dos recursos do Azure](../../role-based-access-control/built-in-roles.md).

As seções a seguir descrevem as limitações e os recursos de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de usuários para funções de administrador

|Função| Limite de usuários|
|---|---|
|Administrador corporativo|Ilimitado|
|Administrador da empresa (somente leitura)|Ilimitado|
|Administrador de departamento|Ilimitado|
|Administrador de departamento (somente leitura)|Ilimitado|
|Proprietário da conta|1 por conta<sup>1</sup>|

<sup>1</sup> Cada conta requer uma conta exclusiva da Microsoft ou uma conta de trabalho ou escola.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura de organização e permissões por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura)|Proprietário da conta|
|---|---|---|---|---|---|
|Exibir administradores de empresa|✔|✔|✘|✘|✘|
|Administradores da Empresa|✔|✘|✘|✘|✘|
|Exibir contatos de notificação<sup>2</sup> |✔|✔|✘|✘|✘|
|Adicionar ou remover contatos de notificação <sup>2</sup> |✔|✘|✘|✘|✘|
|Criar e gerenciar os departamentos |✔|✘|✘|✘|✘|
|Administradores de departamento do modo de exibição|✔|✔|✔|✔|✘|
|Adicionar ou remover administradores de departamento|✔|✘|✔|✘|✘|
|Exibir contas na inscrição |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Adicione contas para o registro e alterar o proprietário da conta|✔|✘|✔<sup>3</sup>|✘|✘|
|Criar e gerenciar assinaturas e permissões de assinatura|✘|✘|✘|✘|✔|

- <sup>2</sup> Os contatos de notificação recebem comunicações por e-mail sobre o Contrato do Azure Enterprise.
- <sup>3</sup> tarefa é limitada às contas em seu departamento.


## <a name="usage-and-costs-access-by-role"></a>Acesso de uso e os custos por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura) |Proprietário da conta|
|---|---|---|---|---|---|
|Exibir o saldo de crédito incluindo compromisso monetário|✔|✔|✘|✘|✘|
|Cotas de gastos do departamento de exibição|✔|✔|✘|✘|✘|
|Definir cotas de gastos do departamento|✔|✘|✘|✘|✘|
|Exibir a folha de preços EA da organização|✔|✔|✘|✘|✘|
|Exibir detalhes de uso e custo|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Gerenciar recursos no portal do Azure|✘|✘|✘|✘|✔|

- <sup>4</sup> Requer que o administrador corporativo habilite a política de **custos de exibição de DA** no portal da empresa. O Administrador do Departamento pode, então, ver os detalhes do custo para o departamento.
- <sup>5</sup> Requer que o administrador corporativo ative a política de **custos de exibição AO** no portal da empresa. O proprietário da conta pode ver detalhes do custo da conta.


## <a name="pricing-in-azure-portal"></a>Preço no portal do Azure

Você pode ver preços diferentes no portal do Azure, dependendo de sua função administrativa e de como as políticas de cobranças de visualização são definidas pelo administrador corporativo. As duas políticas no portal da empresa que afetam o preço que você vê no portal do Azure são:

- Encargos de visualização DA
- Encargos de visualização AO

Para saber como definir essas políticas, consulte [Gerenciar acesso às informações de faturamento do Azure](manage-billing-access.md).

A tabela a seguir mostra o relacionamento entre as funções de administração do Contrato Enterprise, a política de cobranças de visualização, a função de controle de acesso baseado em função (RBAC) no portal do Azure e o preço que você vê no portal do Azure. O administrador corporativo sempre vê detalhes de uso com base no preço do EA da organização. No entanto, o administrador do departamento e o proprietário da conta veem exibições de preços diferentes com base na política de cobrança de visualização e na função de RBAC. A função Administrador de departamento listada na tabela a seguir refere-se às funções Admin de departamento e Admin de departamento (somente leitura).

|Função de administrador do Contrato Enterprise|Política de encargos de exibição para função|Função RBAC|Exibição de preço|
|---|---|---|---|
|Proprietário da conta ou administrador de departamento|✔ Habilitado|Proprietário|Preços de EA da organização|
|Proprietário da conta ou administrador de departamento|✘ Desabilitado|Proprietário|Preço de varejo|
|Proprietário da conta ou administrador de departamento|✔ Habilitado |none|Não há preços|
|Proprietário da conta ou administrador de departamento|✘ Desabilitado |none|Não há preços|
|Nenhum|Não aplicável |Proprietário|Preço de varejo|

Você define a função de administrador da empresa e exibe as políticas de cobranças no portal da empresa. A função RBAC pode ser atualizada no portal do Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso a informações de faturamento do Azure](manage-billing-access.md)
- [Gerenciar acesso usando o RBAC e o Portal do Azure](../../role-based-access-control/role-assignments-portal.md)
- [Funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md)
