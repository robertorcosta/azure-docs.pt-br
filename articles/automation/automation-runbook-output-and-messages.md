---
title: Saída e mensagens do runbook na Automação do Azure
description: Descreve como criar e recuperar mensagens de saída e de erro de runbooks na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535529"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Saída e mensagens do runbook na Automação do Azure

A maioria dos runbooks de Automação do Azure tem alguma forma de saída. Essa saída pode ser uma mensagem de erro para o usuário ou um objeto complexo destinado a ser usado com outro runbook. O Windows PowerShell oferece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para o envio da saída de um script ou de um de fluxo de trabalho. A Automação do Azure trabalha com cada um desses fluxos de maneira diferente. Você deve seguir as práticas recomendadas para usar os fluxos quando estiver criando um runbook.

A tabela a seguir descreve brevemente cada fluxo com seu comportamento na portal do Azure para runbooks publicados e durante [o teste de um runbook](automation-testing-runbook.md). O fluxo de saída é o fluxo principal usado para comunicação entre runbooks. Os outros fluxos são classificados como fluxos de mensagens, destinados a comunicar informações para o usuário. 

| STREAM | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Erro do |Mensagem de erro para o usuário. Ao contrário de uma exceção, o runbook continua depois de uma mensagem de erro por padrão. |Gravado no histórico do trabalho |Exibido no painel de saída de teste |
| Depurar |As mensagens destinadas a um usuário interativo. Não deve ser usado em runbooks. |Não gravado no histórico do trabalho |Não exibido no painel de saída de teste |
| Saída |Objetos que se destinam a consumo de outros runbooks. |Gravado no histórico do trabalho |Exibido no painel de saída de teste |
| Andamento |Os registros são automaticamente gerados antes e depois de cada atividade no runbook. O runbook não deve tentar criar seus próprios registros de progresso, pois eles são destinados a um usuário interativo. |Gravado no histórico de trabalho somente se o log de progresso estiver ativado para o runbook |Não exibido no painel de saída de teste |
| Detalhado |Mensagens que fornecem informações gerais ou de depuração. |Gravado no histórico de trabalho somente se o log detalhado estiver ativado para o runbook |Exibido no painel de saída de teste `VerbosePreference` somente se a variável estiver definida para continuar no runbook |
| Aviso |Mensagem de aviso para o usuário. |Gravado no histórico do trabalho |Exibido no painel de saída de teste |

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="output-stream"></a>Fluxo de saída

O fluxo de saída é usado para a saída de objetos criados por um script ou fluxo de trabalho quando ele é executado corretamente. A automação do Azure usa principalmente esse fluxo para que os objetos sejam consumidos por runbooks pai que chamam o [runbook atual](automation-child-runbooks.md). Quando um pai [chama um runbook embutido](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), o filho retorna dados do fluxo de saída para o pai. 

Seu runbook usará o fluxo de saída para comunicar informações gerais ao cliente somente se ele nunca for chamado por outro runbook. Como prática recomendada, no entanto, os runbooks normalmente devem usar o [fluxo detalhado](#verbose-stream) para comunicar informações gerais ao usuário.

Faça com que seu runbook grave dados no fluxo de saída usando [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). Como alternativa, você pode colocar o objeto em sua própria linha no script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Manipulando a saída de uma função

Quando uma função de runbook grava no fluxo de saída, a saída é passada de volta para o runbook. Se o runbook atribuir essa saída a uma variável, a saída não será gravada no fluxo de saída. A gravação de qualquer outro fluxo de dentro da função grava ao fluxo correspondente para o runbook. Considere o seguinte runbook de fluxo de trabalho do PowerShell de exemplo.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

O fluxo de saída para o trabalho de runbook é:

```output
Output inside of function
Output outside of function
```

O fluxo detalhado para o trabalho de runbook é:

```output
Verbose outside of function
Verbose inside of function
```

Depois de publicar o runbook e antes de iniciá-lo, você também deve ativar o log detalhado nas configurações do runbook para obter a saída de fluxo detalhado.

### <a name="declaring-output-data-type"></a>Declarando o tipo de dados de saída

Veja a seguir exemplos de tipos de dados de saída:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declarar o tipo de dados de saída em um fluxo de trabalho

Um fluxo de trabalho especifica o tipo de dados de sua saída usando o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Esse atributo não tem nenhum efeito durante o tempo de execução, mas fornece uma indicação no tempo de design da saída esperada do runbook. Como o conjunto de ferramentas para runbooks continua a evoluir, a importância de declarar tipos de dados de saída em tempo de design aumenta. Portanto, é uma prática recomendada incluir essa declaração em todos os runbooks que você criar.

O exemplo de runbook a seguir gera um objeto de cadeia de caracteres e inclui uma declaração de seu tipo de saída. Se seu runbook gerar uma matriz de um determinado tipo, você ainda deverá especificar o tipo como oposto a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declarar o tipo de dados de saída em um runbook gráfico

Para declarar um tipo de saída em um runbook de fluxo de trabalho gráfico ou gráfico do PowerShell, você pode selecionar a opção de menu **entrada e saída** e inserir o tipo de saída. É recomendável usar o nome completo da classe .NET para tornar o tipo facilmente identificável quando um runbook pai faz referência a ele. O uso do nome completo expõe todas as propriedades da classe para as DataBus no runbook e aumenta a flexibilidade quando as propriedades são usadas para lógica condicional, registro em log e referência como valores para outras atividades de runbook.<br> ![Opção Entrada e Saída de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Depois de inserir um valor no campo **tipo de saída** no painel Propriedades de entrada e saída, certifique-se de clicar fora do controle para que ele reconheça sua entrada.

O exemplo a seguir mostra dois runbooks gráficos para demonstrar o recurso de entrada e saída. Aplicando o modelo de design modular de runbook, você tem um runbook como o modelo de runbook de autenticação que gerencia a autenticação com o Azure usando a conta Executar como. O segundo runbook, que normalmente executa a lógica principal para automatizar um determinado cenário, nesse caso executa o modelo de autenticação de runbook. Ele exibe os resultados para o painel de saída de teste. Sob circunstâncias normais, esse runbook teria de fazer algo em um recurso aproveitando a saída do runbook filho.

Veja a lógica básica do runbook** AuthenticateTo-Azure**.<br> ![Exemplo de modelo de runbook de autenticação](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

O runbook inclui o tipo `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`de saída, que retorna as propriedades do perfil de autenticação.<br> ![Exemplo de tipo de saída de runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora esse runbook seja simples, há um item de configuração para chamar aqui. A última atividade executa o `Write-Output` cmdlet para gravar dados de perfil em uma variável usando uma expressão do PowerShell para `Inputobject` o parâmetro. Esse parâmetro é necessário para `Write-Output`.

O segundo runbook neste exemplo, chamado **Test-ChildOutputType**, simplesmente define duas atividades.<br> ![Runbook de tipo de saída de exemplo filho](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o runbook **authenticateto-Azure** . A segunda atividade executa o `Write-Verbose` cmdlet com a **fonte de dados** definida como **saída da atividade**. Além disso, o **caminho do campo** é definido como **Context. Subscription. subscriptionname**, a saída do contexto do runbook **authenticateto-Azure** .<br> ![Fonte de dados de parâmetro de cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da assinatura.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Fluxos de mensagens

Ao contrário do fluxo de saída, os fluxos de mensagens comunicam as informações ao usuário. Há vários fluxos de mensagens para diferentes tipos de informações, e a automação do Azure manipula cada fluxo de forma diferente.

### <a name="warning-and-error-streams"></a>Fluxos de aviso e de erro

O aviso e os fluxos de erro registram problemas que ocorrem em um runbook. A automação do Azure grava esses fluxos no histórico de trabalho ao executar um runbook. A automação inclui os fluxos no painel de saída de teste no portal do Azure quando um runbook é testado. 

Por padrão, um runbook continua a ser executado após um aviso ou erro. Você pode especificar que o runbook deve suspender em um aviso ou erro fazendo com que o runbook defina uma [variável de preferência](#preference-variables) antes de criar a mensagem. Por exemplo, para fazer com que o runbook suspenda um erro como faz em uma exceção, defina a `ErrorActionPreference` variável como parar.

Crie uma mensagem de aviso ou de erro usando o cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). As atividades também podem gravar nos fluxos de aviso e de erro.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Fluxo de Depuração

A automação do Azure usa o fluxo de mensagens de depuração para usuários interativos. Ele não deve ser usado em runbooks.

### <a name="verbose-stream"></a>Fluxo Detalhado

O fluxo de mensagens detalhados dá suporte a informações gerais sobre a operação de runbook. Como o fluxo de depuração não está disponível para um runbook, seu runbook deve usar mensagens detalhadas para informações de depuração. 

Por padrão, o histórico de trabalhos não armazena mensagens detalhadas de runbooks publicados, por motivos de desempenho. Para armazenar mensagens detalhadas, use a guia portal do Azure **Configurar** com a configuração **registros detalhados de log** para configurar seus runbooks publicados para registrar mensagens detalhadas. Só ative essa opção para solucionar problemas ou depurar um runbook. Na maioria dos casos, você deve manter a configuração padrão de não registrar em log os registros detalhados.

Quando [testar um runbook](automation-testing-runbook.md), as mensagens detalhadas não serão exibidas mesmo que o runbook esteja configurado para registros detalhados de log. Para exibir mensagens detalhadas durante [o teste de um runbook](automation-testing-runbook.md), você deve `VerbosePreference` definir a variável para continuar. Com esse conjunto de variáveis, as mensagens detalhadas são exibidas no painel saída de teste do portal do Azure.

O código a seguir cria uma mensagem detalhada usando o cmdlet [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Registros de andamento

Você pode usar a guia **Configurar** do portal do Azure para configurar um runbook para registrar em log os registros de progresso. A configuração padrão é não registrar os registros para maximizar o desempenho. Na maioria dos casos, você deve manter a configuração padrão. Só ative essa opção para solucionar problemas ou depurar um runbook. 

Se você habilitar o log de registros de andamento, o runbook gravará um registro no histórico de trabalhos antes e depois de cada atividade ser executada. O teste de um runbook não exibe mensagens de progresso mesmo se o runbook estiver configurado para registrar em log os registros de progresso.

>[!NOTE]
>O cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) não é válido em um runbook, já que esse cmdlet é destinado ao uso com um usuário interativo.

## <a name="preference-variables"></a>Variáveis de preferência

Você pode definir certas variáveis de [preferência](https://technet.microsoft.com/library/hh847796.aspx) do Windows PowerShell em seus runbooks para controlar a resposta aos dados enviados a fluxos de saída diferentes. A tabela a seguir lista as variáveis de preferência que podem ser usadas em runbooks, com seus valores padrão e válidos. Valores adicionais estão disponíveis para as variáveis de preferência quando usados no Windows PowerShell fora da automação do Azure.

| Variável | Valor padrão | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

A tabela a seguir lista o comportamento dos valores de variáveis de preferência que são válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continue |Registra em log a mensagem e continua executando o runbook. |
| SilentlyContinue |Continua executando o runbook sem registrar em log a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Stop |Registra em log a mensagem e suspende o runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperar saída e mensagens do runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperar mensagens e saída do runbook no portal do Azure

Você pode exibir os detalhes de um trabalho de runbook no portal do Azure usando a guia **trabalhos** para o runbook. O resumo do trabalho exibe os parâmetros de entrada e o [fluxo de saída](#output-stream), além de informações gerais sobre o trabalho e quaisquer exceções ocorridas. O histórico de trabalhos inclui mensagens do fluxo de saída e dos [fluxos de aviso e erro](#warning-and-error-streams). Ele também inclui mensagens do [fluxo detalhado](#verbose-stream) e dos [registros de progresso](#progress-records) se o runbook estiver configurado para registrar em log os registros detalhados e de andamento.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperar mensagens e saída do runbook no Windows PowerShell

No Windows PowerShell, você pode recuperar a saída e as mensagens de um runbook usando o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) . Esse cmdlet requer a ID do trabalho e tem um parâmetro chamado `Stream` no qual especificar o fluxo a ser recuperado. Você pode especificar um valor para esse parâmetro para recuperar todos os fluxos para o trabalho.

O exemplo a seguir inicia um runbook de exemplo e aguarda a sua conclusão. Depois que o runbook conclui a execução, o script coleta o fluxo de saída do runbook do trabalho.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperar mensagens e saída do runbook em runbooks gráficos

Para runbooks gráficos, o log extra de saída e mensagens está disponível na forma de rastreamento de nível de atividade. Há dois níveis de rastreamento: Básico e Detalhado. O rastreamento básico exibe a hora de início e de término de cada atividade no runbook, além de informações relacionadas a qualquer nova tentativa de atividade. Alguns exemplos são o número de tentativas e a hora de início da atividade. O rastreamento detalhado inclui recursos básicos de rastreamento, além de registro em log de dados de entrada e saída para cada atividade. 

Atualmente, o rastreamento de nível de atividade grava registros usando o fluxo detalhado. Portanto, você deve habilitar o log detalhado ao habilitar o rastreamento. Para runbooks gráficos com rastreamento habilitado, não há necessidade de registros de progresso do log. O rastreamento básico serve para o mesmo propósito e é mais informativo.

![Modo de exibição Transmissões de Trabalho da criação gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Você pode ver na imagem que habilitar o log detalhado e o rastreamento de runbooks gráficos torna muito mais informações disponíveis no modo de exibição **fluxos de trabalho** de produção. Essas informações adicionais podem ser essenciais para solucionar problemas de produção com um runbook. 

No entanto, a menos que você exija essas informações para acompanhar o progresso de um runbook para solução de problemas, convém manter o rastreamento desativado como uma prática geral. Os registros de rastreamento podem ser especialmente numerosos. Com o rastreamento de runbook gráfico, você pode obter de dois a quatro registros por atividade, dependendo da configuração do rastreamento básico ou detalhado.

**Para habilitar o rastreamento em nível de atividade:**

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **automação de processo** para abrir a lista de runbooks.
3. Na página Runbooks, selecione um runbook gráfico na lista de runbooks.
4. Em **Configurações**, clique em **Log e rastreamento**.
5. Na página log e rastreamento, em **registros detalhados de log**, clique **em** ativar para habilitar o log detalhado.
6. Em **rastreamento de nível de atividade**, altere o nível de rastreamento para **básico** ou **detalhado**, com base no nível de rastreamento que você precisa.<br>

   ![Página Registro e Rastreamento de Criação Gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperar mensagens e saída do runbook em logs do Microsoft Azure monitor

A automação do Azure pode enviar fluxos de trabalho e status de trabalho de runbook para seu espaço de trabalho Log Analytics. O Azure Monitor dá suporte a logs que permitem:

* Obter informações sobre os Trabalhos de automação.
* Dispare um email ou alerta com base no status do trabalho de runbook, por exemplo, com falha ou suspenso.
* Escrever consultas avançadas entre fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Visualize o histórico de trabalhos.

Para obter mais informações sobre como configurar a integração com os logs de Azure Monitor para coletar, correlacionar e agir sobre os dados do trabalho, consulte [encaminhar o status do trabalho e fluxos de trabalho de automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a execução de runbook, o monitoramento de trabalhos de runbook e outros detalhes técnicos, consulte [rastrear um trabalho de runbook](automation-runbook-execution.md).
* Para entender como projetar e usar runbooks filho, consulte [Runbooks filho na automação do Azure](automation-child-runbooks.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](/powershell/scripting/overview).
