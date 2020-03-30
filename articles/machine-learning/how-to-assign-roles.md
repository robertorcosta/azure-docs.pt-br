---
title: Gerencie funções em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como acessar um espaço de trabalho do Azure Machine Learning usando o RBAC (Role-Based Access Control).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270089"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerencie o acesso a um espaço de trabalho de aprendizado de máquina do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a gerenciar o acesso a um espaço de trabalho de Machine Learning do Azure. [O RBAC (Role-based Access Control, controle de acesso baseado em função)](/azure/role-based-access-control/overview) é usado para gerenciar o acesso aos recursos do Azure. Os usuários do Seu Diretório Ativo do Azure são atribuídos funções específicas, que concedem acesso aos recursos. O Azure fornece tanto funções incorporadas quanto a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho de aprendizado de máquina do Azure é um recurso do Azure. Como outros recursos do Azure, quando um novo espaço de trabalho de Aprendizado de Máquina do Azure é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções incorporadas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações somente leitura no espaço de trabalho. Os leitores podem listar e visualizar ativos em um espaço de trabalho, mas não podem criar ou atualizar esses ativos. |
| **Colaborador** | Exibir, criar, editar ou excluir (quando aplicável) ativos em um espaço de trabalho. Por exemplo, os colaboradores podem criar um experimento, criar ou anexar um cluster de computação, enviar uma execução e implantar um serviço web. |
| **Proprietário** | Acesso total ao espaço de trabalho, incluindo a capacidade de visualizar, criar, editar ou excluir (quando aplicável) ativos em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |

> [!IMPORTANT]
> O acesso à função pode ser escopo para vários níveis no Azure. Por exemplo, alguém com acesso do proprietário a um espaço de trabalho pode não ter acesso do proprietário ao grupo de recursos que contém o espaço de trabalho. Para obter mais informações, consulte [Como funciona o RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Para obter mais informações sobre funções incorporadas específicas, consulte [Funções incorporadas para o Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você é dono de um espaço de trabalho, você pode adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções aos usuários. Use os seguintes links para descobrir como gerenciar o acesso:
- [Azure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Modelos do Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Se você tiver instalado o Cli de aprendizagem de máquina do [Azure,](reference-azure-machine-learning-cli.md)você também poderá usar um comando CLI para atribuir funções aos usuários.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de e-mail de um usuário existente no caso do Azure Active Directory, onde a assinatura pai do espaço de trabalho vive. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada

Se as funções incorporadas forem insuficientes, você pode criar funções personalizadas. As funções personalizadas podem ter lido, escrito, excluído e computado permissões de recursos nesse espaço de trabalho. Você pode disponibilizar a função em um nível específico de espaço de trabalho, um nível específico de grupo de recursos ou um nível específico de assinatura.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro construa uma definição de função arquivo JSON que especifique a permissão e o escopo para a função. O exemplo a seguir define uma função personalizada chamada "Cientista de Dados" escopo em um nível específico de espaço de trabalho:

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

Você pode `AssignableScopes` alterar o campo para definir o escopo dessa função personalizada no nível de assinatura, no nível do grupo de recursos ou em um nível específico de espaço de trabalho.

Esta função personalizada pode fazer tudo no espaço de trabalho, exceto as seguintes ações:

- Ele não pode criar ou atualizar um recurso de computação.
- Ele não pode excluir um recurso de computação.
- Ele não pode adicionar, excluir ou alterar atribuições de função.
- Não pode excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função fica disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal Azure. Ou, você pode atribuir essa função a `az ml workspace share` um usuário usando o comando CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Para obter mais informações sobre funções personalizadas, consulte [Funções personalizadas para os recursos do Azure](/azure/role-based-access-control/custom-roles).

Para obter mais informações sobre as operações (ações) utilizáveis com funções personalizadas, consulte [operações do provedor de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Perguntas frequentes


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Quais são as permissões necessárias para realizar várias ações no serviço de Machine Learning do Azure?

A tabela a seguir é um resumo das atividades de Machine Learning do Azure e as permissões necessárias para realizá-las no mínimo. Como exemplo, se uma atividade pode ser realizada com um escopo de espaço de trabalho (Coluna 4), então todo escopo superior com essa permissão também funcionará automaticamente. Todos os caminhos nesta `Microsoft.MachineLearningServices/`tabela são **caminhos relativos** para .

| Atividade | Escopo de nível de assinatura | Escopo de nível de grupo de recursos | Escopo em nível de espaço de trabalho |
|---|---|---|---|
| Criar novo workspace | Não obrigatório | Proprietário ou contribuinte | N/A (torna-se Proprietário ou herda função de escopo mais alto após a criação) |
| Criar um novo cluster de computação | Não obrigatório | Não obrigatório | Proprietário, contribuinte ou função personalizada permitindo:`workspaces/computes/write` |
| Criar novo Notebook VM | Não obrigatório | Proprietário ou contribuinte | Impossível |
| Criar nova instância de computação | Não obrigatório | Não obrigatório | Proprietário, contribuinte ou função personalizada permitindo:`workspaces/computes/write` |
| Atividade de plano de dados como enviar run, acessar dados, implantar modelo ou publicar pipeline | Não obrigatório | Não obrigatório | Proprietário, contribuinte ou função personalizada permitindo:`workspaces/*/write` <br/> Observe que você também precisa de um datastore registrado no espaço de trabalho para permitir que o MSI acesse dados em sua conta de armazenamento. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Como listo todas as funções personalizadas da minha assinatura?

Na CLI do Azure, execute o seguinte comando.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Como encontrar a definição de papel para um papel na minha assinatura?

Na CLI do Azure, execute o seguinte comando. Observe `<role-name>` que deve estar no mesmo formato retornado pelo comando acima.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Como atualizar uma definição de função?

Na CLI do Azure, execute o seguinte comando.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Observe que você precisa ter permissões em todo o escopo de sua nova definição de função. Por exemplo, se essa nova função tiver um escopo em três assinaturas, você precisa ter permissões nas três assinaturas. 

> [!NOTE]
> As atualizações de função podem levar de 15 minutos a uma hora para serem aplicadas em todas as atribuições de função nesse escopo.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. Posso definir um papel que impede a atualização do workspace Edition? 

Sim, você pode definir uma função que impede a atualização do workspace Edition. Uma vez que a atualização do espaço de trabalho é uma chamada PATCH `"NotActions"` no objeto do espaço de trabalho, você faz isso colocando a seguinte ação na matriz em sua definição JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quais permissões são necessárias para realizar operações de cotas em um espaço de trabalho? 

Você precisa de permissões de nível de assinatura para executar qualquer operação relacionada à cota no espaço de trabalho. Isso significa que definir a cota de nível de assinatura ou a cota de nível de espaço de trabalho para seus recursos de computação gerenciados só pode acontecer se você tiver permissões de gravação no escopo da assinatura. 


## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança empresarial](concept-enterprise-security.md)
- [Execute com segurança experimentos e inferência/pontuação dentro de uma rede virtual](how-to-enable-virtual-network.md)
- [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
- [Operações de provedor de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
