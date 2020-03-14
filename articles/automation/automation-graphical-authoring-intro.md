---
title: Criação gráfica na Automação do Azure
description: A criação gráfica permite criar runbooks para a Automação do Azure sem trabalhar com código. Este artigo fornece uma introdução à criação gráfica e todos os detalhes necessários para iniciar a criação de um runbook gráfico.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a50dbe4d1e100032282891ccd15a94330f7fead4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278877"
---
# <a name="graphical-authoring-in-azure-automation"></a>Criação gráfica na Automação do Azure

A criação gráfica permite que você crie runbooks para a automação do Azure sem as complexidades do código subjacente de fluxo de trabalho do Windows PowerShell ou do PowerShell. Você pode adicionar atividades à tela de uma biblioteca de cmdlets e runbooks, vinculá-los e configurá-los para formar um fluxo de trabalho. Se você já trabalhou com o System Center Orchestrator ou o Service Management Automation (SMA), a criação gráfica deve parecer familiar. Este artigo fornece uma introdução aos conceitos necessários para começar a criar um runbook gráfico.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbooks gráficos

Todos os runbooks na automação do Azure são fluxos de trabalho do Windows PowerShell. Runbooks gráficos e runbooks gráficos de fluxo de trabalho do PowerShell geram código do PowerShell que os trabalhos de automação executam, mas que você não pode exibir ou modificar. Você pode converter um runbook gráfico em um runbook de fluxo de trabalho do PowerShell gráfico e vice-versa. No entanto, não é possível converter esses runbooks em um runbook textual. Além disso, o editor gráfico de automação não pode importar um runbook textual.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Você pode abrir o editor gráfico no Portal do Azure criando ou editando um runbook gráfico.

![Workspace gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seções a seguir descrevem os controles no editor gráfico.

### <a name="canvas-control"></a>Controle de tela

O controle Canvas permite que você projete seu runbook. Você pode adicionar atividades dos nós no controle de biblioteca ao runbook e conectá-las com links para definir a lógica do runbook. Na parte inferior da tela, há controles que permitem ampliar e reduzir.

### <a name="library-control"></a>Controle de Biblioteca

O controle biblioteca permite que você selecione [atividades](#activities) a serem adicionadas ao seu runbook. Você os adiciona à tela, onde você pode conectá-las a outras atividades. O controle biblioteca inclui as seções definidas na tabela a seguir.

| Seção | DESCRIÇÃO |
|:--- |:--- |
| Cmdlets |Todos os cmdlets que podem ser usados em seu runbook. Os cmdlets são organizados por módulo. Todos os módulos que você instalou em sua conta de automação estão disponíveis. |
| Runbooks |Os runbooks em sua conta de automação. Você pode adicionar esses runbooks à tela para serem usados como runbooks filho. São mostrados apenas runbooks do mesmo tipo de núcleo do runbook que está sendo editado. Para runbooks gráficos, somente runbooks baseados no PowerShell são mostrados. Para runbooks gráficos de fluxo de trabalho do PowerShell, somente runbooks baseados em fluxo de trabalho do PowerShell são mostrados. |
| Ativos |Os [ativos de automação](/previous-versions/azure/dn939988(v=azure.100)) na sua conta de automação que você pode usar em seu runbook. A adição de um ativo a um runbook adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos de variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou defini-la. |
| Controle de Runbook |As atividades de controle que podem ser usadas em seu runbook atual. Uma atividade de junção usa várias entradas e aguarda até que todas tenham sido concluídas antes de continuar o fluxo de trabalho. Uma atividade de código executa uma ou mais linhas de código do PowerShell ou do fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico. Você pode usar essa atividade para código personalizado ou a funcionalidade que é difícil de conseguir obter com outras atividades. |

### <a name="configuration-control"></a>Controle de Configuração

O controle de configuração permite que você forneça detalhes para um objeto selecionado na tela. As propriedades disponíveis neste controle dependem do tipo de objeto selecionado. Quando você escolhe uma opção no controle de configuração, ela abre folhas adicionais para fornecer mais informações.

### <a name="test-control"></a>Controle de Teste

O controle de Teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você testa interativamente um runbook gráfico.

## <a name="activities"></a>Atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook filho ou um fluxo de trabalho. Você pode adicionar uma atividade ao runbook clicando com o botão direito do mouse no controle de biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar que você desejar na tela. O local da atividade na tela não afeta a operação do runbook. Você pode definir o layout de seu runbook de qualquer forma que achar mais adequada para visualizar sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selecione uma atividade na tela para configurar suas propriedades e parâmetros na folha configuração. Você pode alterar o rótulo da atividade para um nome que você acha que é descritivo. O runbook ainda executa o cmdlet original. Você está simplesmente alterando o nome de exibição usado pelo editor gráfico. Observe que o rótulo deve ser exclusivo dentro do runbook.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para determinado cmdlet. Todos os cmdlets têm pelo menos um conjunto de parâmetros e alguns têm vários conjuntos. Se um cmdlet tiver vários conjuntos de parâmetros, você deverá selecionar aquele a ser usado antes de configurar os parâmetros. Você pode alterar o conjunto de parâmetros usado por uma atividade selecionando **conjunto de parâmetros** e escolhendo outro conjunto. Nesse caso, todos os valores de parâmetro que você já configurou serão perdidos.

No exemplo a seguir, o cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) tem três conjuntos de parâmetros. O exemplo usa um conjunto chamado **ListVirtualMachineInResourceGroupParamSet**, com um único parâmetro opcional, para retornar todas as máquinas virtuais em um grupo de recursos. O exemplo também usa o conjunto de parâmetros **GetVirtualMachineInResourceGroupParamSet** para especificar a máquina virtual a ser retornada. Esse conjunto tem dois parâmetros obrigatórios e um parâmetro opcional.

![Conjunto de Parâmetros](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetro

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados que estão disponíveis para um determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, **NULL** não é uma opção disponível para um parâmetro que não permite valores nulos.

| fonte de dados | DESCRIÇÃO |
|:--- |:--- |
| Valor Constante |Digite um valor válido para o parâmetro. Esta fonte de dados só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime, switch. |
| Saída de Atividade |Use a saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Para o valor do parâmetro, use apenas a atividade que produz a saída. Se a atividade produzir um objeto com várias propriedades, você poderá digitar o nome de uma propriedade específica depois de selecionar a atividade. |
| Entrada do Runbook |Selecione uma entrada de runbook como uma entrada para o parâmetro de atividade. |
| Ativo da Variável |Selecione uma variável de automação como entrada. |
| Ativo da Credencial |Selecione uma credencial de automação como entrada. |
| Ativo do Certificado |Selecione um certificado de automação como entrada. |
| Ativo da Conexão |Selecione uma conexão de automação como entrada. |
| Expressão do PowerShell |Especifique uma expressão simples do [PowerShell](#powershell-expressions). A expressão é avaliada antes da atividade e o resultado é usado para o valor do parâmetro. Você pode usar variáveis para referir-se à saída de uma atividade ou um parâmetro de entrada de runbook. |
| Não configurado |Desmarque qualquer valor configurado anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Esses são parâmetros comuns do PowerShell ou outros parâmetros personalizados. O editor gráfico apresenta uma caixa de texto na qual você pode fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para usar o parâmetro comum *Verbose* , você deve especificar `-Verbose:$True`.

### <a name="retry-activity"></a>Atividade de repetição

A funcionalidade de repetição para uma atividade permite que ela seja executada várias vezes até que uma determinada condição seja atendida, assim como um loop. Você pode usar esse recurso para atividades que devem ser executadas várias vezes, são propensas a erros, podem precisar de mais de uma tentativa de sucesso ou testar as informações de saída da atividade para obter dados válidos.

Quando você habilita a repetição de uma atividade, é possível definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook aguarda antes de executar novamente a atividade. Se você não especificar um atraso, a atividade será executada novamente imediatamente após a conclusão.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão do PowerShell que é avaliada após cada vez que a atividade é executada. Se a expressão for resolvida como true, a atividade será executada novamente. Se a expressão for resolvida como false, a atividade não será executada novamente e o runbook passará para a próxima atividade.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de repetição pode usar uma variável chamada *RetryData* que fornece acesso a informações sobre as tentativas de atividade. Essa variável tem as propriedades na tabela a seguir:

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| NumberOfAttempts |Número de vezes que a atividade foi executada. |
| Saída |Saída da última execução da atividade. |
| TotalDuration |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| StartedAt |Tempo (em formato UTC) quando a atividade foi iniciada pela primeira vez. |

Veja a seguir exemplos de condições de repetição de atividade.

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

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui duas indicações visuais para lembrá-lo. Uma é apresentada na atividade e a outra é mostrada quando você revisa a configuração da atividade.

![Indicadores Visuais de Repetição da Atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de Script de Fluxo de Trabalho

Um controle de script de fluxo de trabalho é uma atividade especial que aceita o script do PowerShell ou do fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico que está sendo criado. Esse controle fornece funcionalidade que pode não estar disponível por outros meios. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Qualquer saída da atividade é adicionada às DataBus. Uma exceção é saída sem nenhum link de saída; nesse caso, a saída é adicionada à saída do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de runbook chamada *NumberOfDays*. Em seguida, ele envia um valor DateTime calculado como saída a ser usado pelas atividades subsequentes no runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Links e fluxo de trabalho

Um link em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta, com a atividade de destino se iniciando após a atividade de origem ser concluída.

### <a name="link-creation"></a>Criação de link

Você pode criar um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e solte-a.

![Criar um link](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecione o link para configurar suas propriedades na folha Configuração. As propriedades incluem o tipo de link, que é descrito na tabela a seguir.

| Tipo de link | DESCRIÇÃO |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada saída de objeto da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez quando recebe a saída da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="start-of-activity"></a>Início da atividade

Um runbook gráfico é iniciado com todas as atividades que não têm um link de entrada. Geralmente, há apenas uma atividade que atua como a atividade inicial do runbook. Se várias atividades não tiverem um link de entrada, o runbook começará executando-as em paralelo. Ele segue então os links para executar outras atividades, à medida que cada uma for concluída.

### <a name="link-conditions"></a>Condições de link

Quando você especificar uma condição em um link, a atividade de destino será executada somente se a condição for resolvida como true. Normalmente, você usa uma variável *ActivityOutput* em uma condição para recuperar a saída da atividade de origem.

Para um link de pipeline, você deve especificar uma condição para um único objeto. O runbook avalia a condição para cada saída de objeto pela atividade de origem. Em seguida, ele executa a atividade de destino para cada objeto que satisfaz a condição. Por exemplo, com uma atividade de origem de **Get-AzVM**, você pode usar a sintaxe a seguir para um link de pipeline condicional para recuperar somente máquinas virtuais no grupo de recursos chamado Grupo1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para um link de sequência, o runbook avalia apenas a condição uma vez, já que uma única matriz contendo todos os objetos da atividade de origem é retornada. Por isso, o runbook não pode usar um link de sequência para filtragem, como pode com um link de pipeline. O link de sequência pode simplesmente determinar se a próxima atividade é executada ou não.

Por exemplo, use o seguinte conjunto de atividades em nosso runbook de **VM inicial** :

![Link Condicional com Sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

O runbook usa três links de sequência diferentes que verificam os valores dos parâmetros de entrada *VMName* e *ResourceGroupName* para determinar a ação apropriada a ser tomada. As ações possíveis são iniciar uma única VM, iniciar todas as VMs no grupo de recursos ou iniciar todas as VMs em uma assinatura. Para o link de sequência entre **conectar-se ao Azure** e **obter uma única VM**, aqui está a lógica da condição:

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

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtradas pela condição. Se uma atividade for a origem para vários links, os dados disponíveis para atividades em cada ramificação dependerão da condição no link que se conecta a essa ramificação.

Por exemplo, a atividade **Start-AzVM** no runbook abaixo inicia todas as máquinas virtuais. Ela tem dois links condicionais. O primeiro link condicional usa a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` para filtrar se a atividade **Start-AzVM** for concluída com êxito. O segundo link condicional usa a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` para filtrar se a atividade **Start-AzVm** falhar ao iniciar a máquina virtual.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que segue o primeiro link e usa a saída da atividade de **Get-AzureVM** recupera somente as máquinas virtuais que foram iniciadas no momento em que **Get-AzureVM** foi executado. Qualquer atividade que segue o segundo link só obtém as máquinas virtuais que foram interrompidas no momento em que **Get-AzureVM** foi executado. Qualquer atividade que seguir o terceiro link obterá todas as máquinas virtuais, independentemente do estado de execução.

### <a name="junctions"></a>Junções

Uma junção é uma atividade especial que espera até que todas as ramificações de entrada sejam concluídas. Isso permite que o runbook execute várias atividades em paralelo e certifique-se de que todas tenham sido concluídas antes de prosseguir.

Embora uma junção possa ter um número ilimitado de links de entrada, somente um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você pode criar a junção com vários links de pipeline de entrada e salvar o runbook, mas ele falhará quando for executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais enquanto baixa simultaneamente patches a serem aplicados a esses computadores. Ele usa uma junção para garantir que ambos os processos sejam concluídos antes de o runbook continuar.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

Um ciclo é formado quando uma atividade de destino é vinculada de volta à sua atividade de origem ou a outra atividade que, eventualmente, se vincula de volta à sua origem. Atualmente, a criação gráfica não oferece suporte a ciclos. Se seu runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Compartilhamento de dados entre atividades

Todos os dados que uma atividade produz com um link de saída são gravados nas DataBus do runbook. Qualquer atividade no runbook pode usar dados no barramento de dados para popular os valores de parâmetros ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A maneira como os dados são gravados no barramento de dados depende do tipo de link da atividade. Para um link de pipeline, os dados são gerados como vários objetos. Para um link de sequência, os dados são gerados como uma matriz. Se houver apenas um valor, ele será apresentado como uma matriz de elemento único.

Seu runbook tem duas maneiras de acessar dados nas DataBus: 
* Use uma fonte de dados de saída de atividade.
* Use uma fonte de dados de expressão do PowerShell.

O primeiro mecanismo usa uma fonte de dados de saída de atividade para popular um parâmetro de outra atividade. Se a saída for um objeto, o runbook poderá especificar uma única propriedade.

![saída da atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

O segundo mecanismo de acesso a dados recupera a saída de uma atividade em uma fonte de dados de expressão do PowerShell ou uma atividade de script de fluxo de trabalho com uma variável *ActivityOutput* , usando a sintaxe mostrada abaixo. Se a saída for um objeto, o runbook poderá especificar uma única propriedade.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Pontos de verificação

Você pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) em um runbook de fluxo de trabalho gráfico do PowerShell selecionando **runbook de ponto de verificação** em qualquer atividade. Isso causa a definição de um ponto de verificação após a execução da atividade.

![Ponto de verificação](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação são habilitados apenas em runbooks gráficos de fluxo de trabalho do PowerShell e não estão disponíveis em runbooks gráficos. Se o runbook usar cmdlets do Azure, ele deverá seguir qualquer atividade de ponto de verificação com uma atividade **Connect-AzAccount** . A operação de conexão é usada no caso de o runbook ser suspenso e deve ser reiniciado desse ponto de verificação em um trabalho diferente.

## <a name="runbook-input-and-output"></a>Entrada e saída de runbook

### Entrada de runbook<a name="runbook-input"></a>

Um runbook requer entrada de um usuário que inicia o runbook por meio do portal do Azure ou de outro runbook, se o atual for usado como um filho. Por exemplo, para um runbook que cria uma máquina virtual, o usuário pode precisar fornecer informações como o nome da máquina virtual e outras propriedades sempre que o runbook for iniciado.

O runbook aceita entrada definindo um ou mais parâmetros de entrada. O usuário fornece valores para esses parâmetros cada vez que o runbook é iniciado. Quando o usuário inicia o runbook usando o portal do Azure, é solicitado que o usuário forneça valores para cada parâmetro de entrada com suporte no runbook.

Ao criar seu runbook, você pode acessar seus parâmetros de entrada clicando no botão **entrada e saída** na barra de ferramentas do runbook. Isso abre o controle entrada e saída, onde você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Nome | Obrigatórios. O nome do parâmetro. O nome deve ser exclusivo dentro do runbook. Ele deve começar com uma letra e pode conter apenas letras, números e sublinhados. O nome não pode conter um espaço. |
| DESCRIÇÃO |Opcional. Descrição da finalidade do parâmetro de entrada. |
| Type | Opcional. Tipo de dados esperado para o valor do parâmetro. O Portal do Azure fornece um controle apropriado para o tipo de dados para cada parâmetro quando a entrada for solicitada. Os tipos de parâmetro com suporte são String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se um tipo de dados não for selecionado, o padrão será String.|
| Obrigatório | Opcional. Configuração que especifica se um valor deve ser fornecido para o parâmetro. Se você escolher **Sim**, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher **não**, um valor não será necessário quando o runbook for iniciado e um valor padrão poderá ser usado. O runbook não poderá ser iniciado se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Padrão | Opcional. O valor usado para um parâmetro se um não for passado quando o runbook for iniciado. Para definir um valor padrão, escolha **personalizado**. Selecione **nenhum** se você não quiser fornecer nenhum valor padrão. |

### <a name="runbook-output"></a>Saída de runbook

A criação gráfica salva os dados criados por qualquer atividade que não tenha um link de saída para o [resultado do runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é salva com o trabalho do runbook e está disponível para um runbook pai quando o runbook é usado como filho.

## <a name="powershell-expressions"></a>Expressões do PowerShell

Uma das vantagens da criação gráfica é que ele permite que você crie um runbook com conhecimento mínimo do PowerShell. No entanto, no entanto, você precisa saber um pouco do PowerShell para popular determinados [valores de parâmetro](#activities) e definir [condições de vínculo](#links-and-workflow). Esta seção fornece uma breve introdução às expressões do PowerShell. Detalhes completos do PowerShell estão disponíveis em [scripts com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Fonte de dados de expressão do PowerShell

Você pode usar uma expressão do PowerShell como uma fonte de dados para popular o valor de um [parâmetro de atividade](#activities) com os resultados do código do PowerShell. A expressão pode ser uma única linha de código que executa uma função simples ou várias linhas que executam uma lógica complexa. Qualquer saída de um comando que não está atribuído a uma variável é enviado para o valor do parâmetro.

Por exemplo, o comando a seguir gera a data atual.

```powershell-interactive
Get-Date
```

O próximo trecho de código cria uma cadeia de caracteres a partir da data atual e a atribui a uma variável. O código envia o conteúdo da variável para a saída.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os comandos a seguir avaliam a data atual e retornam uma cadeia de caracteres que indica se o dia atual é um fim de semana ou um dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Saída de Atividade

Para usar a saída de uma atividade anterior em seu runbook, use a variável *ActivityOutput* com a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, você pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual. Nesse caso, o runbook pode usar a expressão a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se a propriedade exigir o objeto de máquina virtual em vez de apenas um nome, o runbook retornará o objeto inteiro usando a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

O runbook pode usar a saída de uma atividade em uma expressão mais complexa, como a seguinte. Essa expressão concatena o texto ao nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Condições

Use [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de true ou false.

Por exemplo, a condição a seguir determina se a máquina virtual de uma atividade chamada **Get-AzureVM** está atualmente parada.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A condição a seguir determina se a mesma máquina virtual está em qualquer estado diferente de parado.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Você pode unir várias condições em seu runbook usando um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx), como **-e** ou **-ou**. Por exemplo, a condição a seguir verifica se a máquina virtual no exemplo anterior está em um estado de **parado** ou **parando**.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelas de hash

As [Hashtables](https://technet.microsoft.com/library/hh847780.aspx) são pares de nome-valor que são úteis para retornar um conjunto de valores. Você também pode ver uma tabela de hash referenciada como um dicionário. As propriedades de determinadas atividades esperam uma tabela de hash em vez de um valor simples.

Crie uma tabela de hash usando a sintaxe a seguir. Ele pode conter qualquer número de entradas, mas cada uma é definida por um nome e um valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma tabela de hash a ser usada como fonte de dados para um parâmetro de atividade que espera uma tabela de hash de valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir usam a saída de uma atividade chamada **Obter conexão do Twitter** para preencher uma tabela de hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Autenticação para recursos do Azure

Os runbooks na Automação do Azure que gerenciam os recursos do Azure requerem a autenticação do Azure. A [conta Executar como](automation-create-runas-account.md), também conhecida como entidade de serviço, é o mecanismo padrão que um runbook de automação usa para acessar Azure Resource Manager recursos em sua assinatura. Você pode adicionar essa funcionalidade a um runbook gráfico adicionando o ativo de conexão **AzureRunAsConnection** , que usa o cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) do PowerShell, à tela. Você também pode adicionar o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Esse cenário é ilustrado no exemplo a seguir.

![Executa Como Atividades de Autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A **atividade obter conexão executar como**ou **Get-AutomationConnection**é configurada com uma fonte de dados de valor constante chamada **AzureRunAsConnection**.

![Executar Como Configuração da Conexão](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, **Connect-AzAccount**, adiciona a conta Executar como autenticada para uso no runbook.

![Conjunto de parâmetros Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Para runbooks do PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são aliases para **Connect-AzAccount**. Observe que esses aliases não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só pode usar o **Connect-AzAccount** em si.

Para os campos de parâmetro, **APPLICATIONID**, **CERTIFICATETHUMBPRINT**e **tenantid**, especifique o nome da propriedade para o caminho do campo, já que a atividade produz um objeto com várias propriedades. Caso contrário, quando o runbook for executado, ele falhará ao tentar autenticar. Isso é o que você precisa, no mínimo, para autenticar seu runbook com a conta Executar Como.

Alguns assinantes criam uma conta de automação usando uma [conta de usuário do Azure ad](automation-create-aduser-account.md) para gerenciar a implantação clássica do Azure ou para recursos de Azure Resource Manager. Para manter a compatibilidade com versões anteriores para esses assinantes, o mecanismo de autenticação a ser usado em seu runbook é o cmdlet **Add-AzureAccount** com um [ativo de credencial](automation-credentials.md). O ativo representa um usuário Active Directory com acesso à conta do Azure.

Você pode habilitar essa funcionalidade para o runbook gráfico adicionando um ativo de credencial à tela, seguido por uma atividade **Add-AzureAccount** que usa o ativo de credencial para sua entrada. Veja os exemplos a seguir.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

O runbook deve autenticar em seu início e depois de cada ponto de verificação. Portanto, você deve usar uma atividade **Add-AzureAccount** após qualquer atividade **de fluxo de trabalho** . Você não precisa usar uma atividade de credencial adicional.

![Saída de Atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Exportar e importar um runbook gráfico

Você pode exportar apenas a versão publicada de um runbook gráfico. Se o runbook ainda não tiver sido publicado, o botão **Exportar** será desabilitado. Quando você clica no botão **Exportar** , o runbook é baixado para o computador local. O nome do arquivo corresponde ao nome do runbook com uma extensão **. graphrunbook** .

Você pode importar um arquivo de runbook de fluxo de trabalho gráfico ou gráfico do PowerShell selecionando a opção **importar** ao adicionar um runbook. Ao selecionar o arquivo a ser importado, você pode manter o mesmo nome ou fornecer um novo. O campo **tipo de runbook** exibe o tipo de runbook depois de avaliar o arquivo selecionado. Se você tentar selecionar um tipo diferente que não esteja correto, o editor gráfico apresentará uma mensagem indicando que há conflitos em potencial e pode haver erros de sintaxe durante a conversão.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>Testando um runbook gráfico

Cada runbook gráfico na automação do Azure tem uma versão de rascunho e uma versão publicada. Você pode executar apenas a versão publicada, enquanto só pode editar a versão de rascunho. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de rascunho estiver pronta para uso, você a publicará, o que substitui a versão atual publicada pela sua versão de rascunho.

Você pode testar a versão de rascunho de um runbook no portal do Azure enquanto deixa a versão publicada inalterada. Como alternativa, você pode testar um novo runbook antes que ele tenha sido publicado para que você possa verificar se o runbook funciona corretamente antes de qualquer substituição de versão. O teste de um runbook executa a versão de rascunho e garante que todas as ações que ele executa sejam concluídas. Nenhum histórico de trabalho é criado, mas o painel saída de teste exibe a saída.

Abra o controle de teste para seu runbook gráfico abrindo o runbook para editar e, em seguida, clicando em **painel de teste**. O controle de teste solicita parâmetros de entrada e você pode iniciar o runbook clicando em **Iniciar**.

## <a name="publishing-a-graphical-runbook"></a>Publicando um runbook gráfico

Publique um runbook gráfico abrindo o runbook para edição e, em seguida, clicando em **publicar**. Os status possíveis para o runbook são:

* Novo – o runbook ainda não foi publicado. 
* Publicado--o runbook foi publicado.
* Em editar – o runbook foi editado depois de ser publicado, e o rascunho e as versões publicadas são diferentes.

![Status de runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Você tem a opção de reverter para a versão publicada de um runbook. Esta operação gera as alterações feitas desde a última publicação do runbook. Ele substitui a versão de rascunho do runbook pela versão publicada.

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para saber mais sobre tipos de runbook e suas vantagens e limitações, confira [tipos de runbook de automação do Azure](automation-runbook-types.md).
* Para entender como autenticar usando a conta Executar como da automação, consulte [Configurar a conta Executar como do Azure](automation-sec-configure-azure-runas-account.md).
