---
title: Tipos de runbook da Automação do Azure
description: Descreve os diferentes tipos de runbook que você pode usar na Automação do Azure e as considerações que devem ser levadas em conta ao determinar qual tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010164"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure

O serviço de automação de processos do Azure Automation suporta vários tipos de runbooks. Os tipos são brevemente definidos na tabela a seguir e descritos com mais detalhes nas seções abaixo. Para saber mais sobre o ambiente de automação de processos, consulte [execução do Runbook no Azure Automation](automation-runbook-execution.md).

| Type | Descrição |
|:--- |:--- |
| [Gráfico](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell Workflow e criado e editado completamente no editor gráfico do portal Azure. |
| [PowerShell](#powershell-runbooks) |Manual de texto baseado no scripting do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Manual de texto baseado no scripting do Fluxo de Trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Manual de texto baseado em scripting Python. |

## <a name="graphical-runbooks"></a>Runbooks gráficos

Você pode criar e editar runbooks gráficos e gráficos do PowerShell Workflow usando o editor gráfico no portal Azure. No entanto, você não pode criar ou editar este tipo de runbook com outra ferramenta.

Um runbook gráfico tem as seguintes características principais:

* Pode ser exportado para um arquivo em sua conta de Automação e, em seguida, importado para outra conta de Automação. 
* Gera código PowerShell. 
* Pode ser convertido para ou a partir de um manual gráfico powershell workflow durante a importação. 

### <a name="advantages"></a>Vantagens

* Usa o modelo de autoragem visual insert-link-configure.
* Foca-se em como os dados fluem através do processo.
* Representa visualmente os processos de gestão.
* Inclui outros runbooks como livros de execução infantil para criar fluxos de trabalho de alto nível.
* Incentiva a programação modular.

### <a name="limitations"></a>Limitações

* Não pode ser criado ou editado fora do portal Azure.
* Pode exigir uma atividade de código contendo código PowerShell para executar lógica complexa.
* Não pode ser convertido em um dos formatos de [texto,](automation-runbook-types.md)nem um manual de texto pode ser convertido em formato gráfico. 
* Não permite que você visualize ou edite diretamente o código PowerShell que o fluxo de trabalho gráfico cria. Você pode visualizar o código criado em qualquer atividade de código.
* Não é executado em um Linux Hybrid Runbook Worker. Consulte [Automatizar recursos em seu data center ou nuvem usando hybrid runbook worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell. Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell.
* O runbook inicia mais rapidamente do que os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não precisa ser compilado antes da execução.
* Pode ser executado no Azure ou em Trabalhadores de Runbook Híbridos linux e Windows

### <a name="limitations"></a>Limitações

* Exige familiarização com a criação de script do PowerShell.
* Não é possível usar o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
* Não é possível usar os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
* Os runbooks de Fluxo de Trabalho e Gráficos do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

Veja a seguir problemas conhecidos atuais com os runbooks do PowerShell.

* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) com *~* no nome.
* Get-Process em um loop em um runbook do PowerShell pode falhar após cerca de 80 iterações.
* Um runbook do PowerShell poderá falhar se tentar gravar uma grande quantidade de dados no fluxo de saída de uma vez.   Geralmente, você pode contornar esse problema emitindo apenas as informações que precisa ao trabalhar com objetos grandes.  Por exemplo, em vez de produzir algo como *Get-Process*, você pode exibir apenas os campos obrigatórios com *Get-Process | Selecionar ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md).  Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
* Use os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
* Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
* Pode incluir runbooks Gráficos e runbooks de Fluxo de Trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* O autor deve estar familiarizado com o Fluxo de Trabalho do PowerShell.
* O runbook deve lidar com a complexidade adicional do Fluxo de Trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#code-changes).
* O runbook demora mais para iniciar do que os runbooks do PowerShell pois precisa ser compilado antes da execução.
* Os runbooks do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.
* Não é possível executar em um Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Runbooks Python

Compilar runbooks Python em no Python 2. Você pode editar diretamente o código do runbook usando o editor de texto do portal do Azure ou com qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) para a Automação do Azure.

### <a name="advantages"></a>Vantagens

* Utilize as robustas bibliotecas do Python.
* Pode ser executado no Azure ou em ambos os Trabalhadores híbridos do Linux Runbook. Os trabalhadores do runbook híbrido do Windows são suportados com [python2.7](https://www.python.org/downloads/release/latest/python2) instalado.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com a criação de scripts do Python.
* Somente o Python 2 tem suporte no momento, o que significa que as funções específicas do Python 3 falharão.
* Para usar bibliotecas de terceiros, você deve [importar o pacote](python-packages.md) para a conta automação para que ele seja usado.

## <a name="considerations"></a>Considerações

Leve em conta as considerações adicionais a seguir ao determinar qual tipo usar para um runbook específico.

* Você não pode converter runbooks de tipo de gráfico para texto ou vice-versa.
* Há limitações ao usar runbooks de tipos diferentes, como um runbook filho. Para obter mais informações, confira [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell para runbooks, consulte [Aprendendo sobre o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Runbook, consulte [Criando ou Importando um Runbook](manage-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
