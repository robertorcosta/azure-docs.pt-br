---
title: Tratamento de erros em runbooks gráficos na Automação do Azure
description: Este artigo descreve como implementar a lógica de tratamento de erros em runbooks gráficos na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: dec715ec6741f4429d8b1d4f620ef3cb82d4c1d3
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649969"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Tratamento de erros em runbooks gráficos na Automação do Azure

Um princípio de design importante a ser considerado para o runbook gráfico de automação do Azure é a identificação dos problemas que o runbook pode experimentar durante a execução. Esses problemas podem incluir sucesso, estados de erro esperado e as condições de erro inesperado.

Geralmente, se houver um erro de não finalização que ocorre com uma atividade de runbook, o Windows PowerShell lida com a atividade processando qualquer atividade que se segue, independentemente do erro. O erro é provavelmente gerar uma exceção, mas a próxima atividade ainda pode ser executado.

Seu runbook gráfico deve incluir código de tratamento de erros para lidar com problemas de execução. Para validar a saída de uma atividade ou manipular um erro, você pode usar uma atividade de código do PowerShell, definir a lógica condicional no link de saída da atividade ou aplicar outro método.

Runbooks gráficos de automação do Azure foram aprimorados com a capacidade de incluir o tratamento de erro. Agora você pode ativar exceções em erros de não finalização e criar links de erro entre atividades. O processo aprimorado permite que seu runbook Capture erros e gerencie condições realizadas ou inesperadas. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Tipos de erro do PowerShell

Os tipos de erros do PowerShell que podem ocorrer durante a execução do runbook são: erros de encerramento e erros de não finalização.
 
### <a name="terminating-error"></a>Erro de encerramento

Um erro de encerramento é um erro grave durante a execução que interrompe completamente um comando ou execução de script. Os exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem a execução de um cmdlet e outros erros fatais.

### <a name="non-terminating-error"></a>Erro de não encerramento

Um erro de não finalização é um erro não sério que permite que a execução continue apesar da condição de erro. Os exemplos incluem erros operacionais, como erros de arquivo e permissões de arquivos não encontrados.

## <a name="when-to-use-error-handling"></a>Quando usar o tratamento de erros

Use o tratamento de erros em seu runbook quando uma atividade crítica lançar um erro ou uma exceção. É importante impedir que a próxima atividade no runbook seja processada e manipule o erro adequadamente. Lidar com o erro é especialmente crítico quando seus runbooks dão suporte a um processo de operações de negócios ou serviços.

Para cada atividade que pode produzir um erro, você pode adicionar um link de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo atividade de código, invocação de um cmdlet, invocação de outro runbook e assim por diante. A atividade de destino também pode ter links de saída, ou seja, links regulares ou de erro. Os links permitem que o runbook implemente uma lógica de tratamento de erro complexa sem recorrer a uma atividade de código.

A prática recomendada é criar um runbook de tratamento de erros dedicado com funcionalidade comum, mas essa prática não é obrigatória. Por exemplo, considere um runbook que tenta iniciar uma máquina virtual e instalar um aplicativo nele. Se a VM não for iniciada corretamente, ela:

1. Envia uma notificação sobre esse problema.
2. Inicia outro runbook que provisiona automaticamente uma nova VM em vez disso.

Uma solução é ter um link de erro no runbook apontando para uma atividade que lida com a etapa um. Por exemplo, o runbook pode conectar o cmdlet **Write-Warning** a uma atividade para a etapa dois, como o cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) .

Você também pode generalizar esse comportamento para uso em muitos runbooks, colocando essas duas atividades em um runbook de tratamento de erros separado, seguindo as diretrizes sugeridas anteriormente. Antes que o runbook original Chame esse runbook de tratamento de erros, ele pode construir uma mensagem personalizada de seus dados e, em seguida, passá-la como um parâmetro para o runbook de tratamento de erros.

## <a name="how-to-use-error-handling"></a>Como usar o tratamento de erros

Cada atividade em seu runbook tem uma definição de configuração que transforma exceções em erros de não encerramento. Por padrão, essa configuração é desabilitada. É recomendável habilitar essa configuração em qualquer atividade em que o runbook lide com erros. A habilitação dessa configuração garante que o runbook lide com os erros de encerramento e de não finalização na atividade como erros de não finalização, usando um link de erro.  

Depois de habilitar a definição de configuração, faça com que o runbook crie uma atividade que manipule o erro. Se a atividade produzir qualquer erro, os links de erro de saída serão seguidos. Os links regulares não são seguidos, mesmo que a atividade produza saída regular também.<br><br> ![Exemplo de link de erro de runbook de automação](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo a seguir, um runbook recupera uma variável que contém o nome do computador de uma VM. Em seguida, ele tenta iniciar a VM com a próxima atividade.<br><br> ![Exemplo de tratamento de erro de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A atividade **Get-AutomationVariable** e o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) são configurados para converter exceções em erros. Se houver problemas ao obter a variável ou iniciar a VM, o código gerará erros.<br><br> ![configurações de atividade de tratamento de erro do runbook de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

Links de erro fluem dessas atividades para uma única atividade de código de **Gerenciamento de erro** . Essa atividade é configurada com uma expressão simples do PowerShell que usa a palavra-chave *throw* para interromper o processamento, juntamente com `$Error.Exception.Message` para obter a mensagem que descreve a exceção atual.<br><br> exemplo de código de tratamento de erro do runbook de automação ![](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links e tipos de link em runbooks gráficos, confira [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução de runbook, o monitoramento de trabalhos de runbook e outros detalhes técnicos, consulte [execução de runbook na automação do Azure](automation-runbook-execution.md).