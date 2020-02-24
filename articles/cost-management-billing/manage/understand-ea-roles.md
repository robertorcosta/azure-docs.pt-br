---
title: Entenda as funções de administrador do Enterprise no Azure | Microsoft Docs
description: Aprenda sobre as funções de administrador corporativo no Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9fab4d8ba0cf2e6f684a1b9de177084f8ce31604
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462183"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Entenda as funções administrativas do Contrato Azure Enterprise no Azure

Para ajudar a gerenciar o uso e o gasto de sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador corporativo
- Administrador corporativo (somente leitura)<sup>1</sup>
- Administrador de departamento
- Administrador de departamento (somente leitura)
- Proprietário da conta

<sup>1</sup> O contato de cobrança do contrato EA estará sob essa função.

Essas funções são específicas para gerenciar os Contratos Enterprise do Azure e são complementares às funções internas que o Azure controla para acessar os recursos. Para obter mais informações, consulte [Funções internas dos recursos do Azure](../../role-based-access-control/built-in-roles.md).

As seções a seguir descrevem as limitações e os recursos de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de usuários para funções de administrador

|Função| Limite de usuários|
|---|---|
|Administrador corporativo|Ilimitado|
|Administrador da empresa (somente leitura)|Ilimitado|
|Administrador de departamento|Ilimitado|
|Administrador de departamento (somente leitura)|Ilimitado|
|Proprietário da conta|Uma por conta<sup>2</sup>|

<sup>2</sup> Cada conta requer uma conta Microsoft exclusiva ou uma conta corporativa ou de estudante.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura de organização e permissões por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura)|Proprietário da conta|
|---|---|---|---|---|---|
|Exibir administradores de empresa|✔|✔|✘|✘|✘|
|Administradores da Empresa|✔|✘|✘|✘|✘|
|Exibir contatos de notificação<sup>3</sup> |✔|✔|✘|✘|✘|
|Adicionar ou remover contatos de notificação<sup>3</sup> |✔|✘|✘|✘|✘|
|Criar e gerenciar os departamentos |✔|✘|✘|✘|✘|
|Administradores de departamento do modo de exibição|✔|✔|✔|✔|✘|
|Adicionar ou remover administradores de departamento|✔|✘|✔|✘|✘|
|Exibir contas na inscrição |✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✘|
|Adicione contas para o registro e alterar o proprietário da conta|✔|✘|✔<sup>4</sup>|✘|✘|
|Criar e gerenciar assinaturas e permissões de assinatura|✘|✘|✘|✘|✔|

- <sup>3</sup> Os contatos de notificação recebem comunicações por email sobre o Contrato Enterprise do Azure.
- <sup>4</sup> A tarefa é limitada às contas em seu departamento.


## <a name="usage-and-costs-access-by-role"></a>Acesso de uso e os custos por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura) |Proprietário da conta|
|---|---|---|---|---|---|
|Exibir o saldo de crédito incluindo compromisso monetário|✔|✔|✘|✘|✘|
|Cotas de gastos do departamento de exibição|✔|✔|✘|✘|✘|
|Definir cotas de gastos do departamento|✔|✘|✘|✘|✘|
|Exibir a folha de preços EA da organização|✔|✔|✘|✘|✘|
|Exibir detalhes de uso e custo|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✔<sup>6</sup>|
|Gerenciar recursos no portal do Azure|✘|✘|✘|✘|✔|

- <sup>5</sup> Requer que o administrador corporativo habilite a política de **Encargos de visualização DA** no Enterprise Portal. O Administrador do Departamento pode, então, ver os detalhes do custo para o departamento.
- <sup>6</sup> Requer que o administrador corporativo habilite a política de **Encargos de visualização AO** no Enterprise Portal. O proprietário da conta pode ver detalhes do custo da conta.


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
