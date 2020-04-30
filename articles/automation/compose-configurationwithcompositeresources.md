---
title: Compondo configurações de DSC na configuração de estado de automação do Azure usando recursos de composição
description: Saiba como compor configurações usando recursos de composição na configuração de estado da automação do Azure.
keywords: dsc do powershell, configuração de estado desejada, powershell dsc azure, recursos compostos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682925"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Compondo configurações de DSC na configuração de estado de automação do Azure usando recursos de composição

Quando você precisa gerenciar recursos com mais de uma DSC (configuração de estado desejado), o melhor caminho é usar recursos de [composição](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC em outra configuração. O uso de recursos compostos permite que você crie configurações complexas, permitindo que os recursos compostos subjacentes sejam gerenciados e compilados individualmente.

A Automação do Azure permite a [importação e a compilação de recursos compostos](automation-dsc-compile.md). Depois de importar os recursos de composição para sua conta de automação, você poderá usar a configuração de estado da automação do Azure por meio do recurso **configuração de estado (DSC** no portal do Azure.

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração a partir de recursos compostos

Antes de atribuir uma configuração feita de recursos de composição no portal do Azure, você deve compor a configuração. A composição usa a **configuração de Compose** na página de configuração de estado (DSC) enquanto estiver nas **configurações** ou na guia **configurações compiladas** .

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página conta de automação, selecione **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** ou **configurações compiladas** e, em seguida, clique em **compor configuração** no menu na parte superior da página.
1. Na etapa **Básico**, forneça o novo nome de configuração (obrigatório) e clique em qualquer lugar na linha de cada recurso composto que você deseja incluir em sua nova configuração. Em seguida, clique em **Avançar** ou clique no passo **código fonte**. Para as etapas a seguir, `PSExecutionPolicy` selecionamos `RenameAndDomainJoin` e compostos recursos.
   ![Captura de tela da etapa básica da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O **código-fonte** etapa mostra a aparência a configuração composta dos recursos de composição selecionados. Você pode ver a mesclagem de todos os parâmetros e como eles são passados para o recurso composto. Quando você terminar de revisar o novo código-fonte, clique em **Próximo** ou clique na etapa **Parâmetros**.
   ![Captura de tela da etapa de código de origem da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **parâmetros** , o parâmetro para cada recurso de composição é exposto para que os valores possam ser fornecidos. Se um parâmetro tiver uma descrição, ele será exibido ao lado do campo do parâmetro. Se um parâmetro for do `PSCredential` tipo, o menu suspenso fornecerá uma lista de objetos de **credencial** na conta de automação atual. Uma opção **+ Adicionar uma credencial** também está disponível. Depois que todos os parâmetros necessários tiverem sido fornecidos, clique em **Salvar e compilar**.
   ![Captura de tela da etapa de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Depois que a nova configuração é salva, ela é enviada para compilação. O status do trabalho de compilação pode ser visualizado como qualquer configuração importada. Para mais informações, consulte [Visualizando um trabalho de compilação](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando a compilação for concluída com êxito, a nova configuração aparecerá na guia **configurações compiladas** . Em seguida, você pode atribuir a configuração a um nó gerenciado, usando as etapas em [reatribuir um nó a uma configuração de nó diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber como integrar nós, confira [máquinas de integração para o gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
