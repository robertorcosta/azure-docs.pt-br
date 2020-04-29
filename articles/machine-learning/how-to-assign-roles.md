---
title: Gerenciar funções em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como acessar um espaço de trabalho Azure Machine Learning usando o RBAC (controle de acesso baseado em função).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270089"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerenciar o acesso a um espaço de trabalho do Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a gerenciar o acesso a um espaço de trabalho do Azure Machine Learning. O [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview) é usado para gerenciar o acesso aos recursos do Azure. Os usuários em seu Azure Active Directory recebem funções específicas, que concedem acesso aos recursos. O Azure fornece funções internas e a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho Azure Machine Learning é um recurso do Azure. Assim como outros recursos do Azure, quando um novo espaço de trabalho Azure Machine Learning é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções internas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações somente leitura no espaço de trabalho. Os leitores podem listar e exibir ativos em um espaço de trabalho, mas não podem criar ou atualizar esses ativos. |
| **Colaborador** | Exiba, crie, edite ou exclua (onde aplicável) ativos em um espaço de trabalho. Por exemplo, os colaboradores podem criar um experimento, criar ou anexar um cluster de computação, enviar uma execução e implantar um serviço Web. |
| **Proprietário** | Acesso completo ao espaço de trabalho, incluindo a capacidade de exibir, criar, editar ou excluir ativos (onde aplicável) em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |

> [!IMPORTANT]
> O acesso à função pode ser definido para vários níveis no Azure. Por exemplo, alguém com acesso de proprietário a um espaço de trabalho pode não ter acesso de proprietário ao grupo de recursos que contém o espaço de trabalho. Para obter mais informações, consulte [como o RBAC funciona](/azure/role-based-access-control/overview#how-rbac-works).

Para obter mais informações sobre funções internas específicas, consulte [funções internas do Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você for um proprietário de um espaço de trabalho, poderá adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções a usuários. Use os links a seguir para descobrir como gerenciar o acesso:
- [Interface do usuário do portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Modelos do Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Se você instalou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), também poderá usar um comando da CLI para atribuir funções aos usuários.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de email de um usuário existente na instância do Azure Active Directory em que a assinatura pai do espaço de trabalho reside. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada

Se as funções internas forem insuficientes, você poderá criar funções personalizadas. As funções personalizadas podem ter permissões de recurso de leitura, gravação, exclusão e computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro Construa um arquivo JSON de definição de função que especifique a permissão e o escopo para a função. O exemplo a seguir define uma função personalizada denominada "cientista de dados" com escopo em um nível de espaço de trabalho específico:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Você pode alterar o `AssignableScopes` campo para definir o escopo dessa função personalizada no nível da assinatura, no nível do grupo de recursos ou em um nível de espaço de trabalho específico.

Essa função personalizada pode fazer tudo no espaço de trabalho, exceto pelas seguintes ações:

- Ele não pode criar ou atualizar um recurso de computação.
- Ele não pode excluir um recurso de computação.
- Ele não pode adicionar, excluir ou alterar atribuições de função.
- Ele não pode excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando de CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função fica disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal do Azure. Ou, você pode atribuir essa função a um usuário usando o comando `az ml workspace share` da CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](/azure/role-based-access-control/custom-roles).

Para obter mais informações sobre as operações (ações) utilizáveis com funções personalizadas, consulte [operações do provedor de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Perguntas frequentes


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Quais são as permissões necessárias para executar várias ações no serviço de Azure Machine Learning?

A tabela a seguir é um resumo de Azure Machine Learning atividades e as permissões necessárias para realizá-las no menor escopo. Como exemplo, se uma atividade puder ser executada com um escopo de espaço de trabalho (coluna 4), todo o escopo mais alto com essa permissão também funcionará automaticamente. Todos os caminhos nesta tabela são **caminhos relativos** para `Microsoft.MachineLearningServices/`.

| Atividade | Escopo de nível de assinatura | Escopo no nível do grupo de recursos | Escopo no nível do espaço de trabalho |
|---|---|---|---|
| Criar novo workspace | Não obrigatório | Proprietário ou colaborador | N/A (torna-se proprietário ou herda uma função de escopo maior após a criação) |
| Criar novo cluster de computação | Não obrigatório | Não obrigatório | Proprietário, colaborador ou função personalizada, permitindo:`workspaces/computes/write` |
| Criar nova VM de notebook | Não obrigatório | Proprietário ou colaborador | Impossível |
| Criar nova instância de computação | Não obrigatório | Não obrigatório | Proprietário, colaborador ou função personalizada, permitindo:`workspaces/computes/write` |
| Atividade do plano de dados como envio de execução, acesso a dados, implantação de modelo ou pipeline de publicação | Não obrigatório | Não obrigatório | Proprietário, colaborador ou função personalizada, permitindo:`workspaces/*/write` <br/> Observe que você também precisa de um repositório de dados registrado para o espaço de trabalho para permitir que o MSI acesse o dado em sua conta de armazenamento. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Como fazer listar todas as funções personalizadas em minha assinatura?

No CLI do Azure, execute o comando a seguir.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Como fazer encontrar a definição de função para uma função em minha assinatura?

No CLI do Azure, execute o comando a seguir. Observe que `<role-name>` deve estar no mesmo formato retornado pelo comando acima.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Como fazer atualizar uma definição de função?

No CLI do Azure, execute o comando a seguir.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Observe que você precisa ter permissões em todo o escopo da nova definição de função. Por exemplo, se essa nova função tiver um escopo entre três assinaturas, você precisará ter permissões em todas as três assinaturas. 

> [!NOTE]
> As atualizações de função podem levar 15 minutos a uma hora para serem aplicadas em todas as atribuições de função nesse escopo.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. Posso definir uma função que impede a atualização da edição do espaço de trabalho? 

Sim, você pode definir uma função que impede a atualização da edição do espaço de trabalho. Como a atualização do espaço de trabalho é uma chamada de PATCH no objeto de espaço de trabalho, você faz isso colocando `"NotActions"` a seguinte ação na matriz em sua definição JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quais permissões são necessárias para executar operações de cota em um espaço de trabalho? 

Você precisa de permissões de nível de assinatura para executar qualquer operação relacionada à cota no espaço de trabalho. Isso significa que a definição de cota de nível de assinatura ou cota de nível de espaço de trabalho para seus recursos de computação gerenciados só poderá ocorrer se você tiver permissões de gravação no escopo da assinatura. 


## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança empresarial](concept-enterprise-security.md)
- [Executar experimentos e inferência/Pontuação com segurança dentro de uma rede virtual](how-to-enable-virtual-network.md)
- [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
- [Operações de provedor de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
