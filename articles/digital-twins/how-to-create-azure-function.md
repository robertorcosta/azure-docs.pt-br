---
title: Configurar uma função do Azure para processar dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função do Azure que pode acessar e ser disparada por gêmeos digital.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acbccd09fc8bc765083fea7b3a8d6ad749003ae9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428009"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Conectar Azure Functions aplicativos para processamento de dados

A atualização do gêmeos digital com base em dados é tratada usando [**rotas de eventos**](concepts-route-events.md) por meio de recursos de computação, como [Azure Functions](../azure-functions/functions-overview.md). Uma função do Azure pode ser usada para atualizar uma atualização digital em resposta a:
* dados de telemetria do dispositivo provenientes do Hub IoT
* alteração de propriedade ou outros dados provenientes de outra troca digital no grafo de entrelaçamento

Este artigo orienta você pela criação de uma função do Azure para uso com o gêmeos digital do Azure. 

Aqui está uma visão geral das etapas que ela contém:

1. Criar um aplicativo Azure Functions no Visual Studio
2. Escrever uma função do Azure com um gatilho de [grade de eventos](../event-grid/overview.md)
3. Adicione o código de autenticação à função (para poder acessar o Azure digital gêmeos)
4. Publicar o aplicativo de funções no Azure
5. Configurar o acesso de [segurança](concepts-security.md) para o aplicativo de funções do Azure

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Criar um aplicativo Azure Functions no Visual Studio

No Visual Studio 2019, selecione _arquivo > novo projeto de >_ e procure o modelo de _Azure Functions_ , selecione _Avançar_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Especifique um nome para o aplicativo de funções e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Selecione o tipo de *gatilho de grade de eventos* do aplicativo de funções e selecione _criar_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Depois que seu aplicativo de funções for criado, seu Visual Studio terá o exemplo de código preenchido automaticamente no arquivo **Function.cs** na pasta do projeto. Essa função curta do Azure é usada para registrar eventos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Escrever uma função do Azure com um gatilho de grade de eventos

Você pode escrever uma função do Azure adicionando o SDK ao seu aplicativo de funções. O aplicativo de funções interage com o gêmeos digital do Azure usando o [SDK do gêmeos digital do Azure para .net (C#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 

Para usar o SDK, você precisará incluir os pacotes a seguir em seu projeto. Você pode instalar os pacotes usando o Gerenciador de pacotes NuGet do Visual Studio ou adicionar os pacotes usando a `dotnet` ferramenta de linha de comando. Escolha um destes métodos: 

**Opção 1. Adicionar pacotes usando o Gerenciador de pacotes do Visual Studio:**
    
Você pode fazer isso selecionando o projeto com o botão direito do mouse e selecionando _gerenciar pacotes NuGet_ na lista. Em seguida, na janela que é aberta, selecione a guia _procurar_ e procure os pacotes a seguir. Selecione _instalar_ e _aceite_ o contrato de licença para instalar os pacotes.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Para que a configuração do pipeline do SDK do Azure seja configurada corretamente para Azure Functions, você também precisará dos pacotes a seguir. Repita o mesmo processo acima para instalar todos os pacotes.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opção 2. Adicionar pacotes usando `dotnet` a ferramenta de linha de comando:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Em seguida, no Gerenciador de Soluções do Visual Studio, abra o arquivo _Function.cs_ em que você tem um código de exemplo e adicione as instruções _using_ a seguir à sua função do Azure. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Adicionar código de autenticação à função do Azure

Agora, você declarará variáveis de nível de classe e adicionará o código de autenticação que permitirá que a função acesse o Azure digital gêmeos. Você adicionará o seguinte à sua função do Azure no arquivo {seu nome de função}. cs.

* Leia a URL do serviço ADT como uma variável de ambiente. É uma boa prática ler a URL do serviço de uma variável de ambiente, em vez de codificá-la embutidamente na função.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Uma variável estática para manter uma instância de HttpClient. O HttpClient é relativamente caro de criar e queremos evitar ter que fazer isso para cada invocação de função.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Você pode usar as credenciais de identidade gerenciadas no Azure function.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Adicione uma variável local _DigitalTwinsClient_ dentro de sua função para manter a instância do cliente gêmeos do Azure digital para o projeto de função. Não *torne essa* variável estática dentro de sua classe.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Adicione uma verificação nula para _adtInstanceUrl_ e empacote sua lógica de função em um bloco try catch para capturar quaisquer exceções.

Após essas alterações, o código de função será semelhante ao seguinte:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Publicar o aplicativo de funções no Azure

Para publicar o aplicativo de funções no Azure, selecione o projeto de função (não a solução) em Gerenciador de Soluções e escolha **publicar**.

> [!IMPORTANT] 
> A publicação de uma função do Azure incorrerá em encargos adicionais em sua assinatura, independentemente do Azure digital gêmeos.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Selecione **Azure** como o destino de publicação e selecione **Avançar**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Na página a seguir, insira o nome desejado para o novo aplicativo de funções, um grupo de recursos e outros detalhes.
Para que seu aplicativo de funções possa acessar o Azure digital gêmeos, ele precisa ter uma identidade gerenciada pelo sistema e ter permissões para acessar sua instância do gêmeos digital do Azure.

Em seguida, você pode configurar o acesso de segurança para a função usando a CLI ou portal do Azure. Escolha um destes métodos:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Configurar o acesso de segurança para o aplicativo de funções do Azure
Você pode configurar o acesso de segurança para o aplicativo de funções do Azure usando uma destas opções:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Opção 1: configurar o acesso de segurança para o aplicativo de funções do Azure usando a CLI

O esqueleto do Azure function dos exemplos anteriores requer que um token de portador seja passado para ele, a fim de ser capaz de autenticar com o gêmeos digital do Azure. Para garantir que esse token de portador seja passado, você precisará configurar [identidade de serviço gerenciada (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo de funções. Isso só precisa ser feito uma vez para cada aplicativo de funções.

Você pode criar uma identidade gerenciada pelo sistema e atribuir a identidade do aplicativo de funções à função de _proprietário do gêmeos digital do Azure (versão prévia)_ para sua instância do gêmeos digital do Azure. Isso dará à permissão do aplicativo de funções na instância para executar atividades de plano de dados. Em seguida, torne a URL da instância do Azure digital gêmeos acessível para sua função definindo uma variável de ambiente.

 Use [Azure cloud Shell](https://shell.azure.com) para executar os comandos.

Use o comando a seguir para criar a identidade gerenciada pelo sistema. Anote o campo _principalId_ na saída.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Use o valor _PrincipalId_ no comando a seguir para atribuir a identidade do aplicativo de funções à função de _proprietário do gêmeos digital do Azure (versão prévia)_ para sua instância do gêmeos digital do Azure.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Por fim, você pode tornar a URL da sua instância de gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre como definir variáveis de ambiente, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> A URL da instância do gêmeos digital do Azure é feita adicionando *https://* ao início do *nome de host*da instância do Azure digital gêmeos. Para ver o nome do host, juntamente com todas as propriedades de sua instância, você pode executar `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Opção 2: configurar o acesso de segurança para o aplicativo de funções do Azure usando portal do Azure

Uma identidade gerenciada atribuída pelo sistema permite que os recursos do Azure se autentiquem nos serviços de nuvem (por exemplo, Azure Key Vault) sem armazenar credenciais no código. Uma vez habilitada, todas as permissões necessárias podem ser concedidas por meio do controle de acesso baseado em função do Azure. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida deste recurso. Além disso, cada recurso (por exemplo, máquina virtual) pode ter apenas uma identidade gerenciada atribuída pelo sistema.

Na [portal do Azure](https://portal.azure.com/), pesquise por _aplicativo de funções_ na barra de pesquisa com o nome do aplicativo de funções que você criou anteriormente. Selecione o *aplicativo de funções* na lista. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Na janela do aplicativo de funções, selecione _identidade_ na barra de navegação à esquerda para habilitar a identidade gerenciada.
Na guia _atribuído pelo sistema_ , alterne o _status_ para ativado e _salve_ -o. Você verá um pop-up para _habilitar a identidade gerenciada atribuída pelo sistema_.
Selecione o botão _Sim_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Você pode verificar nas notificações de que sua função foi registrada com êxito com Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Observe também a **ID de objeto** mostrada na página _identidade_ , pois ela será usada na próxima seção.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

### <a name="assign-access-roles-using-azure-portal"></a>Atribuir funções de acesso usando portal do Azure

Selecione o botão _atribuições de função do Azure_ , que abrirá a página *atribuições de função do Azure* . Em seguida, selecione _+ Adicionar atribuição de função (versão prévia)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Na página _Adicionar atribuição de função (versão prévia)_ que é aberta, selecione:

* _Escopo_: grupo de recursos
* _Assinatura_: selecione sua assinatura do Azure
* _Grupo de recursos_: selecione o grupo de recursos na lista suspensa
* _Função_: selecione _proprietário do gêmeos digital do Azure (versão prévia)_ na lista suspensa

Em seguida, Salve seus detalhes pressionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

### <a name="configure-application-settings-using-azure-portal"></a>Definir configurações de aplicativo usando portal do Azure

Você pode tornar a URL da sua instância do gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre isso, consulte [*variáveis de ambiente*](/sandbox/functions-recipes/environment-variables). As configurações do aplicativo são expostas como variáveis de ambiente para acessar a instância digital gêmeos. 

Você precisará de ADT_INSTANCE_URL para criar uma configuração de aplicativo.

Você pode obter ADT_INSTANCE_URL acrescentando **_https://_** ao nome do host da instância. No portal do Azure, você pode encontrar o nome do host da instância gêmeos digital pesquisando sua instância na barra de pesquisa. Em seguida, selecione _visão geral_ na barra de navegação à esquerda para exibir o _nome do host_. Copie esse valor para criar uma configuração de aplicativo.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Agora você pode criar uma configuração de aplicativo seguindo as etapas abaixo:

* Pesquise sua função do Azure usando o nome da função na barra de pesquisa e selecione a função na lista
* Selecione _configuração_ na barra de navegação à esquerda para criar uma nova configuração de aplicativo
* Na guia _configurações do aplicativo_ , selecione _+ nova configuração de aplicativo_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Na janela que é aberta, use o valor copiado acima para criar uma configuração de aplicativo. \
_Nome_  : ADT_SERVICE_URL \
_Valor_ : https://{Your-Azure-digital-gêmeos-nome_do_host}

Selecione _OK_ para criar uma configuração de aplicativo.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Você pode exibir as configurações do aplicativo com o nome do aplicativo no campo _nome_ . Em seguida, salve as configurações do aplicativo selecionando o botão _salvar_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

As alterações nas configurações do aplicativo precisam de uma reinicialização do aplicativo. Selecione _continuar_ para reiniciar o aplicativo.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Você pode exibir que as configurações do aplicativo são atualizadas selecionando o ícone de _notificações_ . Se a configuração do aplicativo não for criada, você poderá repetir a adição de uma configuração de aplicativo seguindo o processo acima.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você seguiu as etapas para configurar uma função do Azure para uso com o gêmeos digital do Azure. Em seguida, você pode assinar sua função do Azure para a grade de eventos, para escutar em um ponto de extremidade. Esse ponto de extremidade pode ser:
* Um ponto de extremidade de grade de eventos anexado ao Azure digital gêmeos para processar mensagens provenientes do Azure digital gêmeos em si (como mensagens de alteração de propriedade, mensagens de telemetria geradas por [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento ou mensagens de ciclo de vida)
* Os tópicos do sistema IoT usados pelo Hub IoT para enviar telemetria e outros eventos de dispositivo
* Um ponto de extremidade de grade de eventos que recebe mensagens de outros serviços

Em seguida, consulte como criar em sua função básica do Azure para ingerir dados do Hub IoT no gêmeos digital do Azure:
* [*Como: telemetria de ingestão do Hub IoT*](how-to-ingest-iot-hub-data.md)