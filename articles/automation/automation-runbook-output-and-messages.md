---
title: Saída e mensagens do runbook na Automação do Azure
description: Descreve como criar e recuperar mensagens de saída e erro de runbooks no Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535529"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Saída e mensagens do runbook na Automação do Azure

A maioria dos runbooks de Automação do Azure tem alguma forma de saída. Esta saída pode ser uma mensagem de erro para o usuário ou um objeto complexo destinado a ser usado com outro runbook. O Windows PowerShell oferece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para o envio da saída de um script ou de um de fluxo de trabalho. A Automação do Azure trabalha com cada um desses fluxos de maneira diferente. Você deve seguir as melhores práticas para usar os fluxos quando estiver criando um runbook.

A tabela a seguir descreve brevemente cada fluxo com seu comportamento no portal Azure para runbooks publicados e durante [o teste de um runbook](automation-testing-runbook.md). O fluxo Output é o principal fluxo usado para comunicação entre runbooks. Os outros fluxos são classificados como fluxos de mensagens, destinados a comunicar informações ao usuário. 

| STREAM | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Erro |Mensagem de erro para o usuário. Ao contrário de uma exceção, o livro de execução continua após uma mensagem de erro por padrão. |Escrito para a história do trabalho |Exibido no painel de saída de teste |
| Depurar |As mensagens destinadas a um usuário interativo. Não deve ser usado em runbooks. |Não escrito para a história do trabalho |Não exibido no painel de saída de teste |
| Saída |Objetos que se destinam a consumo de outros runbooks. |Escrito para a história do trabalho |Exibido no painel de saída de teste |
| Andamento |Os registros são automaticamente gerados antes e depois de cada atividade no runbook. O runbook não deve tentar criar seus próprios registros de progresso, uma vez que eles são destinados a um usuário interativo. |Escrito para o histórico de trabalho somente se o registro de progresso for ligado para o runbook |Não exibido no painel de saída de teste |
| Detalhado |Mensagens que fornecem informações gerais ou de depuração. |Escrito para o histórico de trabalho somente se o registro verbose é ligado para o runbook |Exibido no painel de saída `VerbosePreference` de teste somente se a variável estiver definida para Continuar no runbook |
| Aviso |Mensagem de aviso para o usuário. |Escrito para a história do trabalho |Exibido no painel de saída de teste |

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="output-stream"></a>Fluxo de saída

O fluxo 'Saída' é usado para a saída de objetos criados por um script ou fluxo de trabalho quando é executado corretamente. O Azure Automation usa principalmente esse fluxo para que objetos sejam consumidos por runbooks dos pais que chamam de [runbook atual](automation-child-runbooks.md). Quando um pai [chama um runbook inline,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)a criança retorna os dados do fluxo De saída para o pai. 

Seu runbook usa o fluxo De saída para comunicar informações gerais ao cliente apenas se ele nunca for chamado por outro runbook. Como uma prática recomendada, no entanto, você deve usar o [fluxo Verbose](#verbose-stream) para comunicar informações gerais ao usuário.

Faça com que os dados de gravação do caderneta sejam transmitidos pelo fluxo de saída usando ['Saída de gravação'.](https://technet.microsoft.com/library/hh849921.aspx) Alternativamente, você pode colocar o objeto em sua própria linha no script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Manipulação da saída de uma função

Quando uma função de runbook é escrita para o fluxo Desaída, a saída é transmitida de volta para o runbook. Se o runbook atribuir essa saída a uma variável, a saída não será escrita no fluxo de saída. A gravação de qualquer outro fluxo de dentro da função grava ao fluxo correspondente para o runbook. Considere o seguinte manual de fluxo de trabalho powershell.

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

O fluxo Verbose para o trabalho de runbook é:

```output
Verbose outside of function
Verbose inside of function
```

Depois de publicar o livro de execução e antes de iniciá-lo, você também deve ativar o registro verbose nas configurações do runbook para obter a saída do fluxo Verbose.

### <a name="declaring-output-data-type"></a>Declarando o tipo de dados de saída

A seguir estão exemplos de tipos de dados de saída:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declare o tipo de dados de saída em um fluxo de trabalho

Um fluxo de trabalho especifica o tipo de dados de sua saída usando o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Este atributo não tem efeito durante o tempo de execução, mas fornece uma indicação no momento do projeto da saída esperada do runbook. À medida que o conjunto de ferramentas para runbooks continua a evoluir, a importância de declarar os tipos de dados de saída no tempo de projeto aumenta. Portanto, é uma prática recomendada incluir esta declaração em todos os runbooks que você criar.

O exemplo de runbook a seguir gera um objeto de cadeia de caracteres e inclui uma declaração de seu tipo de saída. Se seu runbook gerar uma matriz de um determinado tipo, você ainda deverá especificar o tipo como oposto a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declare o tipo de dados de saída em um runbook gráfico

Para declarar um tipo de saída em um runbook gráfico ou gráfico powershell workflow, você pode selecionar a opção De entrada **e saída** menu e digitar o tipo de saída. Recomenda-se usar o nome completo da classe .NET para tornar o tipo facilmente identificável quando um runbook pai o referencia. O uso do nome completo expõe todas as propriedades da classe ao databus no manual e aumenta a flexibilidade quando as propriedades são usadas para lógica condicional, registro e referência como valores para outras atividades de runbook.<br> ![Opção Entrada e Saída de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Depois de inserir um valor no campo **Tipo de saída** no painel de propriedades Entrada e Saída, certifique-se de clicar fora do controle para que ele reconheça sua entrada.

O exemplo a seguir mostra dois runbooks gráficos para demonstrar o recurso Entrada e Saída. Aplicando o modelo modular de design de runbook, você tem um runbook como o modelo Authenticate Runbook gerenciando a autenticação com o Azure usando a conta Run As. O segundo runbook, que normalmente executa a lógica central para automatizar um determinado cenário, neste caso executa o modelo Authenticate Runbook. Ele exibe os resultados no painel de saída do teste. Sob circunstâncias normais, esse runbook teria de fazer algo em um recurso aproveitando a saída do runbook filho.

Veja a lógica básica do runbook** AuthenticateTo-Azure**.<br> ![Exemplo de modelo de runbook de autenticação](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

O runbook inclui o `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`tipo de saída, que retorna as propriedades do perfil de autenticação.<br> ![Exemplo de tipo de saída de runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora este runbook seja simples, há um item de configuração para chamar aqui. A última atividade `Write-Output` executa o cmdlet para gravar dados de `Inputobject` perfil para uma variável usando uma expressão PowerShell para o parâmetro. Este parâmetro é `Write-Output`necessário para .

O segundo runbook neste exemplo, chamado **Test-ChildOutputType,** simplesmente define duas atividades.<br> ![Runbook de tipo de saída de exemplo filho](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o manual do **AuthenticateTo-Azure.** A segunda atividade `Write-Verbose` executa o cmdlet com **a fonte de dados** definida para a saída de **atividade**. Além disso, **o caminho de campo** está definido como **Context.Subscription.SubscriptionName**, a saída de contexto do runbook **AuthenticateTo-Azure.**<br> ![Fonte de dados do parâmetro write-Verbose Cmdlet](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da assinatura.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Fluxos de mensagens

Ao contrário do fluxo De saída, os fluxos de mensagem comunicam informações ao usuário. Existem vários fluxos de mensagens para diferentes tipos de informações, e a Azure Automation lida com cada fluxo de forma diferente.

### <a name="warning-and-error-streams"></a>Fluxos de aviso e de erro

O registro de erros de aviso e erro que ocorrem em um runbook. A Azure Automation escreve esses fluxos para o histórico de trabalho ao executar um runbook. A automação inclui os fluxos no painel de saída de teste no portal Azure quando um runbook é testado. 

Por padrão, um runbook continua a ser executado após um aviso ou erro. Você pode especificar que seu runbook deve ser suspenso em um aviso ou erro, tendo o runbook definido uma [variável de preferência](#preference-variables) antes de criar a mensagem. Por exemplo, para fazer com que o manual de execução `ErrorActionPreference` seja suspenso por um erro como faz em uma exceção, defina a variável como Stop.

Crie uma mensagem de aviso ou de erro usando o cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). As atividades também podem ser escritas nos fluxos Aviso e Erro.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Fluxo de Depuração

O Azure Automation usa o fluxo de mensagem Debug para usuários interativos. Não deve ser usado em runbooks.

### <a name="verbose-stream"></a>Fluxo Detalhado

O fluxo de mensagens Verbose suporta informações gerais sobre a operação do runbook. Como o fluxo Debug não está disponível para um livro de execução, seu livro de execução deve usar mensagens verbose para depurar informações. 

Por padrão, o histórico de trabalho não armazena mensagens verbosas de runbooks publicados, por razões de desempenho. Para armazenar mensagens verbosas, use a guia **Configurar** o portal Azure com a configuração **Log Verbose Records** para configurar seus runbooks publicados para registrar mensagens verbose. Só ative essa opção para solucionar problemas ou depurar um runbook. Na maioria dos casos, você deve manter a configuração padrão de não registrar registros verbos.

Quando [testar um runbook](automation-testing-runbook.md), as mensagens detalhadas não serão exibidas mesmo que o runbook esteja configurado para registros detalhados de log. Para exibir mensagens verbosas durante [o teste de um livro de execução,](automation-testing-runbook.md)você deve definir a `VerbosePreference` variável como Continuar. Com esse conjunto de variáveis, as mensagens verbose são exibidas no painel de saída de teste do portal Azure.

O código a seguir cria uma mensagem verbosa usando o [cmdlet Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Registros de andamento

Você pode usar a guia **Configurar** do portal Azure para configurar um manual para registrar registros de progresso. A configuração padrão é não registrar os registros, para maximizar o desempenho. Na maioria dos casos, você deve manter a configuração padrão. Só ative essa opção para solucionar problemas ou depurar um runbook. 

Se você habilitar o registro de registro de progresso, seu runbook gravará um registro no histórico de trabalho antes e depois de cada atividade ser executada. Testar um runbook não exibe mensagens de progresso mesmo se o manual estiver configurado para registrar registros de progresso.

>[!NOTE]
>O cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) não é válido em um runbook, já que esse cmdlet é destinado ao uso com um usuário interativo.

## <a name="preference-variables"></a>Variáveis de preferência

Você pode definir certas variáveis de preferência do Windows PowerShell em seus runbooks para controlar a resposta aos dados enviados para [diferentes](https://technet.microsoft.com/library/hh847796.aspx) fluxos de saída. A tabela a seguir lista as variáveis de preferência que podem ser usadas em runbooks, com seus valores padrão e válidos. Valores adicionais estão disponíveis para as variáveis de preferência quando usados no Windows PowerShell fora do Azure Automation.

| Variável | Valor Padrão | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

A tabela seguinte lista o comportamento dos valores variáveis de preferência válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continue |Registra em log a mensagem e continua executando o runbook. |
| SilentlyContinue |Continua executando o runbook sem registrar em log a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Stop |Registra em log a mensagem e suspende o runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperar saída e mensagens do runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperar saída de runbook e mensagens no portal Azure

Você pode ver os detalhes de um trabalho de runbook no portal Azure usando a guia **Empregos** para o runbook. O resumo do trabalho exibe os parâmetros de entrada e o [fluxo de saída,](#output-stream)além de informações gerais sobre o trabalho e quaisquer exceções que tenham ocorrido. O histórico de trabalho inclui mensagens do fluxo de saída e [fluxos de aviso e erro](#warning-and-error-streams). Ele também inclui mensagens do [fluxo Verbose](#verbose-stream) e [registros de progresso](#progress-records) se o livro de execução estiver configurado para registrar registros verbos e progress.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperar a saída do runbook e as mensagens no Windows PowerShell

No Windows PowerShell, você pode recuperar saídas e mensagens de um runbook usando o [cmdlet Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Este cmdlet requer o ID do trabalho `Stream` e tem um parâmetro chamado para especificar o fluxo para recuperar. Você pode especificar um valor de Any para este parâmetro para recuperar todos os fluxos para o trabalho.

O exemplo a seguir inicia um runbook de exemplo e aguarda a sua conclusão. Uma vez que o runbook complete a execução, o script coleta o fluxo de saída do runbook do trabalho.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperar a saída do runbook e as mensagens em runbooks gráficos

Para runbooks gráficos, o registro extra de saída e mensagens está disponível na forma de rastreamento em nível de atividade. Há dois níveis de rastreamento: Básico e Detalhado. O rastreamento básico exibe o tempo de início e fim de cada atividade no manual, além de informações relacionadas a qualquer repetição de atividade. Alguns exemplos são o número de tentativas e o tempo de início da atividade. O rastreamento detalhado inclui recursos básicos de rastreamento, além de registro de dados de entrada e saída para cada atividade. 

Atualmente, o rastreamento em nível de atividade grava registros usando o fluxo Verbose. Portanto, você deve habilitar o registro verbose quando você habilitar o rastreamento. Para runbooks gráficos com rastreamento habilitado, não há necessidade de registros de progresso do log. O rastreamento básico serve para o mesmo propósito e é mais informativo.

![Modo de exibição Transmissões de Trabalho da criação gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Você pode ver pela imagem que permitir o registro e rastreamento de verbos para runbooks gráficos torna muito mais informações disponíveis na exibição **de Job Streams** de produção. Essas informações extras podem ser essenciais para solucionar problemas de produção com um runbook. 

No entanto, a menos que você exija essas informações para acompanhar o progresso de um runbook para solução de problemas, você pode querer continuar rastreando desligado como uma prática geral. Os registros de rastreamento podem ser especialmente numerosos. Com o rastreamento de runbook gráfico, você pode obter de dois a quatro registros por atividade, dependendo da configuração do rastreamento básico ou detalhado.

**Para habilitar o rastreamento em nível de atividade:**

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Na página Runbooks, selecione um runbook gráfico da sua lista de runbooks.
4. Em **Configurações**, clique em **Log e rastreamento**.
5. Na página Registro e Rastreamento, em **Log verbose records**, clique **em On** para ativar o registro verbose.
6. Em **rastreamento em nível de atividade,** altere o nível de rastreamento para **Básico** ou **Detalhado,** com base no nível de rastreamento necessário.<br>

   ![Página Registro e Rastreamento de Criação Gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperar a saída do runbook e as mensagens nos registros do Microsoft Azure Monitor

O Azure Automation pode enviar status de trabalho e fluxos de trabalho para o seu espaço de trabalho log analytics. O Azure Monitor suporta logs que permitem:

* Obter informações sobre os Trabalhos de automação.
* Acione um e-mail ou alerta com base no status do seu trabalho de runbook, por exemplo, Falhou ou Suspendeu.
* Escreva consultas avançadas em fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Visualize o histórico do trabalho.

Para obter mais informações sobre a configuração da integração com os logs do Monitor do Azure para coletar, correlacionar e agir sobre dados de trabalho, consulte [Avançar no status do trabalho e fluxos de trabalho de Automação para Logs do Monitor do Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre execução de runbook, monitoramento de trabalhos em runbook e outros detalhes técnicos, consulte [Track a runbook job](automation-runbook-execution.md).
* Para entender como projetar e usar livros de execução infantil, consulte [Livros de execução infantil no Azure Automation](automation-child-runbooks.md).
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](/powershell/scripting/overview).
