---
title: Referência do desenvolvedor PowerShell para funções do Azure
description: Entenda como desenvolver funções usando o PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276732"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia de desenvolvedor do Azure Functions PowerShell

Este artigo fornece detalhes sobre como você escreve funções do Azure usando o PowerShell.

Uma função PowerShell Azure (função) é representada como um script PowerShell que é executado quando acionado. Cada script de `function.json` função tem um arquivo relacionado que define como a função se comporta, como como ela é acionada e seus parâmetros de entrada e saída. Para saber mais, consulte os [Gatilhos e o artigo vinculante](functions-triggers-bindings.md). 

Como outros tipos de funções, as funções de script do PowerShell tomam `function.json` parâmetros que correspondem aos nomes de todas as vinculações de entrada definidas no arquivo. Também `TriggerMetadata` é passado um parâmetro que contém informações adicionais sobre o gatilho que iniciou a função.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Você também deveria ter concluído o [quickstart functions para powershell](functions-create-first-function-powershell.md) para criar sua primeira função PowerShell.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pasta necessária para um projeto PowerShell parece a seguir. Este padrão pode ser alterado. Para mais informações, consulte a seção [scriptArquivo](#configure-function-scriptfile) abaixo.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Na raiz do projeto, há um [`host.json`](functions-host-json.md) arquivo compartilhado que pode ser usado para configurar o aplicativo de função. Cada função tem uma pasta com seu próprio arquivo de`function.json`código (.ps1) e arquivo de configuração vinculante ( ). O nome do diretório pai do arquivo function.json é sempre o nome da sua função.

Certas ligações requerem a `extensions.csproj` presença de um arquivo. As extensões de vinculação, exigidas nas [versões 2.x e posteriores](functions-versions.md) do tempo de execução functions, são definidas no `extensions.csproj` arquivo, com os arquivos reais da `bin` biblioteca na pasta. Ao desenvolver localmente, você precisa [registrar as extensões de associação](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

Nos aplicativos de função PowerShell, `profile.ps1` você pode opcionalmente ter um que é executado quando um aplicativo de função começa a ser executado (de outra forma conhecido como um *[início frio](#cold-start)*. Para obter mais informações, consulte [o perfil PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script PowerShell como uma função

Por padrão, o runtime de Funções procura sua função em `run.ps1`, onde `run.ps1` compartilha o mesmo diretório pai que o `function.json` correspondente.

Seu script é aprovado uma série de argumentos sobre a execução. Para lidar com esses `param` parâmetros, adicione um bloco à parte superior do seu script como no exemplo a seguir:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O `TriggerMetadata` parâmetro é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de vinculação `sys` à vinculação, mas todos contêm uma propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Type     |
|------------|-------------------------------------------------|----------|
| Utcnow     | Quando, na UTC, a função foi acionada        | Datetime |
| MethodName | O nome da Função que foi acionada     | string   |
| RandGuid   | uma orientação única para esta execução da função | string   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, `$TriggerMetadata` `QueueTrigger` o `InsertionTime`for `Id` `DequeueCount`contém o , , , , entre outras coisas. Para obter mais informações sobre os metadados do gatilho da fila, acesse a [documentação oficial para acionamentos de fila](functions-bindings-storage-queue-trigger.md#message-metadata). Verifique a documentação nos [gatilhos](functions-triggers-bindings.md) com os quais você está trabalhando para ver o que vem dentro dos metadados do gatilho.

## <a name="bindings"></a>Associações

No PowerShell, [as vinculações](functions-triggers-bindings.md) são configuradas e definidas na função de uma função.json. As funções interagem com as ligações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Gatilho de leitura e dados de entrada

As ligações de gatilho e entrada são lidas à medida que os parâmetros são passados para sua função. As ligações de `direction` entrada `in` têm um conjunto para em function.json. A `name` propriedade `function.json` definida é o nome do `param` parâmetro, no bloco. Uma vez que o PowerShell usa parâmetros nomeados para vinculação, a ordem dos parâmetros não importa. No entanto, é uma prática recomendada para seguir a `function.json`ordem das vinculações definidas no .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Gravar dados de saída

Em Funções, uma vinculação de saída tem um `direction` conjunto para `out` na função.json. Você pode escrever para uma `Push-OutputBinding` vinculação de saída usando o cmdlet, que está disponível para o tempo de execução functions. Em todos os `name` casos, a propriedade `function.json` da vinculação definida corresponde ao `Name` parâmetro do `Push-OutputBinding` cmdlet.

A seguir, mostra `Push-OutputBinding` como chamar no seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Você também pode passar em um valor para uma ligação específica através do pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`comporta-se de forma diferente com `-Name`base no valor especificado para:

* Quando o nome especificado não pode ser resolvido em uma vinculação de saída válida, então um erro é jogado.

* Quando a vinculação de saída aceita uma `Push-OutputBinding` coleção de valores, você pode ligar repetidamente para empurrar vários valores.

* Quando a vinculação de saída aceita `Push-OutputBinding` apenas um valor singleton, chamar uma segunda vez aumenta um erro.

#### <a name="push-outputbinding-syntax"></a>Sintaxe de `Push-OutputBinding`

A seguir estão os `Push-OutputBinding`parâmetros válidos para a chamada:

| Nome | Type | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | O nome da vinculação de saída que você deseja definir. |
| **`-Value`** | Objeto | 2 | O valor da vinculação de saída que você deseja definir, que é aceito a partir do pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | nomeado | (Opcional) Quando especificado, força o valor a ser definido para uma vinculação de saída especificada. | 

Os seguintes parâmetros comuns também são suportados: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para obter mais informações, consulte [Sobre CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de vinculação push-output: respostas HTTP

Um gatilho HTTP retorna uma resposta `response`usando uma vinculação de saída nomeada . No exemplo a seguir, `response` a vinculação de saída tem o valor de "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída é http, que aceita apenas um valor `Push-OutputBinding` singleton, um erro é jogado quando é chamado uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que só aceitam valores de singleton, você pode usar o `-Clobber` parâmetro para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir assume que você já adicionou um valor. Ao `-Clobber`usar, a resposta do exemplo a seguir substitui o valor existente para devolver um valor de "#3 de saída":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de vinculação push-output: vinculação de saída de fila

`Push-OutputBinding`é usado para enviar dados para vinculações de saída, como uma [vinculação de saída de armazenamento azure Fila .](functions-bindings-storage-queue-output.md) No exemplo a seguir, a mensagem escrita na fila tem um valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A vinculação de saída para uma fila de armazenamento aceita vários valores de saída. Neste caso, chamar o exemplo a seguir após a primeira grava na fila uma lista com dois itens: "saída #1" e "saída #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo a seguir, quando chamado após os dois anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando escrita na fila, a mensagem contém estes quatro valores: "saída #1", "saída #2", "saída #3" e "saída #4".

#### <a name="get-outputbinding-cmdlet"></a>cmdlet `Get-OutputBinding`

Você pode `Get-OutputBinding` usar o cmdlet para recuperar os valores atualmente definidos para as vinculações de saída. Este cmdlet recupera uma hashtable que contém os nomes das vinculações de saída com seus respectivos valores. 

A seguir, um `Get-OutputBinding` exemplo de uso para retornar valores de vinculação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`também contém um `-Name`parâmetro chamado , que pode ser usado para filtrar a vinculação retornada, como no exemplo a seguir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Curingas (*) são `Get-OutputBinding`suportados em .

## <a name="logging"></a>Registrando em log

O login em funções powerShell funciona como o registro regular do PowerShell. Você pode usar os cmdlets de registro para escrever em cada fluxo de saída. Cada cmdlet mapeia para um nível de log usado por Funções.

| Nível de registro de funções | Cmdlet de registro |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Grava para registro de nível _de informação._ |
| Depurar | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além desses cmdlets, qualquer coisa escrita no pipeline `Information` é redirecionada para o nível de registro e exibida com a formatação padrão do PowerShell.

> [!IMPORTANT]
> Usar `Write-Verbose` os `Write-Debug` cmdlets não é suficiente para ver o registro de nível de verbose e depuração. Você também deve configurar o limite de nível de log, que declara qual nível de logs você realmente se importa. Para saber mais, consulte [Configurar o nível de log do aplicativo de função](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configure o nível de log do aplicativo de função

O Azure Functions permite definir o nível de limiar para facilitar o controle da forma como as funções gravam nos logs. Para definir o limite para todos os vestígios gravados no console, use a `logging.logLevel.default` propriedade na referência [ `host.json` ][host.json]do arquivo . Essa configuração se aplica a todas as funções em seu aplicativo de função.

O exemplo a seguir define o limiar para habilitar o registro verbose para todas `MyFunction`as funções, mas define o limiar para ativar o registro de depuração de uma função chamada :

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Para obter mais informações, consulte a [referência para host.json].

### <a name="viewing-the-logs"></a>Visualização dos registros

Se o aplicativo function for executado no Azure, você poderá usar o Application Insights para monitorá-lo. Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

Se você estiver executando seu aplicativo de função localmente para desenvolvimento, registre o padrão no sistema de arquivos. Para ver os logs no `AZURE_FUNCTIONS_ENVIRONMENT` console, `Development` defina a variável ambiente para antes de iniciar o Aplicativo função.

## <a name="triggers-and-bindings-types"></a>Tipos de gatilhos e vinculações

Há uma série de gatilhos e vinculações disponíveis para você usar com seu aplicativo de função. A lista completa de gatilhos e ligações [pode ser encontrada aqui.](functions-triggers-bindings.md#supported-bindings)

Todos os gatilhos e vinculações são representados em código como alguns tipos de dados reais:

* Hashtable
* string
* byte[]
* INT
* double
* HttpRequestContext
* HttpResponseContext

Os primeiros cinco tipos nesta lista são os tipos padrão .NET. Os dois últimos são usados apenas pelo [gatilho HttpTrigger](#http-triggers-and-bindings).

Cada parâmetro de ligação em suas funções deve ser um desses tipos.

### <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto da solicitação

O objeto de solicitação que é passado `HttpRequestContext`para o script é do tipo , que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo da solicitação. `Body`é serializado no melhor tipo com base nos dados. Por exemplo, se os dados são JSON, ele é passado como um hashtable. Se os dados são uma seqüência, é passado como uma seqüência. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de solicitação.                | Dicionário<string,string><sup>*</sup> |
| **`Method`** | O método HTTP da solicitação.                                | string                    |
| **`Params`**  | Um objeto que contém os parâmetros de roteamento da solicitação. | Dicionário<string,string><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros da consulta.                  | Dicionário<string,string><sup>*</sup> |
| **`Url`** | A URL da solicitação.                                        | string                    |

<sup>*</sup>Todas `Dictionary<string,string>` as chaves são insensíveis a casos.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que você `HttpResponseContext`deve enviar de volta é do tipo , que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma mão curta para definir o tipo de conteúdo para a resposta. | string                    |
| **`Headers`** | Um objeto que contém os cabeçalhos da resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de status HTTP da resposta.                       | cadeia de caracteres ou inteiro             |

#### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta

Quando você trabalha com gatilhos HTTP, você pode acessar a solicitação HTTP da mesma forma que faria com qualquer outra vinculação de entrada. Está no `param` quarteirão.

Use `HttpResponseContext` um objeto para retornar uma resposta, conforme mostrado no seguinte:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

O resultado da invocação desta função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Fundição de tipo para gatilhos e amarras

Para certas ligações como a ligação blob, você é capaz de especificar o tipo do parâmetro.

Por exemplo, para ter dados do armazenamento Blob fornecidos como `param` uma string, adicione o seguinte tipo de elenco ao meu bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, há o conceito de um perfil PowerShell. Se você não estiver familiarizado com os perfis do PowerShell, consulte [Sobre perfis](/powershell/module/microsoft.powershell.core/about/about_profiles).

Em Funções PowerShell, o script de perfil é executado quando o aplicativo de função é iniciado. Os aplicativos de função começam quando são implantados pela primeira vez e depois de ociosos[(partida a frio).](#cold-start)

Quando você cria um aplicativo de função usando ferramentas, como Visual Studio `profile.ps1` Code e Azure Functions Core Tools, um padrão é criado para você. O perfil padrão é mantido [no repositório Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação automática de MSI no Azure.
* A capacidade de ativar os pseudônimos Azure PowerShell `AzureRM` PowerShell, se você quiser.

## <a name="powershell-version"></a>Versão do PowerShell

A tabela a seguir mostra a versão PowerShell usada por cada versão principal do tempo de execução Functions:

| Versão do Functions | Versão do PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloqueado pelo tempo de execução) |
| 2. x               | Núcleo PowerShell 6                              |

Você pode ver a `$PSVersionTable` versão atual imprimindo de qualquer função.

## <a name="dependency-management"></a>Gerenciamento de dependência

As funções permitem que você aproveite a [galeria PowerShell](https://www.powershellgallery.com) para gerenciar dependências. Com o gerenciamento de dependência ativado, o arquivo requirements.psd1 é usado para baixar automaticamente os módulos necessários. Você habilita esse comportamento `managedDependency` definindo a propriedade na `true` raiz do arquivo [host.json,](functions-host-json.md)como no exemplo a seguir:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando você cria um novo projeto de funções do PowerShell, o gerenciamento de dependência é ativado por padrão, com o [ `Az` módulo](/powershell/azure/new-azureps-module-az) Azure incluído. O número máximo de módulos suportados atualmente é de 10. A sintaxe _`MajorNumber`_ `.*` suportada é ou a versão exata do módulo, conforme mostrado nos seguintes requisitos.exemplo psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando você atualiza o arquivo requirements.psd1, os módulos atualizados são instalados após uma reinicialização.

> [!NOTE]
> As dependências gerenciadas exigem acesso a www.powershellgallery.com para baixar módulos. Ao ser executado localmente, certifique-se de que o tempo de execução pode acessar essa URL adicionando quaisquer regras de firewall necessárias. 

As seguintes configurações de aplicativos podem ser usadas para alterar a forma como as dependências gerenciadas são baixadas e instaladas. A atualização do `MDMaxBackgroundUpgradePeriod`aplicativo começa dentro , e `MDNewSnapshotCheckPeriod`o processo de atualização é concluído dentro de aproximadamente o .

| Configuração do aplicativo de função              | Valor padrão             | Descrição                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dias)     | Cada processo do trabalhador PowerShell inicia a verificação de atualizações `MDMaxBackgroundUpgradePeriod` de módulos na Galeria PowerShell no início do processo e de cada vez depois disso. Quando uma nova versão do módulo está disponível na Galeria PowerShell, ela é instalada no sistema de arquivos e disponibilizada aos trabalhadores do PowerShell. A diminuição desse valor permite que seu aplicativo de função obtenha versões mais recentes do módulo mais cedo, mas também aumenta o uso de recursos do aplicativo (I/O de rede, CPU, armazenamento). Aumentar esse valor diminui o uso de recursos do aplicativo, mas também pode atrasar a entrega de novas versões de módulo para o seu aplicativo. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 hora)       | Depois que novas versões do módulo forem instaladas no sistema de arquivos, todos os processos de trabalhador do PowerShell devem ser reiniciados. A reinicialização dos trabalhadores do PowerShell afeta a disponibilidade do aplicativo, pois pode interromper a execução atual da função. Até que todos os processos do trabalhador PowerShell sejam reiniciados, as invocações de função podem usar as versões antiga ou do novo módulo. Reiniciando todos os `MDNewSnapshotCheckPeriod`trabalhadores da PowerShell concluídos dentro . Aumentar esse valor diminui a frequência de interrupções, mas também pode aumentar o período de tempo em que invocações de função usam as versões antigas ou do novo módulo não determinicamente. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 dia)     | Para evitar atualizações excessivas de módulos nas reinicializações frequentes do Worker, a verificação de atualizações de módulos não é realizada quando algum trabalhador já iniciou essa verificação na última `MDMinBackgroundUpgradePeriod`. |

Aproveitar seus próprios módulos personalizados é um pouco diferente de como você faria normalmente.

Em seu computador local, o módulo é instalado em uma `$env:PSModulePath`das pastas disponíveis globalmente em seu . Ao ser executado no Azure, você não tem acesso aos módulos instalados na sua máquina. Isso significa `$env:PSModulePath` que o aplicativo de função `$env:PSModulePath` For For PowerShell difere de um script PowerShell normal.

Em Funções, `PSModulePath` contém dois caminhos:

* Uma `Modules` pasta que existe na raiz do seu aplicativo de função.
* Um caminho `Modules` para uma pasta controlada pelo trabalhador de idiomas PowerShell.

### <a name="function-app-level-modules-folder"></a>Pasta de `Modules` nível de aplicativo de função

Para usar módulos personalizados, você pode colocar módulos `Modules` nos quais suas funções dependem de uma pasta. A partir desta pasta, os módulos estão automaticamente disponíveis para as funções em tempo de execução. Qualquer função no aplicativo de função pode usar esses módulos. 

> [!NOTE]
> Os módulos especificados no arquivo requirements.psd1 são automaticamente baixados e incluídos no caminho para que você não precise incluí-los na pasta de módulos. Estes são armazenados `$env:LOCALAPPDATA/AzureFunctions` localmente `/data/ManagedDependencies` na pasta e na pasta quando executados na nuvem.

Para aproveitar o recurso de módulo `Modules` personalizado, crie uma pasta na raiz do seu aplicativo de função. Copie os módulos que deseja usar em suas funções para este local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com `Modules` uma pasta, seu aplicativo de função deve ter a seguinte estrutura de pasta:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Quando você inicia seu aplicativo de função, `Modules` o `$env:PSModulePath` trabalhador de idiomas PowerShell adiciona essa pasta ao para que você possa confiar no carregamento automático do módulo, assim como faria em um script PowerShell normal.

### <a name="language-worker-level-modules-folder"></a>Pasta de `Modules` nível de trabalhador de idiomas

Vários módulos são comumente usados pelo trabalhador da linguagem PowerShell. Estes módulos são definidos `PSModulePath`na última posição de . 

A lista atual de módulos é a seguinte:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usado para `.zip`trabalhar `.nupkg`com arquivos, como , e outros.
* **ThreadJob**: Uma implementação baseada em thread das APIs de trabalho do PowerShell.

Por padrão, functions usa a versão mais recente desses módulos. Para usar uma versão específica do módulo, coloque essa versão específica na pasta do `Modules` seu aplicativo de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas `$env:NAME_OF_ENV_VAR`configurações usando, como mostrado no exemplo a seguir:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidos a partir de [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="concurrency"></a>Concorrência

Por padrão, o tempo de execução do Functions PowerShell só pode processar uma invocação de uma função por vez. No entanto, esse nível de simultuário pode não ser suficiente nas seguintes situações:

* Quando você está tentando lidar com um grande número de invocações ao mesmo tempo.
* Quando você tem funções que invocam outras funções dentro do mesmo aplicativo de função.

Você pode alterar esse comportamento definindo a seguinte variável de ambiente para um valor inteiro:

```
PSWorkerInProcConcurrencyUpperBound
```

Você define essa variável de ambiente nas [configurações](functions-app-settings.md) do aplicativo do seu Aplicativo de Função.

### <a name="considerations-for-using-concurrency"></a>Considerações para o uso de concurrency

PowerShell é uma única linguagem de script _roscada_ por padrão. No entanto, a concorrência pode ser adicionada usando vários espaços de execução do PowerShell no mesmo processo. A quantidade de espaços de execução criados corresponderá à configuração do aplicativo PSWorkerInProcConcurrencyUpperBound. O throughput será impactado pela quantidade de CPU e memória disponíveis no plano selecionado.

O Azure PowerShell usa alguns contextos de nível de processo e estado para ajudar a _salvá-lo_ do excesso de digitação. No entanto, se você ativar a concorrência em seu aplicativo de função e invocar ações que mudem de estado, você pode acabar com condições de corrida. Essas condições raciais são difíceis de depurar porque uma invocação depende de um determinado estado e a outra invocação mudou o estado.

Há um imenso valor em concurrency com o Azure PowerShell, já que algumas operações podem levar um tempo considerável. No entanto, você deve proceder com cautela. Se você suspeitar que está passando por uma condição de corrida, defina a configuração do aplicativo PSWorkerInProcConcurrencyUpperBound e, em vez disso, use o `1` isolamento do nível do processo do trabalhador do [idioma](functions-app-settings.md#functions_worker_process_count) para a concorrência.

## <a name="configure-function-scriptfile"></a>Configurar função`scriptFile`

Por padrão, uma função PowerShell `run.ps1`é executada a partir de um `function.json`arquivo que compartilha o mesmo diretório pai que seu correspondente .

A `scriptFile` propriedade `function.json` no pode ser usada para obter uma estrutura de pasta que se parece com o seguinte exemplo:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Neste caso, `function.json` o `myFunction` for `scriptFile` inclui uma propriedade referenciando o arquivo com a função exportada para executar.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Use módulos PowerShell configurando um entryPoint

Este artigo mostrou funções do `run.ps1` PowerShell no arquivo de script padrão gerado pelos modelos.
No entanto, você também pode incluir suas funções em módulos PowerShell. Você pode referenciar seu código de `scriptFile` função `entryPoint` específico no módulo usando os campos e campos no arquivo de configuração function.json.

Neste caso, `entryPoint` é o nome de uma função ou cmdlet `scriptFile`no módulo PowerShell referenciado em .

Considere a seguinte estrutura de pasta:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Onde `PSFunction.psm1` contém:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Neste exemplo, a `myFunction` configuração `scriptFile` para inclui `PSFunction.psm1`uma propriedade que faz referência, que é um módulo PowerShell em outra pasta.  A `entryPoint` propriedade faz `Invoke-PSTestFunc` referência à função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com esta configuração, o `Invoke-PSTestFunc` é `run.ps1` executado exatamente como um faria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções powershell

Quando trabalhar com funções PowerShell, esteja ciente das considerações nas seguintes seções.

### <a name="cold-start"></a>Inicialização a frio

Ao desenvolver funções do Azure no [modelo de hospedagem sem servidor,](functions-scale.md#consumption-plan)partidas frias são uma realidade. *O início* frio refere-se ao período de tempo necessário para que seu aplicativo de função comece a ser executado para processar uma solicitação. O início frio acontece com mais freqüência no plano de consumo porque seu aplicativo de função é desligado durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Módulos de pacote em vez de usar`Install-Module`

Seu roteiro é executado em cada invocação. Evite `Install-Module` usar em seu script. Em `Save-Module` vez disso, use antes de publicar para que sua função não tenha que perder tempo baixando o módulo. Se as partidas frias estiverem afetando suas funções, considere implantar seu aplicativo de função em um [plano de Serviço de Aplicativo](functions-scale.md#app-service-plan) definido para sempre *ligado* ou para um [plano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas do Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Azure Funções gatilhos e vinculações](functions-triggers-bindings.md)

[Referência de host.JSON]: functions-host-json.md
