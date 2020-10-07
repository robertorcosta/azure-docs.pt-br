---
title: Exportar recursos do Azure Policy
description: Aprenda a exportar recursos do Azure Policy para o GitHub, como definições de política e atribuições de política.
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 691e0a026c5f4f1a0a68c744ee81b1da8da9e70b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777081"
---
# <a name="export-azure-policy-resources"></a>Exportar recursos do Azure Policy

Este artigo fornece informações sobre como exportar os recursos existentes do Azure Policy. Exportar seus recursos é útil e recomendado para backup, mas também é uma etapa importante em sua jornada com governança de nuvem e tratar sua [política como código](../concepts/policy-as-code.md). Azure Policy recursos podem ser exportados por meio de [portal do Azure](#export-with-azure-portal), [CLI do Azure](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)e cada um dos SDKs com suporte.

## <a name="export-with-azure-portal"></a>Exportar com portal do Azure

Para exportar uma definição de política de portal do Azure, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Selecione **definições** no lado esquerdo da página Azure Policy.

1. Use o botão **exportar definições** ou selecione as reticências na linha de uma definição de política e, em seguida, selecione **Exportar definição**.

1. Selecione o botão **entrar com o GitHub** . Se você ainda não tiver autenticado com o GitHub para autorizar Azure Policy para exportar o recurso, revise o acesso que a [ação do GitHub](https://github.com/features/actions) precisa na nova janela que é aberta e selecione **autorizar AzureGitHubActions** para continuar com o processo de exportação. Depois de concluído, a nova janela é fechada automaticamente.

1. Na guia **noções básicas** , defina as seguintes opções e, em seguida, selecione a guia **políticas** ou **próximo: botão políticas** na parte inferior da página.

   - **Filtro de repositório**: Defina como _meus repositórios_ para ver apenas os repositórios que você possui ou _todos os repositórios_ para ver tudo o que você concedeu acesso à ação do github.
   - **Repositório**: defina para o repositório para o qual você deseja exportar os recursos de Azure Policy.
   - **Branch**: defina a ramificação no repositório. Usar uma ramificação que não seja o padrão é uma boa maneira de validar suas atualizações antes de mesclá-las em seu código-fonte.
   - **Diretório**: a _pasta de nível raiz_ para a qual exportar os recursos de Azure Policy. As subpastas nesse diretório são criadas com base em quais recursos são exportados.

1. Na guia **políticas** , defina o escopo a ser pesquisado selecionando as reticências e escolhendo uma combinação de grupos de gerenciamento, assinaturas ou grupos de recursos.
   
1. Use o botão **Adicionar definição (ões) de política** para pesquisar o escopo para os objetos a serem exportados. Na janela lateral que é aberta, selecione cada objeto a ser exportado. Filtre a seleção pela caixa de pesquisa ou pelo tipo. Depois de selecionar todos os objetos a serem exportados, use o botão **Adicionar** na parte inferior da página.

1. Para cada objeto selecionado, selecione as opções de exportação desejadas, como _somente definição_ ou _definição e atribuição (ões)_ para uma definição de política. Em seguida, selecione a guia **revisar + exportar** ou **próximo: botão revisar + exportar** na parte inferior da página.

   > [!NOTE]
   > Se a _definição de opção e a atribuição (ões)_ forem escolhidas, somente as atribuições de política dentro do escopo definido pelo filtro quando a definição de política for adicionada serão exportadas.

1. Na guia **revisar + exportar** , verifique a correspondência de detalhes e, em seguida, use o botão **Exportar** na parte inferior da página.

1. Verifique o repositório do GitHub, a ramificação e a _pasta de nível raiz_ para ver que os recursos selecionados agora são exportados para o controle do código-fonte.

Os recursos de Azure Policy são exportados para a seguinte estrutura dentro do repositório GitHub selecionado e da _pasta de nível raiz_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportar com CLI do Azure

Azure Policy definições, iniciativas e atribuições podem ser exportadas como JSON com [CLI do Azure](/cli/azure/install-azure-cli). Cada um desses comandos usa um parâmetro **Name** para especificar o objeto para o qual obter o JSON. A propriedade **Name** geralmente é um _GUID_ e não é o **DisplayName** do objeto.

- Definição – [Mostrar definição de política AZ](/cli/azure/policy/definition#az-policy-definition-show)
- Iniciativa- [conjunto de políticas de AZ-definição de definições](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- Atribuição- [AZ política de atribuição mostrar](/cli/azure/policy/assignment#az-policy-assignment-show)

Aqui está um exemplo de como obter o JSON para uma definição de política com o **nome** de _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportar com Azure PowerShell

Azure Policy definições, iniciativas e atribuições podem ser exportadas como JSON com [Azure PowerShell](/powershell/azure/). Cada um desses cmdlets usa um parâmetro **Name** para especificar o objeto para o qual obter o JSON. A propriedade **Name** geralmente é um _GUID_ e não é o **DisplayName** do objeto.

- Definição- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Iniciativa- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Atribuição- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Aqui está um exemplo de como obter o JSON para uma definição de política com o **nome** de _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
