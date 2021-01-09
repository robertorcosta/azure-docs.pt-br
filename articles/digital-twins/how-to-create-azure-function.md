---
title: Configurar uma função no Azure para processar dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função no Azure que pode acessar e ser disparada por gêmeos digital.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6c4f23406c97d647002fbb3ab4a3544866303cf4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051336"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Conectar aplicativos de funções no Azure para processamento de dados

A atualização do gêmeos digital com base em dados é tratada usando [**rotas de eventos**](concepts-route-events.md) por meio de recursos de computação, como uma função feita usando [Azure Functions](../azure-functions/functions-overview.md). As funções podem ser usadas para atualizar uma atualização digital em resposta a:
* dados de telemetria do dispositivo provenientes do Hub IoT
* alteração de propriedade ou outros dados provenientes de outra troca digital no grafo de entrelaçamento

Este artigo orienta você pela criação de uma função no Azure para uso com o gêmeos digital do Azure. 

Aqui está uma visão geral das etapas que ela contém:

1. Criar um projeto do Azure Functions no Visual Studio
2. Escrever uma função com um gatilho de [grade de eventos](../event-grid/overview.md)
3. Adicione o código de autenticação à função (para poder acessar o Azure digital gêmeos)
4. Publicar o aplicativo de funções no Azure
5. Configurar o acesso de [segurança](concepts-security.md) para o aplicativo de funções

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Pré-requisito: configurar a instância de gêmeos digital do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Criar um aplicativo de funções no Visual Studio

No Visual Studio 2019, selecione _arquivo > novo projeto de >_ e procure o modelo de _Azure Functions_ , selecione _Avançar_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Especifique um nome para o aplicativo de funções e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: configurar novo projeto":::

Selecione o tipo de *gatilho de grade de eventos* do aplicativo de funções e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: caixa de diálogo gatilho de projeto de Azure Functions":::

Depois que seu aplicativo de funções for criado, seu Visual Studio terá o exemplo de código preenchido automaticamente no arquivo **Function.cs** na pasta do projeto. Essa função curta é usada para registrar eventos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: janela do projeto com código de exemplo":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Escrever uma função com um gatilho de grade de eventos

Você pode escrever uma função adicionando o SDK ao seu aplicativo de funções. O aplicativo de funções interage com o gêmeos digital do Azure usando o [SDK do gêmeos digital do Azure para .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Para usar o SDK, você precisará incluir os pacotes a seguir em seu projeto. Você pode instalar os pacotes usando o Gerenciador de pacotes NuGet do Visual Studio ou adicionar os pacotes usando a `dotnet` ferramenta de linha de comando. Escolha um destes métodos: 

**Opção 1. Adicionar pacotes usando o Gerenciador de pacotes do Visual Studio:**
    
Você pode fazer isso selecionando o projeto com o botão direito do mouse e selecionando _gerenciar pacotes NuGet_ na lista. Em seguida, na janela que é aberta, selecione a guia _procurar_ e procure os pacotes a seguir. Selecione _instalar_ e _aceite_ o contrato de licença para instalar os pacotes.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Para que a configuração do pipeline do SDK do Azure seja configurada corretamente para Azure Functions, você também precisará dos pacotes a seguir. Repita o mesmo processo acima para instalar todos os pacotes.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opção 2. Adicionar pacotes usando `dotnet` a ferramenta de linha de comando:**

Como alternativa, você pode usar os seguintes `dotnet add` comandos em uma ferramenta de linha de comando:
```cmd/sh
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

Em seguida, adicione mais duas dependências ao seu projeto que serão necessárias para trabalhar com o gêmeos digital do Azure. Você pode usar os links abaixo para navegar até os pacotes no NuGet, onde encontrará os comandos de console (inclusive da CLI do .NET) para adicionar a versão mais recente de cada um ao seu projeto.
 * [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Este é o pacote para o [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
 * [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Esta biblioteca fornece ferramentas para ajudar com a autenticação no Azure.

Em seguida, no Gerenciador de Soluções do Visual Studio, abra o arquivo _Function.cs_ em que você tem um código de exemplo e adicione as instruções _using_ a seguir à sua função. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Adicionar código de autenticação à função

Agora, você declarará variáveis de nível de classe e adicionará o código de autenticação que permitirá que a função acesse o Azure digital gêmeos. Você adicionará o seguinte à sua função no arquivo {seu nome de função}. cs.

* Leia a URL do serviço ADT como uma variável de ambiente. É uma boa prática ler a URL do serviço de uma variável de ambiente, em vez de codificá-la embutidamente na função.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Uma variável estática para manter uma instância de HttpClient. O HttpClient é relativamente caro de criar e queremos evitar ter que fazer isso para cada invocação de função.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Você pode usar as credenciais de identidade gerenciadas no Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Adicione uma variável local _DigitalTwinsClient_ dentro de sua função para manter a instância do cliente gêmeos do Azure digital para o projeto de função. Não *torne essa* variável estática dentro de sua classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Adicione uma verificação nula para _adtInstanceUrl_ e empacote sua lógica de função em um bloco try catch para capturar quaisquer exceções.

Após essas alterações, o código de função será semelhante ao seguinte:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>Publicar o aplicativo de funções no Azure

Para publicar o projeto em um aplicativo de funções no Azure, selecione o projeto de função (não a solução) em Gerenciador de Soluções e escolha **publicar**.

> [!IMPORTANT] 
> A publicação em um aplicativo de funções no Azure incorre em encargos adicionais em sua assinatura, independentemente do gêmeos digital do Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: publicar função no Azure":::

Selecione **Azure** como o destino de publicação e selecione **Avançar**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: caixa de diálogo Publicar Azure Functions, selecione Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: caixa de diálogo Publicar função, selecione Azure Aplicativo de funções (Windows) ou (Linux) com base em seu computador":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: caixa de diálogo Publicar função, criar uma nova função do Azure":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: caixa de diálogo Publicar função, preencha os campos e selecione criar":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: caixa de diálogo Publicar função, selecione seu aplicativo de funções na lista e conclua":::

Na página a seguir, insira o nome desejado para o novo aplicativo de funções, um grupo de recursos e outros detalhes.
Para que seu aplicativo de funções possa acessar o Azure digital gêmeos, ele precisa ter uma identidade gerenciada pelo sistema e ter permissões para acessar sua instância do gêmeos digital do Azure.

Em seguida, você pode configurar o acesso de segurança para a função usando a CLI ou portal do Azure. Escolha um destes métodos:

## <a name="set-up-security-access-for-the-function-app"></a>Configurar o acesso de segurança para o aplicativo de funções
Você pode configurar o acesso de segurança para o aplicativo de funções usando uma destas opções:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Opção 1: configurar o acesso de segurança para o aplicativo de funções usando a CLI

O esqueleto de função dos exemplos anteriores requer que um token de portador seja passado para ele, a fim de ser capaz de autenticar com o gêmeos digital do Azure. Para garantir que esse token de portador seja passado, você precisará configurar [identidade de serviço gerenciada (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo de funções. Isso só precisa ser feito uma vez para cada aplicativo de funções.

Você pode criar uma identidade gerenciada pelo sistema e atribuir a identidade do aplicativo de funções à função de _**proprietário de dados do gêmeos digital do Azure**_ para sua instância do gêmeos digital do Azure. Isso dará à permissão do aplicativo de funções na instância para executar atividades de plano de dados. Em seguida, torne a URL da instância do Azure digital gêmeos acessível para sua função definindo uma variável de ambiente.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Use [Azure cloud Shell](https://shell.azure.com) para executar os comandos.

Use o comando a seguir para criar a identidade gerenciada pelo sistema. Anote o campo _principalId_ na saída.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Use o valor _PrincipalId_ no comando a seguir para atribuir a identidade do aplicativo de funções à função de _proprietário de dados do gêmeos digital do Azure_ para sua instância do gêmeos digital do Azure.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Por fim, você pode tornar a URL da sua instância de gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre como definir variáveis de ambiente, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> A URL da instância do gêmeos digital do Azure é feita adicionando *https://* ao início do *nome de host* da instância do Azure digital gêmeos. Para ver o nome do host, juntamente com todas as propriedades de sua instância, você pode executar `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Opção 2: configurar o acesso de segurança para o aplicativo de funções usando portal do Azure

Uma identidade gerenciada atribuída pelo sistema permite que os recursos do Azure se autentiquem nos serviços de nuvem (por exemplo, Azure Key Vault) sem armazenar credenciais no código. Uma vez habilitada, todas as permissões necessárias podem ser concedidas por meio do controle de acesso baseado em função do Azure. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida deste recurso. Além disso, cada recurso (por exemplo, máquina virtual) pode ter apenas uma identidade gerenciada atribuída pelo sistema.

Na [portal do Azure](https://portal.azure.com/), pesquise por _aplicativo de funções_ na barra de pesquisa com o nome do aplicativo de funções que você criou anteriormente. Selecione o *aplicativo de funções* na lista. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Portal do Azure: Pesquisar aplicativo de função":::

Na janela do aplicativo de funções, selecione _identidade_ na barra de navegação à esquerda para habilitar a identidade gerenciada.
Na guia _atribuído pelo sistema_ , alterne o _status_ para ativado e _salve_ -o. Você verá um pop-up para _habilitar a identidade gerenciada atribuída pelo sistema_.
Selecione o botão _Sim_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Portal do Azure: habilitar identidade gerenciada pelo sistema":::

Você pode verificar nas notificações de que sua função foi registrada com êxito com Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Portal do Azure: notificações":::

Observe também a **ID de objeto** mostrada na página _identidade_ , pois ela será usada na próxima seção.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Copiar a ID de objeto a ser usada no futuro":::

### <a name="assign-access-roles-using-azure-portal"></a>Atribuir funções de acesso usando portal do Azure

Selecione o botão _atribuições de função do Azure_ , que abrirá a página *atribuições de função do Azure* . Em seguida, selecione _+ Adicionar atribuição de função (versão prévia)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Portal do Azure: Adicionar atribuição de função":::

Na página _Adicionar atribuição de função (versão prévia)_ que é aberta, selecione:

* _Escopo_: grupo de recursos
* _Assinatura_: selecione sua assinatura do Azure
* _Grupo de recursos_: selecione o grupo de recursos na lista suspensa
* _Função_: selecione _Azure digital gêmeos proprietário dos dados_ no menu suspenso

Em seguida, Salve seus detalhes pressionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Portal do Azure: Adicionar atribuição de função (visualização) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Definir configurações de aplicativo usando portal do Azure

Você pode tornar a URL da sua instância do gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre isso, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). As configurações do aplicativo são expostas como variáveis de ambiente para acessar a instância digital gêmeos. 

Você precisará de ADT_INSTANCE_URL para criar uma configuração de aplicativo.

Você pode obter ADT_INSTANCE_URL acrescentando **_https://_** ao nome do host da instância. No portal do Azure, você pode encontrar o nome do host da instância gêmeos digital pesquisando sua instância na barra de pesquisa. Em seguida, selecione _visão geral_ na barra de navegação à esquerda para exibir o _nome do host_. Copie esse valor para criar uma configuração de aplicativo.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Portal do Azure: visão geral – > copiar nome do host para usar no campo _Value_.":::

Agora você pode criar uma configuração de aplicativo seguindo as etapas abaixo:

* Pesquise seu aplicativo usando o nome do aplicativo de funções na barra de pesquisa e selecione o aplicativo de funções na lista
* Selecione _configuração_ na barra de navegação à esquerda para criar uma nova configuração de aplicativo
* Na guia _configurações do aplicativo_ , selecione _+ nova configuração de aplicativo_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Portal do Azure: Pesquisar um aplicativo de funções existente":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Portal do Azure: definir configurações de aplicativo":::

Na janela que é aberta, use o valor copiado acima para criar uma configuração de aplicativo. \
_Nome_  : ADT_SERVICE_URL \
_Valor_ : https://{Your-Azure-digital-gêmeos-nome_do_host}

Selecione _OK_ para criar uma configuração de aplicativo.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Portal do Azure: adicionar configurações de aplicativo.":::

Você pode exibir as configurações do aplicativo com o nome do aplicativo no campo _nome_ . Em seguida, salve as configurações do aplicativo selecionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Portal do Azure: exibir o aplicativo criado e reiniciar o aplicativo":::

As alterações nas configurações do aplicativo precisam de uma reinicialização do aplicativo. Selecione _continuar_ para reiniciar o aplicativo.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Portal do Azure: salvar configurações do aplicativo":::

Você pode exibir que as configurações do aplicativo são atualizadas selecionando o ícone de _notificações_ . Se a configuração do aplicativo não for criada, você poderá repetir a adição de uma configuração de aplicativo seguindo o processo acima.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Portal do Azure: notificações para atualizar as configurações do aplicativo":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você seguiu as etapas para configurar um aplicativo de funções no Azure para uso com o gêmeos digital do Azure. Em seguida, você pode assinar sua função à grade de eventos para escutar em um ponto de extremidade. Esse ponto de extremidade pode ser:
* Um ponto de extremidade de grade de eventos anexado ao Azure digital gêmeos para processar mensagens provenientes do Azure digital gêmeos em si (como mensagens de alteração de propriedade, mensagens de telemetria geradas por [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento ou mensagens de ciclo de vida)
* Os tópicos do sistema IoT usados pelo Hub IoT para enviar telemetria e outros eventos de dispositivo
* Um ponto de extremidade de grade de eventos que recebe mensagens de outros serviços

Em seguida, consulte como criar sua função básica para ingerir dados do Hub IoT no gêmeos digital do Azure:
* [*Como: telemetria de ingestão do Hub IoT*](how-to-ingest-iot-hub-data.md)
