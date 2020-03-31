---
title: Plantas de importação e exportação com powershell
description: Aprenda a trabalhar com as definições do seu projeto como código. Compartilhe, controle de origem e gerencie-os usando os comandos de exportação e importação.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873192"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Definições de projetos de importação e exportação com powershell

As plantas do Azure podem ser totalmente gerenciadas através do portal Azure. À medida que as organizações avançam no uso de Projetos, elas devem começar a pensar em definições de plantas como código gerenciado. Esse conceito é frequentemente referido como Infra-estrutura como Código (IaC). Tratar suas definições de projeto como código oferece vantagens adicionais além do que o portal Azure oferece. Esses benefícios incluem:

- Compartilhando definições de projeto
- Fazendo backup das definições do seu projeto
- Reutilização de definições de projeto em diferentes inquilinos ou assinaturas
- Colocando as definições do projeto no controle de origem
  - Teste automatizado de definições de plantas em ambientes de teste
  - Suporte a gasodutos de integração contínua e implantação contínua (CI/CD)

Quaisquer que sejam suas razões, gerenciar suas definições de projeto como código tem benefícios. Este artigo mostra como `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` usar os comandos e comandos no módulo [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume um conhecimento moderado de trabalho do Azure Blueprints. Se você ainda não fez isso, trabalhe através dos seguintes artigos:

- [Criar um blueprint no portal](../create-blueprint-portal.md)
- Leia sobre [as etapas de implantação](../concepts/deployment-stages.md) e o ciclo de vida do [projeto](../concepts/lifecycle.md)
- [Criação](../create-blueprint-powershell.md) e [gerenciamento de](./manage-assignments-ps.md) definições e atribuições de projetos com o PowerShell

Se ainda não estiver instalado, siga as instruções em [Adicionar o módulo Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** na Galeria do PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Estrutura de pastas de uma definição de projeto

Antes de analisar a exportação e importação de plantas, vamos ver como os arquivos que compõem a definição do projeto são estruturados. Uma definição de projeto deve ser armazenada em sua própria pasta.

> [!IMPORTANT]
> Se nenhum valor for **Name** passado para `Import-AzBlueprintWithArtifact` o parâmetro Nome do cmdlet, o nome da pasta em que a definição do projeto é armazenada será usado.

Junto com a definição do `blueprint.json`projeto, que deve ser nomeado, estão os artefatos que a definição do projeto é composta. Cada artefato deve estar `artifacts`na subpasta chamada .
Juntos, a estrutura da definição do seu projeto como arquivos JSON em pastas deve ser a seguinte:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Exporte sua definição de projeto

As etapas para exportar sua definição de projeto são simples. Exportar a definição do projeto pode ser útil para compartilhar, fazer backup ou colocar no controle de origem.

- **Projeto** [necessário]
  - Especifica a definição do projeto
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Path de saída** [necessário]
  - Especifica o caminho para salvar a definição de projeto arquivos JSON para
  - Os arquivos de saída estão em uma subpasta com o nome da definição do projeto
- **Versão** (opcional)
  - Especifica a versão a saída se o objeto de referência **Projeto** contiver referências a mais de uma versão.

1. Obtenha uma referência à definição do projeto `{subId}`para exportar a partir da assinatura representada como :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Use `Export-AzBlueprintWithArtifact` o cmdlet para exportar a definição de projeto especificada:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importe sua definição de projeto

Uma vez que você tenha uma [definição de projeto exportado](#export-your-blueprint-definition) ou tenha uma definição de projeto criada manualmente na [estrutura de pasta necessária,](#folder-structure-of-a-blueprint-definition)você pode importar essa definição de projeto para um grupo de gerenciamento ou assinatura diferente.

Para exemplos de definições de projeto incorporadas, consulte o [repo do Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Nome** [necessário]
  - Especifica o nome para a nova definição do projeto
- **InputPath** [obrigatório]
  - Especifica o caminho para criar a definição do projeto a partir de
  - Deve corresponder à [estrutura de pasta necessária](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcional)
  - O ID do grupo de gerenciamento para salvar a definição do projeto para se não o padrão de contexto atual
  - O **ManagementGroupId** ou **o SubscriptionId** devem ser especificados
- **SubscriptionId** (opcional)
  - O ID de assinatura para salvar a definição do projeto para se não o padrão de contexto atual
  - O **ManagementGroupId** ou **o SubscriptionId** devem ser especificados

1. Use `Import-AzBlueprintWithArtifact` o cmdlet para importar a definição de projeto especificada:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Uma vez que a definição do projeto seja importada, [atribua-a com o PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Para obter informações sobre a criação de definições avançadas de projetos, consulte os seguintes artigos:

- Use [parâmetros estáticos e dinâmicos.](../concepts/parameters.md)
- Personalize a [ordem de seqüenciamento do projeto](../concepts/sequencing-order.md).
- Proteger implantações com [bloqueio de recursos do projeto](../concepts/resource-locking.md).
- [Gerenciar plantas como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](../concepts/lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).