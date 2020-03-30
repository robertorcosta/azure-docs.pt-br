---
title: Configurar o Cofre de Chaves do Azure com a rotação de chaves e auditoria de ponta a ponta | Microsoft Docs
description: Use este guia de como fazer para ajudá-lo a configurar a rotação de chaves e monitorar os registros do cofre das chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6962a264787bd8a55b6f6a2ebdb6eeb615c33d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218410"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Azure Key Vault com a rotação de chaves e auditoria

## <a name="introduction"></a>Introdução

Depois de ter um cofre de chaves, você pode começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam manter suas chaves ou segredos, mas podem solicitá-los do cofre conforme necessário. Um cofre de chaves permite que você atualize chaves e segredos sem afetar o comportamento do seu aplicativo, o que abre uma amplitude de possibilidades para o seu gerenciamento de chaves e segredos.

Este artigo mostra como implementar uma rotação programada de chaves de conta de armazenamento, monitorar os registros de auditoria do cofre chave e levantar alertas quando solicitações inesperadas são feitas. 

Você deve primeiro criar um cofre de chaves usando o método de sua escolha:

- [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](quick-create-cli.md)
- [Definir e recuperar um segredo do Azure Key Vault usando o Azure PowerShell](quick-create-powershell.md)
- [Definir e recuperar um segredo do Azure Key Vault usando o portal Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Armazene um segredo

Para permitir que um aplicativo recupere um segredo do Cofre de Chaves, primeiro crie o segredo e carregue-o no cofre.

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, digite o nome de usuário e a senha da sua conta do Azure. O PowerShell obterá todas as assinaturas que estão associadas a essa conta. O PowerShell usa a primeira por padrão.

Se tiver várias assinaturas, você terá que especificar a que foi usada para criar o cofre de chaves. Digite o seguinte para ver as assinaturas de sua conta:

```powershell
Get-AzSubscription
```

Para especificar a assinatura associada ao cofre de chaves que você estará registrando, digite:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra como armazenar uma chave de conta de armazenamento como um segredo, você deve obter essa chave de conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar seu segredo (neste caso, a chave da sua conta de armazenamento), você deve converter essa chave em uma seqüência segura e, em seguida, criar um segredo com esse valor no cofre da chave.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, obtenha o URI do segredo que você criou. Você precisará deste URI em uma etapa posterior para chamar o cofre de chaves e recuperar seu segredo. Execute o seguinte comando PowerShell e anote o valor do ID, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar o aplicativo

Agora que você tem um segredo armazenado, você pode usar o código para recuperá-lo e usá-lo depois de executar mais alguns passos.

Primeiro, você deve registrar sua inscrição no Azure Active Directory. Em seguida, informe as informações do aplicativo do Key Vault para permitir solicitações do seu aplicativo.

> [!NOTE]
> O aplicativo deve ser criado no mesmo locatário do Azure Active Directory que o cofre de chaves.

1. Abrir **diretório ativo do Azure**.
2. Selecione **inscrições do Aplicativo**. 
3. Selecione **Novo registro de aplicativo** para adicionar um aplicativo ao Azure Active Directory.

    ![Abrir os aplicativos no Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Em **Criar,** deixe o tipo de aplicativo como **aplicativo web /API** e dê um nome ao seu aplicativo. Dê ao seu aplicativo uma **URL de login**. Esta URL pode ser o que você quiser para esta demonstração.

    ![Criar registro de aplicativo](./media/keyvault-keyrotation/create-app.png)

5. Depois que o aplicativo é adicionado ao Azure Active Directory, a página do aplicativo é aberta. Selecione **Configurações**e selecione **Propriedades**. Copie o valor de **ID do Aplicativo**. Você vai precisar dele em etapas posteriores.

Em seguida, gere uma chave para o seu aplicativo para que ele possa interagir com o Azure Active Directory. Para criar uma chave, selecione **Chaves** em **Configurações**. Anote a chave recém-gerada para o aplicativo Azure Active Directory. Ela será necessária em uma etapa posterior. A chave não estará disponível depois que você sair desta seção. 

![Chaves do aplicativo do Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Antes de estabelecer quaisquer chamadas de sua aplicação no cofre principal, você deve informar o cofre da chave sobre seu aplicativo e suas permissões. O comando a seguir usa o nome do cofre e o ID do aplicativo Azure Active Directory para conceder o aplicativo **Obtenha** acesso ao seu cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a construir suas chamadas de aplicativo. Em seu aplicativo, você deve instalar os pacotes NuGet necessários para interagir com o Azure Key Vault e o Azure Active Directory. No Visual Studio Package Manager Console, insira os comandos a seguir. Na redação deste artigo, a versão atual do pacote Azure Active Directory é 3.10.305231913, então confirme a versão mais recente e atualize conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação do Azure Active Directory. Neste exemplo, essa classe é chamada **de Utils**. Adicione a instrução `using` a seguir:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure Active Directory. Para manter a manutenção, você pode querer mover os valores de string codificados em sua configuração web ou aplicativo.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adicione o código necessário para chamar o Cofre de Chaves e recuperar o valor do segredo. Primeiro, você deve `using` adicionar a seguinte declaração:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de Chaves e recuperar o segredo. Nesse método, você fornece o URI de segredo que salvou na etapa anterior. Observe o uso do método **GetToken** da classe **Utils** que você criou anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deverá se autenticar no Azure Active Directory e recuperar o valor do segredo do Cofre de Chaves do Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves usando a Automação do Azure

> [!IMPORTANT]
> Os runbooks da Azure Automation `AzureRM` ainda exigem o uso do módulo.

Agora você está pronto para configurar uma estratégia de rotação para os valores que você armazena como segredos do Key Vault. Segredos podem ser girados de várias maneiras:

- Como parte de um processo manual
- Programáticamente usando chamadas de API
- Através de um script de Automação Azure

Para efeitos deste artigo, você usará o PowerShell combinado com o Azure Automation para alterar a chave de acesso de uma conta de armazenamento Azure. Em seguida, você atualizará um segredo do cofre com essa nova chave.

Para permitir que o Azure Automation defina valores secretos no cofre principal, você deve obter o ID do cliente para a conexão chamada **AzureRunAsConnection**. Essa conexão foi criada quando você estabeleceu sua instância de Automação Do Azure. Para encontrar esse ID, selecione **Ativos** na instância de automação do Azure. A partir daí, selecione **Conexões**e selecione o diretor do serviço **AzureRunAsConnection.** Anote o valor do **ApplicationID.**

![ID do cliente da Automação do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **Ativos,** selecione **Módulos**. Selecione **Galeria**e, em seguida, pesquise e importe versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na elaboração deste artigo, somente os módulos mencionados anteriormente precisaram ser atualizados para o script a seguir. Se o seu trabalho de automação falhar, confirme que você importou todos os módulos necessários e suas dependências.

Depois de recuperar o ID do aplicativo para sua conexão Azure Automation, você deve dizer ao seu cofre-chave que este aplicativo tem permissão para atualizar segredos em seu cofre. Use o seguinte comando PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, **selecione Runbooks** em sua instância de automação do Azure e, em seguida, **selecione Adicionar Runbook**. Selecione **Criação rápida**. Nomeie seu runbook e selecione **PowerShell** como o tipo de runbook. Você pode adicionar uma descrição. Por fim, selecione **Criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o novo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, **selecione Test pane** para testar seu script. Depois que o script for executado sem erro, você pode selecionar **Publicar**e, em seguida, você pode aplicar um cronograma para o runbook no painel de configuração do runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre de Chaves

Ao configurar um cofre de chaves, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas para o cofre de chaves. Esses registros são armazenados em uma conta de armazenamento azure designada e podem ser retirados, monitorados e analisados. O cenário a seguir usa funções do Azure, aplicativos lógicos do Azure e logs de auditoria do cofre-chave para criar um pipeline que envia um e-mail quando um aplicativo que não corresponde ao ID do aplicativo web recupera segredos do cofre.

Primeiro, você deve habilitar o log no cofre de chaves. Use os seguintes comandos PowerShell. (Você pode ver os detalhes completos [neste artigo sobre o registro do cofre de chaves](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Depois que o registro é ativado, os logs de auditoria começam a ser armazenados na conta de armazenamento designada. Esses logs contêm eventos sobre como e quando os cofres de chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar as informações de log 10 minutos após a operação do cofre de chaves. Muitas vezes estará disponível mais cedo do que isso.

A próxima etapa é [criar uma fila do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Esta fila é onde os logs de auditoria do cofre-chave são empurrados. Quando as mensagens de registro de auditoria estão na fila, o aplicativo lógico as pega e age sobre elas. Crie uma instância de Barramento de Serviço com as seguintes etapas:

1. Crie um espaço de nome de Barramento de Serviço (se você já tiver um que deseja usar, pule para o passo 2).
2. Navegue até a ocorrência do Ônibus de Serviço no portal Azure e selecione o namespace que deseja criar na fila.
3. Selecione **Criar um barramento** > de serviço**de integração** > **corporativa**de recursos e, em seguida, digite os detalhes necessários.
4. Encontre as informações de conexão do Ponto de Serviço selecionando o namespace e, em seguida, selecionando **Informações de conexão**. Você vai precisar dessa informação para a próxima seção.

Em seguida, [crie uma função Do Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os principais registros do cofre dentro da conta de armazenamento e pegar novos eventos. Esta função será acionada em um cronograma.

Para criar um aplicativo de função Azure, selecione **Criar um recurso,** pesquise no mercado para **o Function App**e selecione **Criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar por hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para funções do Azure, consulte [Como dimensionar funções do Azure](../azure-functions/functions-scale.md).

Depois que o aplicativo de função Azure for criado, vá até ele e selecione o cenário **timer** e **C\# ** para o idioma. Em seguida, **selecione Criar essa função**.

![Folha de Início das Funções do Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia **Desenvolver** , substitua o código de run.csx pelo seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Altere as variáveis no código anterior para apontar para sua conta de armazenamento onde os registros do cofre chave estão gravados, para a instância do Ônibus de Serviço que você criou anteriormente e para o caminho específico para os logs de armazenamento do cofre-chave.

A função obtém o arquivo de log mais recente da conta de armazenamento em que os logs do cofre de chaves são gravados, capta os eventos mais recentes do arquivo e os envia por push para uma fila do Barramento de Serviço. 

Como um único arquivo pode ter vários eventos, você deve criar um arquivo sync.txt que a função também analisa para determinar o carimbo de data do último evento que foi pego. O uso deste arquivo garante que você não empurre o mesmo evento várias vezes. 

O arquivo sync.txt contém um carimbo de tempo para o evento encontrado no último encontro. Quando os registros são carregados, eles devem ser classificados com base em seus carimbos de tempo para garantir que eles sejam ordenados corretamente.

Para esta função, fazemos referência a algumas bibliotecas adicionais que não estão disponíveis fora da caixa em Funções Azure. Para incluir essas bibliotecas, precisamos de funções do Azure para puxá-las usando NuGet. Na caixa **Código,** selecione **Exibir arquivos**.

![Opção "Exibir arquivos"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado project.json com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Depois de selecionar **Salvar**, Funções do Azure baixarão os binários necessários.

Mude para a guia **Integrar** e atribua ao parâmetro do temporizador um nome significativo para ser usado na função. No código anterior, a função espera que o temporizador seja chamado *de myTimer*. Especifique uma [expressão CRON](../app-service/webjobs-create.md#CreateScheduledCRON) para `0 * * * * *`o temporizador da seguinte forma: . Esta expressão fará com que a função seja executada uma vez por minuto.

Na mesma guia **Integrar,** adicione uma entrada do **armazenamento tipo Azure Blob**. Esta entrada apontará para o arquivo sync.txt que contém o carimbo de hora do último evento examinado pela função. Esta entrada será acessada dentro da função usando o nome do parâmetro. No código anterior, a entrada de armazenamento Azure Blob espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento onde o arquivo sync.txt será localizado (pode ser a mesma ou uma conta de armazenamento diferente). No campo de caminho, forneça o caminho `{container-name}/path/to/sync.txt`para o arquivo no formato .

Adicione uma saída do tipo **armazenamento Azure Blob**. Esta saída apontará para o arquivo sync.txt que você definiu na entrada. Esta saída é usada pela função para escrever o carimbo de tempo do último evento examinado. O código anterior espera que esse parâmetro seja chamado de *outputBlob*.

A função está pronta. Volte para a guia **Desenvolver** e salve o código. Verifique a janela de saída para verificar quaisquer erros de compilação e corrija-os conforme necessário. Se o código for compilado, então o código deve agora estar verificando os registros do cofre chave a cada minuto e empurrando quaisquer novos eventos para a fila de Ônibus de Serviço definida. Você deve ver as informações de log gravadas na janela de log sempre que a função for disparada.

### <a name="azure-logic-app"></a>Aplicativo lógico do Azure

Em seguida, você deve criar um aplicativo lógico Do Azure que capta os eventos que a função está empurrando para a fila do Ônibus de Serviço, analisa o conteúdo e envia um e-mail com base em uma condição que está sendo compatível.

[Crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando **Criar um aplicativo** > de lógica**de integração** > **de recursos**.

Depois que o aplicativo lógico for criado, vá até ele e selecione **Editar**. No editor de aplicativos lógico, selecione **Linha de Ônibus de Serviço** e digite suas credenciais de Service Bus para conectá-la à fila.

![Barramento de Serviço de aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecione **Adicionar uma condição**. Na condição, mude para o editor avançado e digite o seguinte código. Substitua *APP_ID* pelo ID de aplicativo real do seu aplicativo web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão essencialmente retorna **falsa** se o *apêndice* do evento de entrada (que é o corpo da mensagem do Ônibus de Serviço) não for o *appid* do aplicativo.

Agora, crie uma ação **SE NÃO, NÃO FAÇA NADA.**

![Aplicativos azure logic escolhem ação](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365 - envie e-mail**. Preencha os campos para criar um e-mail para enviar quando a condição definida retornar **falsa**. Se você não tem o Office 365, procure alternativas para alcançar os mesmos resultados.

Agora você tem um pipeline de ponta a ponta que procura novos registros de auditoria do cofre de chaves uma vez por minuto. Ele empurra novos logs que encontra para uma fila de Ônibus de Serviço. O aplicativo lógico é disparado quando uma nova mensagem chega na fila. Se o *appid* dentro do evento não corresponder ao ID do aplicativo de chamada, ele envia um e-mail.
