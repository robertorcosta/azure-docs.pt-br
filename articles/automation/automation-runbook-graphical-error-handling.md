---
title: Tratamento de erros em runbooks gráficos na Automação do Azure
description: Este artigo descreve como implementar a lógica de tratamento de erros em runbooks gráficos na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367057"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Tratamento de erros em runbooks gráficos na Automação do Azure

Um princípio de design fundamental a ser considerado para o seu runbook gráfico azure Automation é a identificação de problemas que o runbook pode experimentar durante a execução. Esses problemas podem incluir sucesso, estados de erro esperado e as condições de erro inesperado.

Muitas vezes, se houver um erro não-terminante que ocorra com uma atividade de runbook, o Windows PowerShell lida com a atividade processando qualquer atividade que se segue, independentemente do erro. O erro é provavelmente gerar uma exceção, mas a próxima atividade ainda pode ser executado.

Seu runbook gráfico deve incluir código de manipulação de erros para lidar com problemas de execução. Para validar a saída de uma atividade ou lidar com um erro, você pode usar uma atividade de código PowerShell, definir lógica condicional no link de saída da atividade ou aplicar outro método.

Runbooks gráficos de automação do Azure foram aprimorados com a capacidade de incluir o tratamento de erro. Agora você pode ativar exceções em erros de não finalização e criar links de erro entre atividades. O processo melhorado permite que seu runbook pegue erros e gerencie condições realizadas ou inesperadas. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Tipos de erro do PowerShell

Os tipos de erros do PowerShell que podem ocorrer durante a execução do runbook são erros de terminação e erros não terminantes.
 
### <a name="terminating-error"></a>Erro de terminação

Um erro de terminação é um erro grave durante a execução que interrompe completamente uma execução de comando ou script. Exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem a execução de um cmdlet e outros erros fatais.

### <a name="non-terminating-error"></a>Erro não-terminativo

Um erro não-terminativo é um erro não grave que permite que a execução continue apesar da condição de erro. Exemplos incluem erros operacionais, como erros e permissões não encontrados.

## <a name="when-to-use-error-handling"></a>Quando usar o tratamento de erros

Use o manuseio de erros no seu manual quando uma atividade crítica lança um erro ou exceção. É importante evitar que a próxima atividade no manual seja processada e lidar com o erro adequadamente. Lidar com o erro é especialmente crítico quando seus runbooks estão suportando um processo de operações de negócios ou serviços.

Para cada atividade que possa produzir um erro, você pode adicionar um link de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo atividade de código, invocação de um cmdlet, invocação de outro runbook, e assim por diante. A atividade de destino também pode ter links de saída, regulares ou links de erro. Os links permitem que o runbook implemente uma lógica complexa de manipulação de erros sem recorrer a uma atividade de código.

A prática recomendada é criar um manual de manipulação de erros dedicado com funcionalidade comum, mas essa prática não é obrigatória. Por exemplo, considere um runbook que tente iniciar uma máquina virtual e instalar um aplicativo nela. Se a VM não começar corretamente, ela:

1. Envia uma notificação sobre esse problema.
2. Inicia outro runbook que repõe automaticamente uma nova VM.

Uma solução é ter um link de erro no manual apontando para uma atividade que lida com o passo um. Por exemplo, o runbook `Write-Warning` pode conectar o cmdlet a uma atividade para a segunda etapa, como o [cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

Você também pode generalizar esse comportamento para uso em muitos runbooks, colocando essas duas atividades em um manual de manipulação de erros separado. Antes que o manual original chame esse manual de manipulação de erros, ele pode construir uma mensagem personalizada a partir de seus dados e, em seguida, passá-la como um parâmetro para o manual de manipulação de erros.

## <a name="how-to-use-error-handling"></a>Como usar o tratamento de erros

Cada atividade em seu runbook tem uma configuração que transforma exceções em erros não terminantes. Por padrão, essa configuração é desabilitada. Recomendamos ativar essa configuração em qualquer atividade em que seu manual lida com erros. Essa configuração garante que o manual lida com erros de terminação e não terminação na atividade como erros não terminadores, usando um link de erro.  

Depois de ativar a configuração, mande seu runbook criar uma atividade que lida com o erro. Se a atividade produzir algum erro, os links de erro de saída serão seguidos. Os links regulares não são seguidos, mesmo que a atividade produza saída regular também.<br><br> ![Exemplo de link de erro de runbook de automação](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo a seguir, um runbook recupera uma variável que contém o nome do computador de uma VM. Em seguida, ele tenta iniciar o VM com a próxima atividade.<br><br> ![Exemplo de manipulação de erros do runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A `Get-AutomationVariable` atividade e o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) são configurados para converter exceções em erros. Se houver problemas para obter a variável ou iniciar a VM, o código gera erros.<br><br> ![Configurações de atividade de manipulação de erros do runbook de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

Os links de erro fluem dessas atividades para uma única `error management` atividade de código. Essa atividade é configurada com uma simples `throw` expressão PowerShell que usa `$Error.Exception.Message` a palavra-chave para parar o processamento, juntamente com a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de manipulação de erros do runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links e tipos de link em runbooks gráficos, confira [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre execução de runbook, monitoramento de trabalhos em runbook e outros detalhes técnicos, consulte [execução do Runbook no Azure Automation](automation-runbook-execution.md).