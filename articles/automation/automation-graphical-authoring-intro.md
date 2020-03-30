---
title: Criação gráfica na Automação do Azure
description: A criação gráfica permite criar runbooks para a Automação do Azure sem trabalhar com código. Este artigo fornece uma introdução à criação gráfica e todos os detalhes necessários para iniciar a criação de um runbook gráfico.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 09afca7eaf385795baf9c4a3c94232622527e357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500973"
---
# <a name="graphical-authoring-in-azure-automation"></a>Criação gráfica na Automação do Azure

A autoria gráfica permite criar runbooks para a Automação Azure sem as complexidades do código de fluxo de trabalho do Windows PowerShell ou PowerShell. Você pode adicionar atividades à tela a partir de uma biblioteca de cmdlets e runbooks, vinculá-los e configurá-los para formar um fluxo de trabalho. Se você já trabalhou com o System Center Orchestrator ou service Management Automation (SMA), a autoria gráfica deve parecer familiar. Este artigo fornece uma introdução aos conceitos que você precisa para começar a criar um runbook gráfico.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbooks gráficos

Todos os runbooks no Azure Automation são fluxos de trabalho do Windows PowerShell. Runbooks gráficos e runbooks gráficos powershell workflow geram código PowerShell que os trabalhadores da Automação executam, mas que você não pode visualizar ou modificar. Você pode converter um runbook gráfico em um runbook gráfico powershell workflow e vice-versa. No entanto, você não pode converter esses runbooks em um runbook textual. Além disso, o editor gráfico automação não pode importar um runbook textual.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Você pode abrir o editor gráfico no Portal do Azure criando ou editando um runbook gráfico.

![Workspace gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seções a seguir descrevem os controles no editor gráfico.

### <a name="canvas-control"></a>Controle de tela

O controle do Canvas permite que você projete seu manual. Você pode adicionar atividades dos nós no controle da Biblioteca ao livro de execução e conectá-los com links para definir a lógica do runbook. Na parte inferior da tela, há controles que permitem que você amplie e desça.

### <a name="library-control"></a>Controle de Biblioteca

O controle da Biblioteca permite selecionar [atividades](#activities) para adicionar ao seu livro de execução. Você os adiciona à tela, onde você pode conectá-los a outras atividades. O controle da Biblioteca inclui as seções definidas na tabela a seguir.

| Seção | Descrição |
|:--- |:--- |
| Cmdlets |Todos os cmdlets que podem ser usados no seu runbook. Os cmdlets são organizados por módulo. Todos os módulos instalados em sua conta de Automação estão disponíveis. |
| Runbooks |Os runbooks em sua conta de Automação. Você pode adicionar esses runbooks à tela para serem usados como livros infantis. Apenas os runbooks do mesmo tipo de núcleo que o runbook que está sendo editado são mostrados. Para runbooks gráficos, apenas os runbooks baseados em PowerShell são mostrados. Para os runbooks gráficos do PowerShell Workflow, apenas os runbooks baseados em fluxo de trabalho PowerShell são mostrados. |
| Ativos |Os [ativos de automação](/previous-versions/azure/dn939988(v=azure.100)) em sua conta de Automação que você pode usar em seu manual. Adicionar um ativo a um runbook adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos de variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou defini-la. |
| Controle de Runbook |Controle atividades que podem ser usadas no seu runbook atual. Uma atividade de junção requer várias entradas e espera até que todas tenham sido concluídas antes de continuar o fluxo de trabalho. Uma atividade de código executa uma ou mais linhas de código PowerShell ou PowerShell Workflow, dependendo do tipo de runbook gráfico. Você pode usar essa atividade para código personalizado ou a funcionalidade que é difícil de conseguir obter com outras atividades. |

### <a name="configuration-control"></a>Controle de Configuração

O controle configuração permite fornecer detalhes para um objeto selecionado na tela. As propriedades disponíveis neste controle dependem do tipo de objeto selecionado. Quando você escolhe uma opção no controle de configuração, ele abre lâminas adicionais para fornecer mais informações.

### <a name="test-control"></a>Controle de Teste

O controle de Teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você testa um runbook gráficointerativamente.

## <a name="activities"></a>Atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet PowerShell, um livro de execução infantil ou um fluxo de trabalho. Você pode adicionar uma atividade ao runbook clicando com o botão direito do mouse no controle da Biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar que você desejar na tela. A localização da atividade na tela não afeta o funcionamento do runbook. Você pode colocar seu manual de corrida da maneira que achar mais adequado para visualizar seu funcionamento.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecione uma atividade na tela para configurar suas propriedades e parâmetros na lâmina de configuração. Você pode alterar o rótulo da atividade para um nome que você achar descritivo. O runbook ainda executa o cmdlet original. Você está simplesmente alterando o nome de exibição que o editor gráfico usa. Observe que o rótulo deve ser único dentro do manual.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para determinado cmdlet. Todos os cmdlets têm pelo menos um parâmetro definido, e alguns têm vários conjuntos. Se um cmdlet tiver vários conjuntos de parâmetros, você deve selecionar o que deve ser usado antes de configurar parâmetros. Você pode alterar o conjunto de parâmetros usados por uma atividade selecionando **Conjunto de parâmetros** e escolhendo outro conjunto. Neste caso, todos os valores de parâmetro que você já configurou são perdidos.

No exemplo a seguir, o [cmdlet Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) tem três conjuntos de parâmetros. O exemplo usa um conjunto chamado **ListVirtualMachineInResourceGroupParamSet**, com um único parâmetro opcional, para retornar todas as máquinas virtuais em um grupo de recursos. O exemplo também usa o parâmetro **GetVirtualMachineInResourceGroupParamSet** para especificar o retorno da máquina virtual. Este conjunto tem dois parâmetros obrigatórios e um parâmetro opcional.

![Conjunto de Parâmetros](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetro

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados disponíveis para um determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| fonte de dados | Descrição |
|:--- |:--- |
| Valor Constante |Digite um valor válido para o parâmetro. Esta fonte de dados está disponível apenas para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime, Switch. |
| Saída de Atividade |Use a saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Para o valor do parâmetro, use apenas a atividade que produz a saída. Se a atividade desemete um objeto com várias propriedades, você poderá digitar o nome de uma propriedade específica após selecionar a atividade. |
| Entrada do Runbook |Selecione uma entrada de runbook como entrada para o parâmetro de atividade. |
| Ativo da Variável |Selecione uma variável de Automação como entrada. |
| Ativo da Credencial |Selecione uma credencial de automação como entrada. |
| Ativo do Certificado |Selecione um certificado de Automação como entrada. |
| Ativo da Conexão |Selecione uma conexão de automação como entrada. |
| Expressão do PowerShell |Especifique uma simples [expressão PowerShell](#powershell-expressions). A expressão é avaliada antes da atividade e o resultado é utilizado para o valor do parâmetro. Você pode usar variáveis para referir-se à saída de uma atividade ou um parâmetro de entrada de runbook. |
| Não configurado |Limpe qualquer valor que tenha sido configurado anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Estes são parâmetros comuns ao PowerShell ou outros parâmetros personalizados. O editor gráfico apresenta uma caixa de texto onde você pode fornecer parâmetros usando a sintaxe PowerShell. Por exemplo, para `Verbose` usar o parâmetro `-Verbose:$True`comum, você deve especificar .

### <a name="retry-activity"></a>Atividade de repetição

A funcionalidade de repetição de uma atividade permite que ela seja executada várias vezes até que uma determinada condição seja atendida, muito parecido com um loop. Você pode usar esse recurso para atividades que devem ser executadas várias vezes, são propensas a erros, podem precisar de mais de uma tentativa de sucesso ou testar as informações de saída da atividade para dados válidos.

Quando você habilita a repetição de uma atividade, é possível definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook aguarda antes de executar novamente a atividade. Se você não especificar um atraso, a atividade será executada novamente imediatamente após sua conclusão.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão PowerShell que é avaliada após cada vez que a atividade é executada. Se a expressão se resolver para True, a atividade será executada novamente. Se a expressão for resolvida como False, a atividade não será executada novamente e o runbook passa para a próxima atividade.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de repetição `RetryData` pode usar uma variável nomeada que fornece acesso a informações sobre as repetições de atividade. Essa variável tem as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| `NumberOfAttempts` |Número de vezes que a atividade foi executada. |
| `Output` |Saída da última execução da atividade. |
| `TotalDuration` |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| `StartedAt` |Tempo (em formato UTC) quando a atividade foi iniciada. |

A seguir, exemplos de condições de repetição da atividade.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui duas indicações visuais para lembrá-lo. Um é apresentado na atividade e o outro é mostrado quando você revisa a configuração da atividade.

![Indicadores Visuais de Repetição da Atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de Script de Fluxo de Trabalho

Um controle de script de fluxo de trabalho é uma atividade especial que aceita o script PowerShell ou PowerShell Workflow, dependendo do tipo de runbook gráfico que está sendo escrito. Este controle fornece funcionalidades que podem não estar disponíveis por outros meios. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Qualquer saída da atividade é adicionada ao databus. Uma exceção é a saída sem link de saída, nesse caso a saída é adicionada à saída do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de livro de execução nomeada `NumberOfDays`. Em seguida, envia um valor de DateTime calculado como saída a ser usada pelas atividades subseqüentes no manual.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Links e fluxo de trabalho

Um link em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta, com a atividade de destino se iniciando após a atividade de origem ser concluída.

### <a name="link-creation"></a>Criação de link

Você pode criar um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e solte-a.

![Criar um link](media/automation-graphical-authoring-intro/create-link-options.png)

Selecione o link para configurar suas propriedades na folha Configuração. As propriedades incluem o tipo de link, descrito na tabela a seguir.

| Tipo de link | Descrição |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada saída de objeto da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez quando recebe saída da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="start-of-activity"></a>Início das atividades

Um runbook gráfico é iniciado com todas as atividades que não têm um link de entrada. Muitas vezes há apenas uma atividade que atua como atividade inicial para o runbook. Se várias atividades não tiverem um link de entrada, o runbook será executado em paralelo. Ele segue então os links para executar outras atividades, à medida que cada uma for concluída.

### <a name="link-conditions"></a>Condições de link

Quando você especifica uma condição em um link, a atividade de destino é executada somente se a condição se resolver para True. Você normalmente `ActivityOutput` usa uma variável em uma condição para recuperar a saída da atividade de origem.

Para um link de pipeline, você deve especificar uma condição para um único objeto. O manual avalia a condição de cada saída de objeto pela atividade de origem. Em seguida, executa a atividade de destino para cada objeto que satisfaz a condição. Por exemplo, com uma `Get-AzVM`atividade de origem de , você pode usar a seguinte sintaxe para um link de pipeline condicional para recuperar apenas máquinas virtuais no grupo de recursos chamado Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para um link de seqüência, o livro de execução só avalia a condição uma vez, uma vez que uma única matriz contendo todos os objetos da atividade de origem é devolvida. Por causa disso, o runbook não pode usar um link de seqüência para filtragem, como pode com um link de pipeline. O link de seqüência pode simplesmente determinar se a próxima atividade será executada ou não.

Por exemplo, pegue o seguinte conjunto de atividades em nosso **runbook Start VM:**

![Link Condicional com Sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

O runbook usa três links de seqüência diferentes que verificam os valores dos parâmetros `VMName` de entrada e `ResourceGroupName` para determinar as medidas apropriadas a serem tomadas. As ações possíveis são iniciar uma única VM, iniciar todas as VMs no grupo de recursos ou iniciar todas as VMs em uma assinatura. Para o elo de seqüência entre `Connect to Azure` e `Get single VM`, aqui está a lógica da condição:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtradas pela condição. Se uma atividade for a fonte de vários links, os dados disponíveis para as atividades em cada ramo dependem da condição no link de conexão a esse ramo.

Por exemplo, `Start-AzVM` a atividade no manual abaixo inicia todas as máquinas virtuais. Ela tem dois links condicionais. O primeiro link condicional `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` usa a `Start-AzVM` expressão para filtrar se a atividade for concluída com sucesso. O segundo link condicional `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` usa a `Start-AzVm` expressão para filtrar se a atividade não iniciar a máquina virtual.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que siga o primeiro link `Get-AzureVM` e use a saída de atividade de `Get-AzureVM` apenas recupera as máquinas virtuais que foram iniciadas no momento em que foi executada. Qualquer atividade que siga o segundo link só recebe as `Get-AzureVM` máquinas virtuais que foram paradas no momento em que foi executada. Qualquer atividade que seguir o terceiro link obterá todas as máquinas virtuais, independentemente do estado de execução.

### <a name="junctions"></a>Junções

Uma junção é uma atividade especial que espera até que todas as ramificações de entrada sejam concluídas. Isso permite que o runbook execute várias atividades em paralelo e garanta que todas tenham sido concluídas antes de seguir em frente.

Enquanto uma junção pode ter um número ilimitado de links de entrada, apenas um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você pode criar a junção com vários links de pipeline de entrada e salvar o manual, mas ele falhará quando for executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais enquanto baixa simultaneamente patches a serem aplicados a esses computadores. Ele usa uma junção para garantir que ambos os processos sejam concluídos antes que o manual continue.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

Um ciclo é formado quando uma atividade de destino se conecta à sua atividade de origem ou a outra atividade que eventualmente se conecta à sua fonte. A autoria gráfica não suporta atualmente ciclos. Se seu runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Compartilhamento de dados entre atividades

Qualquer dado que uma atividade produz com um link de saída é gravado no databus para o runbook. Qualquer atividade no runbook pode usar dados no barramento de dados para popular os valores de parâmetros ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A maneira como os dados são gravados no barramento de dados depende do tipo de link da atividade. Para um link de pipeline, os dados são produzidos como vários objetos. Para um link de sequência , os dados são gerados como uma matriz. Se houver apenas um valor, ele é a saída como uma matriz de elemento único.

Seu runbook tem duas maneiras de acessar dados no databus: 
* Use uma fonte de dados de saída de atividade.
* Use uma fonte de dados de expressão PowerShell.

O primeiro mecanismo usa uma fonte de dados de saída de atividade para preencher um parâmetro de outra atividade. Se a saída for um objeto, o runbook pode especificar uma única propriedade.

![produção de atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

O segundo mecanismo de acesso a dados recupera a saída de uma atividade em `ActivityOutput` uma fonte de dados de expressão PowerShell ou uma atividade de script de fluxo de trabalho com uma variável, usando a sintaxe mostrada abaixo. Se a saída for um objeto, seu runbook pode especificar uma única propriedade.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Pontos de verificação

Você pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) em um runbook gráfico do PowerShell Workflow selecionando **o runbook checkpoint** em qualquer atividade. Isso causa a definição de um ponto de verificação após a execução da atividade.

![Ponto de verificação](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação são habilitados apenas em runbooks gráficos do PowerShell Workflow e não estão disponíveis em runbooks gráficos. Se o runbook usar cmdlets do Azure, ele `Connect-AzAccount` deve seguir qualquer atividade de checkpoint com uma atividade. A operação de conexão é usada no caso do manual estar suspenso e deve ser reiniciado a partir deste ponto de verificação em um trabalhador diferente.

## <a name="runbook-input-and-output"></a>Entrada e saída de runbook

### <a name="runbook-input"></a>Entrada de runbook<a name="runbook-input"></a>

Um runbook requer a entrada de um usuário que inicia o runbook através do portal Azure ou de outro runbook, se o atual for usado como criança. Por exemplo, para um runbook que cria uma máquina virtual, o usuário pode precisar fornecer informações como o nome da máquina virtual e outras propriedades cada vez que o runbook é iniciado.

O runbook aceita entrada definindo um ou mais parâmetros de entrada. O usuário fornece valores para esses parâmetros cada vez que o livro de execução é iniciado. Quando o usuário inicia o manual usando o portal Azure, o usuário é solicitado a fornecer valores para cada parâmetro de entrada suportado pelo manual.

Ao criar seu livro de execução, você pode acessar seus parâmetros de entrada clicando em **Entrada e saída** na barra de ferramentas do runbook. Isso abre o controle Entrada e saída, em que você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Nome | Obrigatórios. O nome do parâmetro. O nome deve ser único dentro do manual. Ele deve começar com uma letra e pode conter apenas letras, números e sublinhados. O nome não pode conter um espaço. |
| Descrição |Opcional. Descrição do propósito do parâmetro de entrada. |
| Type | Opcional. Tipo de dados esperado para o valor do parâmetro. O Portal do Azure fornece um controle apropriado para o tipo de dados para cada parâmetro quando a entrada for solicitada. Os tipos de parâmetro com suporte são String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se um tipo de dados não estiver selecionado, será usado Stringpor padrão.|
| Obrigatório | Opcional. Configuração que especifica se um valor deve ser fornecido para o parâmetro. Se você `yes`escolher, um valor deve ser fornecido quando o livro de execução for iniciado. Se você `no`escolher, um valor não é necessário quando o livro de execução é iniciado e um valor padrão pode ser usado. O runbook não pode ser inicialda se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Padrão | Opcional. O valor usado para um parâmetro se não for passado quando o livro de execução for iniciado. Para definir um valor `Custom`padrão, escolha . Selecione `None` se você não quiser fornecer qualquer valor padrão. |

### <a name="runbook-output"></a>Saída de runbook

A autoria gráfica salva dados criados por qualquer atividade que não tenha um link de saída para a [saída do runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é salva com o trabalho do runbook e está disponível para um runbook pai quando o runbook é usado como filho.

## <a name="powershell-expressions"></a>Expressões do PowerShell

Uma das vantagens da autoria gráfica é que ele permite construir um runbook com o mínimo conhecimento do PowerShell. Atualmente, porém, você precisa conhecer um pouco do PowerShell para preencher certos [valores de parâmetros](#activities) e para definir [condições de link](#links-and-workflow). Esta seção fornece uma introdução rápida às expressões PowerShell. Detalhes completos do PowerShell estão disponíveis em [scripts com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Fonte de dados de expressão do PowerShell

Você pode usar uma expressão PowerShell como fonte de dados para preencher o valor de um parâmetro de [atividade](#activities) com os resultados do código PowerShell. A expressão pode ser uma única linha de código que executa uma função simples ou várias linhas que executam alguma lógica complexa. Qualquer saída de um comando que não está atribuído a uma variável é enviado para o valor do parâmetro.

Por exemplo, o comando a seguir sai na data atual.

```powershell-interactive
Get-Date
```

O próximo trecho de código constrói uma string a partir da data atual e atribui-a a uma variável. O código envia o conteúdo da variável para a saída.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os comandos a seguir avaliam a data atual e retornam uma seqüência indicando se o dia atual é um fim de semana ou um dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Saída de Atividade

Para usar a saída de uma atividade anterior `ActivityOutput` em seu livro de execução, use a variável com a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, você pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual. Neste caso, seu runbook pode usar a seguinte expressão.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se a propriedade exigir o objeto da máquina virtual em vez de apenas um nome, o manual reajusta todo o objeto usando a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

O runbook pode usar a saída de uma atividade em uma expressão mais complexa, como a seguinte. Esta expressão concatena texto para o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Condições

Use [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de True ou False.

Por exemplo, a seguinte condição determina se a `Get-AzureVM` máquina virtual de uma atividade nomeada está atualmente parada.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A seguinte condição determina se a mesma máquina virtual está em qualquer estado diferente de parada.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Você pode juntar várias condições em seu runbook `-or`usando um [operador lógico,](https://technet.microsoft.com/library/hh847789.aspx)como `-and` ou . Por exemplo, a seguinte condição verifica se a máquina virtual no exemplo anterior está em um estado de Parada ou Parada.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelas de hash

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) são pares de valor de nome que são úteis para retornar um conjunto de valores. Você também pode ver um hashtable referido como um dicionário. Propriedades para certas atividades esperam um hashtable em vez de um valor simples.

Crie uma tabela hashusando a seguinte sintaxe. Ele pode conter qualquer número de entradas, mas cada uma é definida por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma hashtable para ser usada como fonte de dados para um parâmetro de atividade que espera um hashtable de valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir usa `Get Twitter Connection` a saída de uma atividade chamada para preencher um hashtable.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Autenticação para recursos do Azure

Os runbooks na Automação do Azure que gerenciam os recursos do Azure requerem a autenticação do Azure. A [conta Run As](automation-create-runas-account.md), também referida como um diretor de serviço, é o mecanismo padrão que um runbook de automação usa para acessar os recursos do Azure Resource Manager em sua assinatura. Você pode adicionar essa funcionalidade a um runbook gráfico adicionando o recurso de `AzureRunAsConnection` conexão, que usa o cmdlet PowerShell [Get-AutomationConnection,](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) à tela. Você também pode adicionar o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Este cenário é ilustrado no exemplo a seguir.

![Executa Como Atividades de Autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A `Get Run As Connection` atividade, `Get-AutomationConnection`ou , é configurada com `AzureRunAsConnection`uma fonte de dados de valor constante chamada .

![Executar Como Configuração da Conexão](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima `Connect-AzAccount`atividade, adiciona a conta execute as autenticadas para uso no manual.

![Conjunto de parâmetros connect-azAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Observe que esses pseudônimos não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só `Connect-AzAccount` pode usar a si mesmo.

Para os campos de **parâmetros APPLICATIONID,** **CERTIFICATETHUMBPRINT**e **TENANTID,** especifique o nome da propriedade para o caminho De campo, uma vez que a atividade produz um objeto com várias propriedades. Caso contrário, quando o runbook é executado, ele falha ao tentar autenticar. Isso é o que você precisa, no mínimo, para autenticar seu runbook com a conta Executar Como.

Alguns assinantes criam uma conta de Automação usando uma [conta de usuário Azure AD](automation-create-aduser-account.md) para gerenciar a implantação clássica do Azure ou para os recursos do Azure Resource Manager. Para manter a compatibilidade retrógrada para esses assinantes, o `Add-AzureAccount` mecanismo de autenticação a ser usado em seu runbook é o cmdlet com um [ativo de credencial](automation-credentials.md). O ativo representa um usuário do Active Directory com acesso à conta do Azure.

Você pode habilitar essa funcionalidade para o seu runbook gráfico adicionando um `Add-AzureAccount` ativo de credencial à tela, seguido por uma atividade que usa o ativo de credencial para sua entrada. Veja os exemplos a seguir.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

O runbook deve autenticar no seu início e após cada ponto de verificação. Assim, você `Add-AzureAccount` deve usar `Checkpoint-Workflow` uma atividade após qualquer atividade. Você não precisa usar uma atividade de credencial adicional.

![Saída de Atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Exportar e importar um runbook gráfico

Você pode exportar apenas a versão publicada de um runbook gráfico. Se o manual ainda não tiver sido publicado, o botão **Exportar** será desativado. Quando você **clica** no botão Exportar, o manual é baixado para o computador local. O nome do arquivo corresponde ao nome do livro de execução com uma extensão **.graphrunbook.**

Você pode importar um arquivo gráfico ou gráfico do powershell workbook selecionando a opção **Importar** ao adicionar um runbook. Quando você seleciona o arquivo para importar, você pode manter o mesmo nome ou fornecer um novo. O campo **Tipo de runbook** exibe o tipo de livro de execução depois de avaliar o arquivo selecionado. Se você tentar selecionar um tipo diferente que não esteja correto, o editor gráfico apresenta uma mensagem observando que há conflitos potenciais e pode haver erros de sintaxe durante a conversão.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Testando um runbook gráfico

Cada runbook gráfico no Azure Automation tem uma versão do Rascunho e uma versão publicada. Você pode executar apenas a versão Publicada, enquanto você só pode editar a versão Draft. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão do Rascunho estiver pronta para uso, você publicá-la, que substitui a versão atual publicada com a versão do Draft.

Você pode testar a versão draft de um runbook no portal Azure, deixando a versão publicada inalterada. Alternativamente, você pode testar um novo runbook antes de ser publicado para que você possa verificar se o runbook funciona corretamente antes de qualquer substituição de versão. O teste de um runbook executa a versão Rascunho e garante que todas as ações que ele executa sejam concluídas. Nenhum histórico de trabalho é criado, mas o painel Saída de teste exibe a saída.

Abra o controle teste para o seu runbook gráfico abrindo o runbook para edição e, em seguida, clicando **em Test pane**. O controle de teste solicita parâmetros de entrada e você pode iniciar o runbook clicando **em Iniciar**.

## <a name="publishing-a-graphical-runbook"></a>Publicando um runbook gráfico

Publique um runbook gráfico abrindo o runbook para edição e, em seguida, clicando **em Publicar**. Os status possíveis para o livro de execução são:

* Novo, o livro ainda não foi publicado. 
* Publicado, o livro foi publicado.
* Em edição - o runbook foi editado após sua publicação, e as versões Draft e Published são diferentes.

![Status de runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Você tem a opção de reverter para a versão publicada de um runbook. Esta operação descarta quaisquer alterações feitas desde que o livro de execução foi publicado pela última vez. Ele substitui a versão Draft do runbook pela versão publicada.

## <a name="next-steps"></a>Próximas etapas

* Para começar com os runbooks do PowerShell Workflow, consulte [Meu primeiro runbook powershell workflow](automation-first-runbook-textual.md).
* Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para saber mais sobre os tipos de runbook e suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para entender como autenticar usando a conta 'Executar como' automação, consulte [Configure Azure Run As Account](automation-sec-configure-azure-runas-account.md).
