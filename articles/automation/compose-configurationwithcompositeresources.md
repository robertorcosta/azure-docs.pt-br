---
title: Compondo configurações de DSC na configuração do estado de automação do Azure usando recursos compostos
description: Aprenda a compor configurações usando recursos compostos na configuração do Estado de Automação do Azure.
keywords: dsc do powershell, configuração de estado desejada, powershell dsc azure, recursos compostos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682925"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Compondo configurações de DSC na configuração do estado de automação do Azure usando recursos compostos

Quando você precisa gerenciar recursos com mais de uma única configuração de estado desejada (DSC), o melhor caminho é usar [recursos compostos](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC em outra configuração. O uso de recursos compostos permite criar configurações complexas, permitindo que os recursos compostos subjacentes sejam gerenciados e construídos individualmente.

A Automação do Azure permite a [importação e a compilação de recursos compostos](automation-dsc-compile.md). Depois de importar recursos compostos para sua conta de automação, você pode usar a configuração do estado de automação do Azure através da **configuração do estado (recurso DSC** no portal Azure.

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração a partir de recursos compostos

Antes de atribuir uma configuração feita a partir de recursos compostos no portal Azure, você deve compor a configuração. A composição usa **configuração** de composição na página DSC (Configuração do estado) enquanto na **guia Configurações** ou **configurações compiladas.**

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta Automação, selecione **Configuração de Estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração Estado (DSC), clique na **guia Configurações** ou na guia **Configurações Compiladas** e clique em Compor a **configuração** no menu na parte superior da página.
1. Na etapa **Básico**, forneça o novo nome de configuração (obrigatório) e clique em qualquer lugar na linha de cada recurso composto que você deseja incluir em sua nova configuração. Em seguida, clique em **Avançar** ou clique no passo **código fonte**. Para as seguintes `PSExecutionPolicy` etapas, selecionamos e compôs `RenameAndDomainJoin` recursos.
   ![Captura de tela da etapa básica da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O **código-fonte** etapa mostra a aparência a configuração composta dos recursos de composição selecionados. Você pode ver a mesclagem de todos os parâmetros e como eles são passados para o recurso composto. Quando você terminar de revisar o novo código-fonte, clique em **Próximo** ou clique na etapa **Parâmetros**.
   ![Captura de tela da etapa de código de origem da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **Parâmetros,** o parâmetro para cada recurso composto é exposto para que os valores possam ser fornecidos. Se um parâmetro tiver uma descrição, ele será exibido ao lado do campo do parâmetro. Se um parâmetro `PSCredential` for de tipo, o dropdown fornece uma lista de objetos **credenciais** na conta de Automação atual. A + Adicionar uma opção **de credencial** também está disponível. Depois que todos os parâmetros necessários tiverem sido fornecidos, clique em **Salvar e compilar**.
   ![Captura de tela da etapa de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Depois que a nova configuração é salva, ela é enviada para compilação. O status do trabalho de compilação pode ser visualizado como qualquer configuração importada. Para mais informações, consulte [Visualizando um trabalho de compilação](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando a compilação for concluída com sucesso, a nova configuração será exibida na guia **Configurações Compiladas.** Em seguida, você pode atribuir a configuração a um nó gerenciado, usando as etapas em [Reatribuir um nó a uma configuração de nó diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para aprender como a bordo de nódulos, consulte [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter referência de cmdlet PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso da configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
