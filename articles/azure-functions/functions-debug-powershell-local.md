---
title: Debug PowerShell Azure funções localmente
description: Entenda como desenvolver funções usando o PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163753"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debug PowerShell Azure funções localmente

O Azure Functions permite que você desenvolva suas funções como scripts PowerShell.

Você pode depurar suas funções powershell localmente como qualquer script do PowerShell usando as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): O editor de texto gratuito, leve e de código aberto da Microsoft com a extensão PowerShell que oferece uma experiência completa de desenvolvimento do PowerShell.
* Um console PowerShell: Depurar usando os mesmos comandos que você usaria para depurar qualquer outro processo powershell.

[O Azure Functions Core Tools](functions-run-local.md) suporta a depuração local das funções do Azure, incluindo funções PowerShell.

## <a name="example-function-app"></a>Aplicativo de função exemplo

O aplicativo de função usado neste artigo tem uma única função acionada HTTP e tem os seguintes arquivos:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Este aplicativo de função é semelhante ao que você recebe quando completa o [PowerShell quickstart](functions-create-first-function-powershell.md).

O código `run.ps1` de função em parece o seguinte script:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Defina o ponto de conexão

Para depurar qualquer função PowerShell, a função precisa parar para que o depurador seja anexado. O `Wait-Debugger` cmdlet interrompe a execução e aguarda o depurador.

Tudo o que você precisa fazer `Wait-Debugger` é adicionar uma `if` chamada ao cmdlet logo acima da declaração, da seguinte forma:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

A depuração `if` começa na declaração. 

Com `Wait-Debugger` o lugar no lugar, agora você pode depurar as funções usando o Visual Studio Code ou um console PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depuração no Código visual do estúdio

Para depurar suas funções powershell no Visual Studio Code, você deve ter as seguintes instalações:

* [Extensão PowerShell para Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Extensão Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar essas dependências, carregue um projeto de funções PowerShell existente ou [crie seu primeiro projeto PowerShell Functions](functions-create-first-function-powershell.md).

>[!NOTE]
> Se o seu projeto não tiver os arquivos de configuração necessários, você é solicitado a adicioná-los.

### <a name="set-the-powershell-version"></a>Defina a versão do PowerShell

O PowerShell Core é instalado lado a lado com o Windows PowerShell. Defina o PowerShell Core como a versão PowerShell para usar com a extensão PowerShell para Visual Studio Code.

1. Pressione F1 para exibir o palete de comando e, em seguida, procure . `Session`

1. Escolha **PowerShell: Mostrar menu de sessão**.

1. Se **a sessão atual** não for **PowerShell Core 6,** escolha **'Alternar para: PowerShell Core 6**.

Quando você tem um arquivo PowerShell aberto, você vê a versão exibida em verde no canto inferior direito da janela. A seleção deste texto também exibe o menu da sessão. Para saber mais, consulte [a Escolha de uma versão do PowerShell para usar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Inicie o aplicativo de função

Verifique `Wait-Debugger` se está definido na função onde deseja anexar o depurador.  Com `Wait-Debugger` adicionado, você pode depurar seu aplicativo de função usando o Visual Studio Code.

Escolha o painel **Debug** e, em seguida, **conecte-se à função PowerShell**.

![depurador](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Você também pode pressionar a tecla F5 para iniciar a depuração.

A operação de depuração inicial faz as seguintes tarefas:

* É `func extensions install` executado no terminal para instalar quaisquer extensões de funções do Azure exigidas pelo aplicativo de função.
* É `func host start` executado no terminal para iniciar o aplicativo de função no host Funções.
* Conecte o depurador PowerShell ao espaço de execução do PowerShell dentro do tempo de execução functions.

>[!NOTE]
> Você precisa garantir que o PSWorkerInProcConcurrencyUpperBound esteja definido como 1 para garantir a experiência correta de depuração no Visual Studio Code. Esse é o padrão.

Com o aplicativo de função em execução, você precisa de um console PowerShell separado para chamar a função http ativada.

Neste caso, o console PowerShell é o cliente. O `Invoke-RestMethod` é usado para ativar a função.

Em um console PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você notará que uma resposta não é imediatamente devolvida. Isso porque `Wait-Debugger` a execução do depurador e do PowerShell entrou no modo de quebra assim que pôde. Isso é por causa do [conceito BreakAll,](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)que é explicado mais tarde. Depois de `continue` pressionar o botão, o depurador `Wait-Debugger`agora quebra a linha logo após .

Neste ponto, o depurador está conectado e você pode fazer todas as operações normais de depuração. Para obter mais informações sobre como usar o depurador no Visual Studio Code, consulte [a documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continuar e invocar totalmente o seu script, você notará que:

* O console PowerShell `Invoke-RestMethod` que fez o retornou um resultado
* O Console Integrado PowerShell em Visual Studio Code está esperando um script ser executado

Mais tarde, quando você invoca a mesma função, o `Wait-Debugger`depurador na extensão PowerShell quebra logo após o .

## <a name="debugging-in-a-powershell-console"></a>Depuração em um console PowerShell

>[!NOTE]
> Esta seção pressupõe que você leu os [docs do Azure Functions Core Tools](functions-run-local.md) e sabe como usar o `func host start` comando para iniciar seu aplicativo de função.

Abra um console, `cd` no diretório do seu aplicativo de função e execute o seguinte comando:

```sh
func host start
```

Com o aplicativo de `Wait-Debugger` função em execução e o no lugar, você pode anexar ao processo. Você precisa de mais dois consoles PowerShell.

Um dos consoles atua como cliente. A partir disso, você liga `Invoke-RestMethod` para acionar a função. Por exemplo, você pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você notará que ele não retorna uma resposta, que `Wait-Debugger`é o resultado do . O espaço de execução do PowerShell está agora à espera da conexão de um depurador. Vamos apegar isso.

No outro console PowerShell, execute o seguinte comando:

```powershell
Get-PSHostProcessInfo
```

Este cmdlet retorna uma tabela que se parece com a seguinte saída:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Anote o `ProcessId` para o item na `ProcessName` `dotnet`tabela com o as . Este processo é o seu aplicativo de função.

Em seguida, execute o seguinte trecho:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Uma vez iniciado, o depurador quebra e mostra algo como a seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste ponto, você está parado em um ponto de ruptura no [depurador PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). A partir daqui, você pode fazer todas as operações habituais de depuração, passar por cima, entrar, continuar, sair, e outros. Para ver o conjunto completo de comandos de `h` depuração disponíveis no console, execute os comandos ou. `?`

Você também pode definir pontos de `Set-PSBreakpoint` interrupção neste nível com o cmdlet.

Uma vez que você continuar e invocar totalmente o seu script, você notará que:

* O console PowerShell onde `Invoke-RestMethod` você executou agora retornou um resultado.
* O console PowerShell onde `Debug-Runspace` você executou está esperando que um script seja executado.

Você pode invocar a mesma `Invoke-RestMethod` função novamente (usando, por exemplo) `Wait-Debugger` e o depurador entra logo após o comando.

## <a name="considerations-for-debugging"></a>Considerações para depuração

Tenha em mente os seguintes problemas ao depurar seu código de funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`pode fazer com que o seu depurador para quebrar em um lugar inesperado

A extensão `Debug-Runspace`PowerShell usa , que por `BreakAll` sua vez conta com o recurso do PowerShell. Esse recurso diz ao PowerShell para parar no primeiro comando executado. Esse comportamento lhe dá a oportunidade de definir pontos de interrupção dentro do runspace depurado.

O tempo de execução do Azure Functions `run.ps1` executa alguns comandos antes de realmente invocar `Microsoft.Azure.Functions.PowerShellWorker.psm1` seu `Microsoft.Azure.Functions.PowerShellWorker.psd1`script, então é possível que o depurador acabe quebrando dentro do ou .

Se essa quebra acontecer, execute o `continue` comando ou `c` para pular este ponto de ruptura. Então você para no ponto de ruptura esperado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de funções usando o PowerShell, consulte [o guia de desenvolvedores do Azure Functions PowerShell](functions-reference-powershell.md).
