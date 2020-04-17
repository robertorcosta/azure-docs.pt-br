---
title: Tipos de runbook da Automação do Azure
description: Descreve os diferentes tipos de runbooks que você pode usar no Azure Automation e considerações para determinar qual tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535512"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure

O serviço de automação de processos azure automation suporta vários tipos de runbooks, conforme definido na tabela a seguir. Para saber mais sobre o ambiente de automação de processos, consulte [execução do Runbook no Azure Automation](automation-runbook-execution.md).

| Type | Descrição |
|:--- |:--- |
| [Gráfico](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell Workflow e criado e editado completamente no editor gráfico do portal Azure. |
| [PowerShell](#powershell-runbooks) |Manual de texto baseado no scripting do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Manual de texto baseado no scripting do Fluxo de Trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Manual de texto baseado em scripting Python. |

Leve em conta as seguintes considerações ao determinar qual tipo usar para um determinado runbook.

* Você não pode converter runbooks de gráfico para tipo de texto, ou o contrário.
* Existem limitações ao usar runbooks de diferentes tipos como livros de execução infantil. Para obter mais informações, confira [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbooks gráficos

Você pode criar e editar runbooks gráficos e gráficos do PowerShell Workflow usando o editor gráfico no portal Azure. No entanto, você não pode criar ou editar este tipo de runbook com outra ferramenta. Principais características dos runbooks gráficos:

* Pode ser exportado para arquivos em sua conta de Automação e, em seguida, importado para outra conta de Automação. 
* Gerar código PowerShell. 
* Pode ser convertido para ou a partir de runbooks gráficos powershell workflow durante a importação. 

### <a name="advantages"></a>Vantagens

* Use o modelo de autoragem visual insert-link-configure.
* Concentre-se em como os dados fluem através do processo.
* Represente visualmente os processos de gerenciamento.
* Inclua outros runbooks como livros de execução infantil para criar fluxos de trabalho de alto nível.
* Incentivar a programação modular.

### <a name="limitations"></a>Limitações

* Não é possível criar ou editar fora do portal Azure.
* Pode exigir uma atividade de código contendo código PowerShell para executar lógica complexa.
* Não é possível converter-se em um dos formatos de [texto,](automation-runbook-types.md)nem você pode converter um manual de texto em formato gráfico. 
* Não é possível visualizar ou editar diretamente o código PowerShell que o fluxo de trabalho gráfico cria. Você pode visualizar o código criado em qualquer atividade de código.
* Não é possível executar runbooks em um Linux Hybrid Runbook Worker. Consulte [Automatizar recursos em seu data center ou nuvem usando hybrid runbook worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell. Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell.
* Inicie mais rápido do que os runbooks powershell workflow, já que eles não precisam ser compilados antes de serem executados.
* Execute no Azure e no Hybrid Runbook Workers para Windows e Linux.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o scriptdo PowerShell.
* Os runbooks não podem usar [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações em paralelo.
* Os runbooks não podem usar [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook se houver um erro.
* Você pode incluir apenas os runbooks powershell workflow e runbooks gráficos como livros de execução infantil usando o [cmdlet Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

A seguir, os problemas conhecidos atuais com os runbooks do PowerShell:

* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não podem recuperar um ativo variável com `*~*` no nome.
* Uma operação [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) em um loop em um runbook PowerShell pode falhar após cerca de 80 iterações.
* Um runbook powershell pode falhar se ele tentar gravar uma grande quantidade de dados no fluxo de saída de uma só vez. Você normalmente pode contornar esse problema tendo a saída do runbook apenas as informações necessárias para trabalhar com objetos grandes. Por exemplo, em `Get-Process` vez de usar sem limitações, você pode ter `Get-Process | Select ProcessName, CPU`a saída de cmdlet apenas os parâmetros necessários como em .

## <a name="powershell-workflow-runbooks"></a>Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md). Você edita o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
* Use [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar a operação se houver um erro.
* Use [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para fazer várias ações em paralelo.
* Pode incluir outros runbooks gráficos e runbooks PowerShell Workflow como livros de execução infantil para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o PowerShell Workflow.
* Os runbooks devem lidar com a complexidade adicional do PowerShell Workflow, como [objetos desserializados.](automation-powershell-workflow.md#code-changes)
* Os runbooks demoram mais tempo para serem reiniciados do que os runbooks do PowerShell, já que eles devem ser compilados antes de serem executados.
* Você só pode incluir os runbooks do `Start-AzAutomationRunbook` PowerShell como livros de execução infantil usando o cmdlet.
* Os runbooks não podem ser executados em um Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Runbooks Python

Compilar runbooks Python em no Python 2. Você pode editar diretamente o código do runbook usando o editor de texto no portal Azure. Você também pode usar um editor de texto offline e [importar o runbook](manage-runbooks.md) para o Azure Automation.

### <a name="advantages"></a>Vantagens

* Use as bibliotecas Python robustas.
* Pode ser executado no Azure ou no Linux Hybrid Runbook Workers. Os trabalhadores do runbook híbrido do Windows são suportados com [python2.7](https://www.python.org/downloads/release/latest/python2) instalado.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o script do Python.
* Apenas o Python 2 é suportado atualmente. Qualquer função específica do Python 3 falha.
* Para usar bibliotecas de terceiros, você deve [importar os pacotes](python-packages.md) para a conta automação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a autoria de runbook gráfico, consulte [Azure Automation](automation-graphical-authoring-intro.md).
* Para entender as diferenças entre os fluxos de trabalho PowerShell e PowerShell para runbooks, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md).
* Para obter mais informações sobre como criar ou importar um runbook, consulte [Gerenciar runbooks no Azure Automation](manage-runbooks.md).
* Para saber mais sobre o PowerShell, incluindo módulos de referência e aprendizagem de idiomas, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
