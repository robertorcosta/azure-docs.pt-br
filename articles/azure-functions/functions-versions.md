---
title: Visão geral de versões do Azure Functions runtime
description: O Azure Functions é compatível com várias versões do runtime. Aprenda as diferenças entre elas e como escolher a certa para você.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276550"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral de versões do Azure Functions runtime

As principais versões do tempo de execução das funções do Azure estão relacionadas à versão do .NET no qual o tempo de execução é baseado. A tabela a seguir indica a versão atual do tempo de execução, o nível de versão e a versão .NET relacionada. 

| Versão de runtime | Nível de liberação<sup>1</sup> | Versão do .NET | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Núcleo 3.1 | 
| 2. x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup> Lançamentos ga são suportados para cenários de produção.   
<sup>2</sup> A versão 1.x está em modo de manutenção. Os aprimoramentos são fornecidos apenas em versões posteriores.   
<sup>3</sup> Só suporta o desenvolvimento no portal Azure ou localmente em computadores Windows.

Este artigo detalha algumas das diferenças entre as várias versões, como você pode criar cada versão e como alterar as versões.

## <a name="languages"></a>Languages

A partir da versão 2.x, o tempo de execução usa um modelo de extensibilidade de linguagem, e todas as funções em um aplicativo de função devem compartilhar o mesmo idioma. A linguagem das funções em um aplicativo de função é escolhida ao criar o aplicativo e é mantida na configuração [FUNCTIONS\_WORKER\_RUNTIME.](functions-app-settings.md#functions_worker_runtime) 

As funções do Azure 1.x as linguagens experimentais não podem usar o novo modelo, por isso não são suportadas em 2.x. A tabela a seguir indica quais linguagens de programação são atualmente compatíveis com cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [idiomas suportados](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Executar em uma versão específica

Por padrão, os aplicativos de função criados no portal Azure e pelo Azure CLI são definidos para a versão 3.x. Você pode modificar esta versão conforme necessário. Você só pode alterar a versão em tempo de execução para 1.x depois de criar seu aplicativo de função, mas antes de adicionar quaisquer funções.  Mover-se entre 2.x e 3.x é permitido mesmo com aplicativos que têm funções, mas ainda é recomendado testar em um novo aplicativo primeiro.

## <a name="migrating-from-1x-to-later-versions"></a>Migrando de 1.x para versões posteriores

Você pode optar por migrar um aplicativo existente escrito para usar a versão 1.x tempo de execução para, em vez disso, usar uma versão mais recente. A maioria das alterações que você precisa fazer está relacionada a alterações no tempo de execução do idioma, como alterações de API C# entre .NET Framework 4.7 e .NET Core. Você também precisará verificar se seu código e suas bibliotecas são compatíveis com o runtime de linguagem escolhido. Por fim, não deixe de observar as alterações no gatilho, associações e recursos destacados abaixo. Para obter os melhores resultados de migração, você deve criar um novo aplicativo de função em uma nova versão e portar o código de função da versão 1.x existente para o novo aplicativo.  

Embora seja possível fazer uma atualização "no lugar" atualizando manualmente a configuração do aplicativo, passar de 1.x para uma versão mais alta inclui algumas alterações de ruptura. Por exemplo, em C#, o objeto `TraceWriter` de `ILogger`depuração é alterado de . Ao criar um novo projeto versão 3.x, você começa com funções atualizadas com base nos modelos mais recentes da versão 3.x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Alterações nos gatilhos e vinculações após a versão 1.x

A partir da versão 2.x, você deve instalar as extensões para gatilhos e vinculações específicas usados pelas funções do seu aplicativo. A única exceção para isso são os gatilhos de temporizador e HTTP, que não exigem uma extensão.  Para obter mais informações, confira [Registrar e instalar extensões de associação](./functions-bindings-register.md).

Há também algumas mudanças na *função.json* ou atributos da função entre as versões. Por exemplo, a propriedade `path` do Hub de Eventos agora é `eventHubName`. Veja a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Alterações nos recursos e funcionalidadeapós a versão 1.x

Alguns recursos foram removidos, atualizados ou substituídos após a versão 1.x. Esta seção detalha as alterações que você vê em versões posteriores depois de ter usado a versão 1.x.

As seguintes alterações foram feitas na versão 2.x:

* As chaves para chamar pontos de extremidade HTTP são sempre armazenadas criptografadas no Armazenamento de Blobs do Azure. Na versão 1.x, as chaves foram armazenadas no armazenamento de arquivos Do Azure por padrão. Ao atualizar um aplicativo da versão 1.x para a 2.x, os segredos existentes que estão no armazenamento de arquivos são redefinidos.

* A versão 2.x do runtime não inclui suporte interno para provedores de webhook. Essa alteração foi feita para melhorar o desempenho. Você ainda pode usar gatilhos HTTP como pontos de extremidade para webhooks.

* O arquivo de configuração de host (host.json) deve estar vazio ou ter a cadeia de caracteres `"version": "2.0"`.

* Para melhorar o monitoramento, o painel WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) no portal, que usou a configuração, [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) é substituído pelo Azure Application Insights, que usa a configuração. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

* Todas as funções em um aplicativo de funções devem compartilhar a mesma linguagem de programação. Quando você cria um aplicativo de funções, você deve escolher para ele uma pilha de runtime. A pilha de tempo de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) execução é especificada pelo valor nas configurações do aplicativo. Esse requisito foi adicionado para melhorar o tempo de inicialização e o volume. Ao desenvolver localmente, você também deve incluir essa configuração no [arquivo local.settings.json](functions-run-local.md#local-settings-file).

* O tempo limite padrão para as funções em um plano do Serviço de Aplicativo foi alterado para 30 minutos. Você pode alterar manualmente o tempo limite para ilimitado, usando a configuração [functionTimeout](functions-host-json.md#functiontimeout) em host.json.

* Os aceleradores de concorrência HTTP são implementados por padrão para funções do plano de consumo, com um padrão de 100 solicitações simultâneas por instância. Você pode alterar [`maxConcurrentRequests`](functions-host-json.md#http) isso na configuração no arquivo host.json.

* Devido [às limitações do .NET Core,](https://github.com/Azure/azure-functions-host/issues/3414)o suporte às funções de script F# (.fsx) foi removido. Funções F# compiladas (.fs) ainda são compatíveis.

* O formato da URL de webhooks de gatilho da Grade de Eventos foi alterado para `https://{app}/runtime/webhooks/{triggerName}`.

## <a name="migrating-from-2x-to-3x"></a>Migração de 2,x para 3,x

A versão 3.x do Azure Functions é altamente retrocompatível com a versão 2.x.  Muitos aplicativos devem ser capazes de atualizar com segurança para 3.x sem qualquer alteração de código.  Enquanto a mudança para o 3.x é encorajada, certifique-se de executar testes extensivos antes de alterar a versão principal em aplicativos de produção.

### <a name="breaking-changes-between-2x-and-3x"></a>Quebrando mudanças entre 2.x e 3.x

A seguir, as alterações a serem atentas antes de atualizar um aplicativo 2.x para 3.x.

#### <a name="javascript"></a>JavaScript

* As vinculações de `context.done` saída atribuídas através ou valores de retorno agora se comportam da mesma forma que a configuração em `context.bindings`.

* Objeto de gatilho do temporizador é cameloCaso em vez de PascalCase

* As funções desencadeadas `dataType` pelo Event Hub `binary` com `string`binário receberão uma matriz de em vez de .

* A carga útil da solicitação HTTP `context.bindingData.req`não pode mais ser acessada via .  Ele ainda pode ser acessado como `context.req`um parâmetro `context.bindings`de entrada, e em .

* O Node.js 8 não é mais suportado e não será executado em funções 3.x.

#### <a name="net"></a>.NET

* [As operações síncronas do servidor são desativadas por padrão](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Como alterar a versão de aplicativos no Azure

A versão do tempo de execução functions usado por aplicativos [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) publicados no Azure é ditada pela configuração do aplicativo. Os seguintes principais valores de versão em tempo de execução são suportados:

| Valor | Alvo de tempo de execução |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2. x |
| `~1` | 1.x |

>[!IMPORTANT]
> Não altere arbitrariamente essa configuração, porque outras alterações de configuração de aplicativos e alterações no código da função podem ser necessárias.

### <a name="locally-developed-application-versions"></a>Versões de aplicativos desenvolvidas localmente

Você pode fazer as seguintes atualizações para funções de aplicativos para alterar localmente as versões direcionadas.

#### <a name="visual-studio-runtime-versions"></a>Versões de runtime do Visual Studio

No Visual Studio, você seleciona a versão de runtime quando cria um projeto. As ferramentas azure Functions para visual studio suportam as três principais versões em tempo de execução. A versão correta é usada ao depurar e publicar com base nas configurações do projeto. As configurações de versão são definidas no arquivo `.csproj` nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versão 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> As funções Azure 3.x `Microsoft.NET.Sdk.Functions` e .NET `3.0.0`exigem que a extensão seja pelo menos .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Atualizando aplicativos 2.x para 3.x no Visual Studio

Você pode abrir uma função existente direcionando 2.x e passar `.csproj` para 3.x editando o arquivo e atualizando os valores acima.  O Visual Studio gerencia versões em tempo de execução automaticamente para você com base em metadados do projeto.  No entanto, é possível se você nunca criou um aplicativo 3.x antes que o Visual Studio ainda não tenha os modelos e o tempo de execução para 3.x em sua máquina.  Isso pode apresentar-se com um erro como "não há funções de tempo de execução disponível que corresponda à versão especificada no projeto".  Para buscar os modelos mais recentes e o tempo de execução, passe pela experiência para criar um novo projeto de função.  Quando você chegar à tela de seleção de versão e modelo, aguarde que o Visual Studio complete os modelos mais recentes.  Uma vez que os modelos mais recentes do .NET Core 3 estejam disponíveis e exibidos, você poderá executar e depurar qualquer projeto configurado para a versão 3.x.

> [!IMPORTANT]
> As funções da versão 3.x só podem ser desenvolvidas no Visual Studio se usarem a versão 16.4 do Visual Studio ou mais recente.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

O [Azure Functions Core Tools](functions-run-local.md) é usado para o desenvolvimento de linha de comando e também pela [extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver contra a versão 3.x, instale a versão 3.x do Core Tools. O desenvolvimento da versão 2.x requer a versão 2.x do Core Tools, e assim por diante. Para obter mais informações, confira [Instalar o Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Para desenvolvimento no Visual Studio Code, você também precisará atualizar a configuração do usuário para o `azureFunctions.projectRuntime` corresponder à versão das ferramentas instaladas.  Essa configuração também atualiza os modelos e linguagens utilizados durante a criação do aplicativo de funções.  Para criar `~3` aplicativos, você `azureFunctions.projectRuntime` atualizaria a configuração do usuário para `~3`.

![Configuração de tempo de execução de funções do Azure](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplicativos Maven e Java

Você pode migrar aplicativos Java da versão 2.x para 3.x [instalando a versão 3.x das ferramentas principais necessárias](functions-run-local.md#install-the-azure-functions-core-tools) para serem executadas localmente.  Depois de verificar se seu aplicativo funciona corretamente rodando localmente `POM.xml` na versão `FUNCTIONS_EXTENSION_VERSION` 3.x, atualize o arquivo do aplicativo para modificar a configuração para `~3`, como no exemplo a seguir:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Associações

A partir da versão 2.x, o tempo de execução usa um novo [modelo de extensibilidade de vinculação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece essas vantagens:

* Suporte para extensões de associação de terceiros.

* Desacoplamento de runtime e associações. Essa alteração permite que extensões de associação sejam lançadas independentemente e com controle de versão. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia em uma versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, onde apenas as associações em uso são conhecidas e carregadas pelo runtime.

Com exceção dos gatilhos HTTP e de temporizador, todas as associações devem ser explicitamente adicionadas ao projeto do aplicativo de funções ou registradas no portal. Para obter mais informações, confira [Registrar extensões de associação](./functions-bindings-expressions-patterns.md).

A tabela a seguir mostra quais associações são compatíveis em cada versão de runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Codificar e testar o Azure Functions localmente](functions-run-local.md)
* [Como direcionar para versões do Azure Functions Runtime](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
