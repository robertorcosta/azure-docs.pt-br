---
title: Tipos de runbook da Automação do Azure
description: Descreve os diferentes tipos de runbooks que você pode usar na automação do Azure e considerações para determinar qual tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535512"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure

O serviço de automação do processo de automação do Azure dá suporte a vários tipos de runbooks, conforme definido na tabela a seguir. Para saber mais sobre o ambiente de automação de processo, consulte [execução de runbook na automação do Azure](automation-runbook-execution.md).

| Type | Descrição |
|:--- |:--- |
| [Gráfico](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks)|Runbook gráfico baseado no fluxo de trabalho do Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto baseado em scripts do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Runbook de texto baseado no script de fluxo de trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texto baseado em scripts Python. |

Leve em consideração as seguintes considerações ao determinar qual tipo usar para um runbook específico.

* Você não pode converter runbooks de gráfico para tipo de texto ou o contrário.
* Há limitações ao usar runbooks de tipos diferentes como runbooks filho. Para obter mais informações, confira [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbooks gráficos

Você pode criar e editar runbooks de fluxo de trabalho gráfico e gráfico do PowerShell usando o editor gráfico no portal do Azure. No entanto, você não pode criar ou editar esse tipo de runbook com outra ferramenta. Principais recursos de runbooks gráficos:

* Pode ser exportado para arquivos em sua conta de automação e, em seguida, importado para outra conta de automação. 
* Gerar código do PowerShell. 
* Pode ser convertido de ou para runbooks gráficos do fluxo de trabalho do PowerShell durante a importação. 

### <a name="advantages"></a>Vantagens

* Use o Visual Insert-link-configure o modelo de criação.
* Concentre-se em como os dados fluem pelo processo.
* Represente visualmente os processos de gerenciamento.
* Inclua outros runbooks como runbooks filho para criar fluxos de trabalho de alto nível.
* Incentive a programação modular.

### <a name="limitations"></a>Limitações

* Não é possível criar ou editar fora do portal do Azure.
* Pode exigir uma atividade de código que contém o código do PowerShell para executar lógica complexa.
* Não é possível converter em um dos [formatos de texto](automation-runbook-types.md), nem converter um runbook de texto em formato gráfico. 
* Não é possível exibir ou editar diretamente o código do PowerShell criado pelo fluxo de trabalho gráfico. Você pode exibir o código criado em qualquer atividade de código.
* Não é possível executar runbooks em um Hybrid Runbook Worker Linux. Confira [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell. Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell.
* Comece mais rápido do que os runbooks de fluxo de trabalho do PowerShell, pois eles não precisam ser compilados antes da execução.
* Execute no Azure e em Hybrid runbook Workers para Windows e Linux.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o script do PowerShell.
* Os Runbooks não podem usar o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações em paralelo.
* Os Runbooks não podem usar [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook se houver um erro.
* Você pode incluir apenas runbooks de fluxo de trabalho do PowerShell e runbooks gráficos como runbooks filho usando o cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

A seguir estão os problemas conhecidos atuais com runbooks do PowerShell:

* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não podem recuperar um ativo variável com `*~*` no nome.
* Uma operação de [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) em um loop em um runbook do PowerShell pode falhar após cerca de 80 iterações.
* Um runbook do PowerShell pode falhar se ele tentar gravar uma grande quantidade de dados no fluxo de saída ao mesmo tempo. Normalmente, você pode contornar esse problema fazendo com que o runbook gere apenas as informações necessárias para trabalhar com objetos grandes. Por exemplo, em vez de `Get-Process` usar sem limitações, você pode fazer com que o cmdlet gere apenas os parâmetros necessários `Get-Process | Select ProcessName, CPU`como em.

## <a name="powershell-workflow-runbooks"></a>Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md). Você edita o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
* Use [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar a operação se houver um erro.
* Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar várias ações em paralelo.
* Pode incluir outros runbooks gráficos e runbooks de fluxo de trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o fluxo de trabalho do PowerShell.
* Os Runbooks devem lidar com a complexidade adicional do fluxo de trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#code-changes).
* Os runbooks demoram mais para iniciar do que os runbooks do PowerShell, pois eles devem ser compilados antes da execução.
* Você só pode incluir runbooks do PowerShell como runbooks filho usando o `Start-AzAutomationRunbook` cmdlet.
* Os Runbooks não podem ser executados em um Hybrid Runbook Worker Linux.

## <a name="python-runbooks"></a>Runbooks Python

Compilar runbooks Python em no Python 2. Você pode editar diretamente o código do runbook usando o editor de texto na portal do Azure. Você também pode usar um editor de texto offline e [importar o runbook para a](manage-runbooks.md) automação do Azure.

### <a name="advantages"></a>Vantagens

* Use as bibliotecas robustas do Python.
* Pode ser executado no Azure ou em Hybrid runbook Workers do Linux. Há suporte para Hybrid runbook Workers do Windows com o [Python 2.7](https://www.python.org/downloads/release/latest/python2) instalado.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com scripts Python.
* Somente o Python 2 tem suporte no momento. Todas as funções específicas do Python 3 falham.
* Para usar bibliotecas de terceiros, você deve [importar os pacotes](python-packages.md) para a conta de automação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de runbook gráfico, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
* Para entender as diferenças entre os fluxos de trabalho do PowerShell e do PowerShell para runbooks, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md).
* Para obter mais informações sobre como criar ou importar um runbook, consulte [gerenciar runbooks na automação do Azure](manage-runbooks.md).
* Para saber mais sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
