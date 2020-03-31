---
title: Criar ou atualizar funções personalizadas do Azure usando o portal Azure (Preview) - Azure RBAC
description: Saiba como criar funções personalizadas do Azure para o controle de acesso baseado em função do Azure (Azure RBAC) usando o portal Azure. Isso inclui como listar, criar, atualizar e excluir funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674864"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Criar ou atualizar funções personalizadas do Azure usando o portal Azure (Preview)

> [!IMPORTANT]
> As funções personalizadas do Azure usando o portal Azure estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se as [funções incorporadas do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, você poderá criar suas próprias funções personalizadas do Azure. Assim como as funções incorporadas, você pode atribuir funções personalizadas a usuários, grupos e diretores de serviços em escopos de grupos de assinatura e recursos. As funções personalizadas são armazenadas em um diretório do Azure AD (Azure Active Directory) e podem ser compartilhadas entre assinaturas. Cada diretório pode ter até 5000 funções personalizadas. As funções personalizadas podem ser criadas usando o portal Azure, Azure PowerShell, Azure CLI ou a API REST. Este artigo descreve como criar funções personalizadas usando o portal Azure (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Passo 1: Determine as permissões necessárias

O Azure tem milhares de permissões que você pode incluir potencialmente em sua função personalizada. Aqui estão quatro maneiras que você pode determinar as permissões que você deseja adicionar à sua função personalizada:

| Método | Descrição |
| --- | --- |
| Olhe para os papéis existentes | Você pode olhar para as funções existentes para ver quais permissões estão sendo usadas. Para obter mais informações, consulte [funções incorporadas do Azure](built-in-roles.md). |
| Busca de permissões por palavra-chave | Quando você cria uma função personalizada usando o portal Azure, você pode procurar permissões por palavra-chave. Por exemplo, você pode procurar por *permissões de máquina virtual* ou *faturamento.* Essa funcionalidade de pesquisa é descrita mais tarde no [Passo 4: Permissões](#step-4-permissions). |
| Baixe todas as permissões | Quando você cria uma função personalizada usando o portal Azure, você pode baixar todas as permissões como um arquivo CSV e, em seguida, pesquisar este arquivo. No **painel Adicionar permissões,** clique no botão **Baixar todas as permissões** para baixar todas as permissões. Para obter mais informações sobre o painel de permissões Adicionar, consulte [Passo 4: Permissões](#step-4-permissions). |
| Veja as permissões nos docs | Você pode visualizar as permissões disponíveis nas operações do [provedor de recursos do Azure Resource Manager](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Passo 2: Escolha como começar

Há três maneiras que você pode começar a criar um papel personalizado. Você pode clonar uma função existente, começar do zero ou começar com um arquivo JSON. A maneira mais fácil é encontrar um papel existente que tenha a maioria das permissões que você precisa e, em seguida, cloná-lo e modificá-lo para o seu cenário. 

### <a name="clone-a-role"></a>Clonar um papel

Se uma função existente não tiver as permissões necessárias, você pode cloná-la e, em seguida, modificar as permissões. Siga estas etapas para começar a clonar um papel.

1. No portal Azure, abra um grupo de assinatura ou recurso onde você deseja que a função personalizada seja atribuída e, em seguida, abra o **controle de acesso (IAM)**.

    A captura de tela a seguir mostra a página de controle de acesso (IAM) aberta para uma assinatura.

    ![Página de controle de acesso (IAM) para uma assinatura](./media/custom-roles-portal/access-control-subscription.png)

1. Clique na guia **Funções** para ver uma lista de todas as funções integradas e personalizadas.

1. Procure por um papel que você deseja clonar, como o papel de Leitor de Faturamento.

1. No final da linha, clique na elipse (**...**) e, em seguida, clique em **Clone**.

    ![Menu de contexto de clone](./media/custom-roles-portal/clone-menu.png)

    Isso abre o editor de funções personalizadas com o Clone uma opção **de função** selecionada.

1. Proceder ao [Passo 3: Noções básicas](#step-3-basics).

### <a name="start-from-scratch"></a>Começar do zero

Se preferir, você pode seguir estes passos para iniciar uma função personalizada do zero.

1. No portal Azure, abra um grupo de assinatura ou recurso onde você deseja que a função personalizada seja atribuída e, em seguida, abra o **controle de acesso (IAM)**.

1. Clique **em Adicionar** e clique em Adicionar função personalizada **(visualização)**.

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isso abre o editor de funções personalizadas com a opção **Iniciar do zero** selecionada.

1. Proceder ao [Passo 3: Noções básicas](#step-3-basics).

### <a name="start-from-json"></a>Comece de JSON

Se preferir, você pode especificar a maioria dos valores de função personalizada em um arquivo JSON. Você pode abrir o arquivo no editor de funções personalizadas, fazer alterações adicionais e, em seguida, criar a função personalizada. Siga estas etapas para começar com um arquivo JSON.

1. Crie um arquivo JSON que tenha o seguinte formato:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. No arquivo JSON, especifique valores para as várias propriedades. Aqui está um exemplo com alguns valores adicionados. Para obter informações sobre as diferentes propriedades, consulte [Entender definições de função](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. No portal Azure, abra a página **controle de acesso (IAM).**

1. Clique **em Adicionar** e clique em Adicionar função personalizada **(visualização)**.

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isso abre o editor de funções personalizadas.

1. Na guia Basics, em **Permissões de Linha de Base,** selecione **Iniciar a partir de JSON**.

1. Ao lado da Selecionar uma caixa de arquivos, clique no botão pasta para abrir a caixa de diálogo Abrir.

1. Selecione seu arquivo JSON e clique em **Abrir**.

1. Proceder ao [Passo 3: Noções básicas](#step-3-basics).

## <a name="step-3-basics"></a>Passo 3: Noções básicas

Na guia **Básico,** você especifica o nome, a descrição e as permissões de linha de base para sua função personalizada.

1. Na **caixa nome de função Personalizado,** especifique um nome para a função personalizada. O nome deve ser único para o diretório Azure AD. O nome pode incluir letras, números, espaços e caracteres especiais.

1. Na caixa **Descrição,** especifique uma descrição opcional para a função personalizada. Esta será a dica de ferramenta para a função personalizada.

    A opção **de permissões de linha de base** já deve ser definida com base na etapa anterior, mas você pode alterar.

    ![Guia básico com valores especificados](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Passo 4: Permissões

Na guia **Permissões,** você especifica as permissões para sua função personalizada. Dependendo se você clonou uma função ou se você começou com JSON, a guia Permissões já pode listar algumas permissões.

![Guia de permissões de criar função personalizada](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Adicionar ou remover permissões

Siga estas etapas para adicionar ou remover permissões para sua função personalizada.

1. Para adicionar permissões, clique **em Adicionar permissões** para abrir o painel Adicionar permissões.

    Este painel lista todas as permissões disponíveis agrupadas em diferentes categorias em um formato de cartão. Cada categoria representa um *provedor de recursos*, que é um serviço que fornece recursos do Azure.

1. Na busca por uma caixa **de permissões,** digite uma string para procurar permissões. Por exemplo, procure *fatura* para encontrar permissões relacionadas à fatura.

    Uma lista de cartões de provedor de recursos será exibida com base na seqüência de pesquisa. Para obter uma lista de como os provedores de recursos mapeiam os serviços do Azure, consulte [provedores de recursos para serviços do Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Adicionar painel de permissões com provedor de recursos](./media/custom-roles-portal/add-permissions-provider.png)

1. Clique em um cartão de provedor de recursos que pode ter as permissões que você deseja adicionar à sua função personalizada, como **o Microsoft Billing**.

    Uma lista das permissões de gerenciamento para esse provedor de recursos é exibida com base na seqüência de pesquisa.

    ![Adicionar lista de permissões](./media/custom-roles-portal/add-permissions-list.png)

1. Se você estiver procurando permissões que se aplicam ao plano de dados, clique em **Ações de dados**. Caso contrário, deixe as ações alternadas **definidas** para Ações para listar permissões que se aplicam ao plano de gerenciamento. Para obter mais informações sobre as diferenças entre o plano de gerenciamento e o plano de dados, consulte [Gestão e operações de dados](role-definitions.md#management-and-data-operations).

1. Se necessário, atualize a seqüência de pesquisa para refinar ainda mais sua pesquisa.

1. Depois de encontrar uma ou mais permissões que deseja adicionar à sua função personalizada, adicione uma marca de seleção ao lado das permissões. Por exemplo, adicione uma marca de seleção ao lado **de Outra : Baixe fatura** para adicionar a permissão para baixar faturas.

1. Clique **em Adicionar** para adicionar a permissão à sua lista de permissões.

    A permissão é `Actions` adicionada `DataActions`como um ou um .

    ![Permissão adicionada](./media/custom-roles-portal/permissions-list-add.png)

1. Para remover permissões, clique no ícone excluir no final da linha. Neste exemplo, uma vez que um usuário não precisará `Microsoft.Support/*` da capacidade de criar tickets de suporte, a permissão pode ser excluída.

### <a name="add-wildcard-permissions"></a>Adicionar permissões curinga

Dependendo de como você escolheu começar, você pode\*ter permissões com curingas ( ) em sua lista de permissões. Um curinga ()\*estende uma permissão para tudo o que corresponde à seqüência que você fornece. Por exemplo, suponha que você queria adicionar todas as permissões relacionadas à Gestão de Custos do Azure e às exportações. Você pode adicionar todas essas permissões:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Em vez de adicionar todas essas permissões, você poderia apenas adicionar uma permissão curinga. Por exemplo, a seguinte permissão curinga é equivalente às cinco permissões anteriores. Isso também incluiria quaisquer permissões futuras de exportação que possam ser adicionadas.

```
Microsoft.CostManagement/exports/*
```

Se você quiser adicionar uma nova permissão curinga, você não pode adicioná-la usando o painel **adicionar permissões.** Para adicionar uma permissão curinga, você tem que adicioná-la manualmente usando a guia **JSON.** Para obter mais informações, consulte [passo 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Exclua permissões

Se a sua função\*tiver uma permissão curinga () e você quiser excluir ou subtrair permissões específicas dessa permissão curinga, você pode excluí-las. Por exemplo, digamos que você tenha a seguinte permissão curinga:

```
Microsoft.CostManagement/exports/*
```

Se você não quiser permitir que uma exportação seja excluída, você pode excluir a seguinte permissão de exclusão:

```
Microsoft.CostManagement/exports/delete
```

Quando você exclui uma permissão, `NotActions` `NotDataActions`ela é adicionada como a ou . As permissões de gerenciamento eficazes são `Actions` calculadas adicionando todas `NotActions`as e, em seguida, subtraindo todas as . As permissões de dados eficazes são `DataActions` calculadas adicionando todas `NotDataActions`as e, em seguida, subtraindo todas as .

> [!NOTE]
> Excluir uma permissão não é o mesmo que negar. Excluir permissões é simplesmente uma maneira conveniente de subtrair permissões de uma permissão curinga.

1. Para excluir ou subtrair uma permissão de uma permissão curinga permitida, clique **em Excluir permissões** para abrir o painel Exclua permissões.

    Neste painel, você especifica o gerenciamento ou as permissões de dados que são excluídas ou subtraídas.

1. Depois de encontrar uma ou mais permissões que deseja excluir, adicione uma marca de seleção ao lado das permissões e clique no botão **Adicionar.**

    ![Exclua o painel de permissões - permissão selecionada](./media/custom-roles-portal/exclude-permissions-select.png)

    A permissão é `NotActions` `NotDataActions`adicionada como um ou .

    ![Permissão excluída](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Passo 5: Escopos atribuídos

Na guia **Escopos atribuídos,** você especifica onde sua função personalizada está disponível para atribuição, como assinatura ou grupo de recursos. Dependendo de como você escolheu iniciar, esta guia pode listar o escopo onde você abriu a página de controle de acesso (IAM). A configuração do escopo atribuído ao escopo raiz ("/") não é suportada. Para esta visualização, você não pode adicionar um grupo de gerenciamento como um escopo atribuível.

1. Clique **em Adicionar escopos atribuídos** para abrir o painel Adicionar escopos atribuídos.

    ![Guia de escopos atribuídos](./media/custom-roles-portal/assignable-scopes.png)

1. Clique em um ou mais escopos que você deseja usar, normalmente sua assinatura.

    ![Adicionar escopos atribuíveis](./media/custom-roles-portal/add-assignable-scopes.png)

1. Clique no botão **Adicionar** para adicionar seu escopo atribuível.

## <a name="step-6-json"></a>Passo 6: JSON

Na guia **JSON,** você vê sua função personalizada formatada em JSON. Se você quiser, você pode editar diretamente o JSON. Se você quiser adicionar uma\*permissão curinga ( ) você deve usar esta guia.

1. Para editar o JSON, clique **em Editar**.

    ![Guia JSON mostrando função personalizada](./media/custom-roles-portal/json.png)

1. Faça alterações no JSON.

    Se o JSON não formatado corretamente, você verá uma linha irregular vermelha e um indicador na sarjeta vertical.

1. Quando terminar a edição, clique **em Salvar**.

## <a name="step-7-review--create"></a>Passo 7: Revisão + criar

Na guia **'Revisar + criar',** você pode rever suas configurações de função personalizadas.

1. Revise suas configurações de função personalizadas.

    ![Guia Examinar + criar](./media/custom-roles-portal/review-create.png)

1. Clique **em Criar** para criar sua função personalizada.

    Após alguns momentos, uma caixa de mensagem aparece indicando que sua função personalizada foi criada com sucesso.

    ![Criar mensagem de função personalizada](./media/custom-roles-portal/custom-role-success.png)

    Se fordetectado algum erro, uma mensagem será exibida.

    ![Revisão + criar erro](./media/custom-roles-portal/review-create-error.png)

1. Veja sua nova função personalizada na lista **De Papéis.** Se você não ver sua função personalizada, clique **em Atualizar**.

     Pode levar alguns minutos para que seu papel personalizado apareça em todos os lugares.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Siga estas etapas para ver suas funções personalizadas.

1. Abra um grupo de assinatura ou recurso e, em seguida, abra **o controle de acesso (IAM)**.

1. Clique na guia **Funções** para ver uma lista de todas as funções integradas e personalizadas.

1. Na lista **Tipo,** selecione **CustomRole** para apenas ver suas funções personalizadas.

    Se você acabou de criar sua função personalizada e não a verá na lista, clique em **Atualizar**.

    ![Lista de papéis personalizada](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

1. Como descrito anteriormente neste artigo, abra sua lista de funções personalizadas.

1. Clique na elipse (**...**) para a função personalizada que deseja atualizar e clique em **Editar**. Observe que você não pode atualizar funções incorporadas.

    O papel personalizado é aberto no editor.

    ![Menu de funções personalizado](./media/custom-roles-portal/edit-menu.png)

1. Use as diferentes guias para atualizar a função personalizada.

1. Depois de terminar suas alterações, clique na guia **'Revisar + criar** para revisar suas alterações.

1. Clique no botão **Atualizar** para atualizar sua função personalizada.

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

1. Como descrito anteriormente neste artigo, abra sua lista de funções personalizadas.

1. Remova quaisquer atribuições de função que usem a função personalizada.

1. Clique na elipse (**...**) para a função personalizada que deseja excluir e clique em **Excluir**.

    ![Menu de funções personalizado](./media/custom-roles-portal/delete-menu.png)

    Pode levar alguns minutos para que sua função personalizada seja completamente excluída.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar uma função personalizada usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Funções personalizadas no Azure](custom-roles.md)
- [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)
