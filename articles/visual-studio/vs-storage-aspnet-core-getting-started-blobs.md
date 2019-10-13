---
title: Introdução ao armazenamento de BLOBs do Azure usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento de blobs do Azure em um projeto ASP.NET Core no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 91e95dcf7cfc46073e2fd752a0e7e5ae6fce4ee8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300070"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

O Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos ou blobs. O Armazenamento de Blobs pode conter qualquer tipo de texto ou de dados binários, como um documento, um arquivo de mídia ou um instalador de aplicativo. O Armazenamento de Blobs também é chamado de armazenamento de objeto. Para saber mais sobre Armazenamento de Blobs, consulte [Introdução ao Armazenamento de Blobs](../storage/blobs/storage-blobs-introduction.md).

Esse tutorial exibe como gravar o código ASP.NET Core para alguns cenários comuns que usam o Armazenamento de Blobs do Azure. Entre os cenários estão a criação de um contêiner de blobs, bem como carregamento, listagem, download e exclusão de blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta seção orienta a configuração do ambiente de desenvolvimento. Ela inclui a criação de um aplicativo ASP.NET MVC (Model-View-Controller), a adição de uma conexão de serviços conectados, a adição de um controlador e a especificação das diretivas de namespace necessárias.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um projeto de aplicativo ASP.NET MVC

1. Abra o Visual Studio.

1. No menu principal, selecione **Arquivo** > **Novo** > **Projeto**.

1. Na caixa de diálogo **Novo Projeto**, selecione **Web** > **Aplicativo Web ASP.NET** > **AspNetCoreStorage**. Depois, selecione **OK**.

    ![Captura de tela da caixa de diálogo Novo Projeto no Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Na caixa de diálogo **Novo Aplicativo Web do ASP.NET Core**, selecione **.NET Core** > **ASP.NET Core 2.0** > **Aplicativo Web (Model-View-Controller)** . Depois, selecione **OK**.

    ![Captura de tela da caixa de diálogo Novo Aplicativo Web ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usar serviços conectados para se conectar a uma conta de armazenamento do Azure

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.

2. No menu de contexto, selecione **Adicionar** > **Serviços Conectados**.

1. Na caixa de diálogo **Serviços Conectados**, selecione **Armazenamento na Nuvem com Armazenamento do Microsoft Azure** e, a seguir, selecione **Configurar**.

    ![Tela de captura da caixa de diálogo Serviços Conectados](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Na caixa de diálogo **Armazenamento do Microsoft Azure**, selecione a conta do Armazenamento do Azure a ser usada para este tutorial. Para criar uma nova conta do Armazenamento do Azure, selecione **Criar uma Nova Conta de Armazenamento** e preencha o formulário. Depois de selecionar uma conta de armazenamento existente ou criar uma nova, selecione **Adicionar**. O Visual Studio instala o pacote NuGet para o Armazenamento do Azure, e uma cadeia de conexão de armazenamento para **appSettings.json**.

> [!TIP]
> Para saber como criar uma conta de armazenamento com o [portal do Azure](https://portal.azure.com), consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
>
> Você também pode criar uma conta de armazenamento usando o [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), a [CLI do Azure](../storage/common/storage-azure-cli.md) ou o [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores**.

2. No menu de contexto, selecione **Adicionar** > **Controlador**.

    ![Captura de tela do Gerenciador de Soluções](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC – Vazio** e selecione **Adicionar**.

    ![Captura de tela da caixa de diálogo Adicionar Scaffold](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Na caixa de diálogo **Adicionar Controlador MVC Vazio**, nomeie o controlador *BlobsController* e selecione **Adicionar**.

    ![Captura de tela da caixa de diálogo Adicionar Controlador MVC Vazio](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adicione as seguintes diretivas `using` ao arquivo `BlobsController.cs`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Conectar-se para uma conta de armazenamento e obter uma referência de contêiner

Um contêiner de blobs é uma hierarquia aninhada de blobs e as pastas. As outras etapas neste documento exigem uma referência a um contêiner de blobs, para que o código seja colocado o seu próprio método para reutilização.

As seguintes etapas criam um método para se conectar à conta de armazenamento usando a cadeia de conexão em **appSettings.json**. As etapas também criam uma referência a um contêiner. A configuração de cadeia de conexão em **appSettings.json** é nomeada com o formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **GetCloudBlobContainer** que retorna um **CloudBlobContainer**. Certifique-se de substituir `<storageaccountname>_AzureStorageConnectionString` pelo nome real da chave em **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Embora *test-blob-container* não exista ainda, esse código cria uma referência a ele. Isso é para que o contêiner possa ser criado com o método `CreateIfNotExists` mostrado na próxima etapa.

## <a name="create-a-blob-container"></a>Criar um contêiner de blob

As seguintes etapas ilustram como criar um contêiner de blobs:

1. Adicione um método chamado `CreateBlobContainer` que retorna um `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Obtenha um objeto `CloudBlobContainer` que representa uma referência ao nome do contêiner de blobs desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Chame o método `CloudBlobContainer.CreateIfNotExists` para criar o contêiner se ele ainda não existir. O método `CloudBlobContainer.CreateIfNotExists` retornará **true** se o contêiner não existir e se for criado com êxito. Caso contrário, o método retornará **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Atualize o `ViewBag` com o nome do contêiner de blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A seguir, é exibido o método `CreateBlobContainer` concluído:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modos de Exibição**.

2. No menu de contexto, selecione **Adicionar** > **Nova Pasta**. Nomeie a nova pasta *Blobs*. 

1. Em **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** e clique com o botão direito em **Blobs**.

4. No menu de contexto, selecione **Adicionar** > **Exibir**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **CreateBlobContainer** para o nome da exibição e selecione **Adicionar**.

1. Abra `CreateBlobContainer.cshtml` e modifique-o para que se pareça com o seguinte snippet de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** > **Compartilhadas** e abra `_Layout.cshtml`.

1. Procure pela lista não ordenada que tenha esta aparência: `<ul class="nav navbar-nav">`.  Após o último elemento `<li>` na lista, adicione o HTML a seguir para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Execute o aplicativo e selecione **Criar Contêiner de Blobs** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Captura de tela de Criar contêiner de blobs](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Conforme mencionado anteriormente, o método `CloudBlobContainer.CreateIfNotExists` retorna **true** apenas quando o contêiner não existir e for criado. Portanto, se o aplicativo for executado quando o contêiner existir, o método retornará **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Carregar um blob em um contêiner de blobs

Quando o [contêiner de blobs for criado](#create-a-blob-container), carregue os arquivos nele. Esta seção explica como carregar um arquivo local em um contêiner de blobs. Estas etapas presumem que existe um contêiner de blobs chamado *test-blob-container*. 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado `UploadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. No método `UploadBlob`, obtenha um objeto `CloudBlobContainer` que representa uma referência ao nome do contêiner de blobs desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. O Armazenamento do Azure oferece suporte a tipos diferentes de blob. Este tutorial usa blobs de bloco. Para recuperar uma referência a um blob de blocos, chame o método `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > O nome do blob é parte da URL usada para recuperar um blob e pode ser qualquer cadeia de caracteres, incluindo o nome do arquivo.

1. Quando houver uma referência de blob, carregue qualquer fluxo de dados nele chamando o método `UploadFromStream` do objeto de referência do blob. O método `UploadFromStream` criará o blob, se ele não existir, ou o substituirá, se ele já existir. (Mude *&lt;file-to-upload>* para um caminho totalmente qualificado para um arquivo a ser carregado.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    A seguir, o método `UploadBlob` concluído é exibido (com um caminho totalmente qualificado para o arquivo a ser carregado):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** > **Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último elemento `<li>` na lista, adicione o HTML a seguir para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Execute o aplicativo e selecione **Carregar blob**. A palavra *êxito!* deve aparecer.
    
    ![Captura de tela de verificação bem-sucedida](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Listar os blobs em um contêiner de blobs

Esta seção ilustra como listar os blobs em um contêiner de blobs. O código de exemplo faz referência ao *test-blob-container* criado na seção, [Criar um contêiner de blobs](#create-a-blob-container).

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado `ListBlobs` que retorna um `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. No método `ListBlobs`, obtenha um objeto `CloudBlobContainer` que representa uma referência ao contêiner de blobs. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Para listar os blobs em um contêiner de blobs, use o método `CloudBlobContainer.ListBlobsSegmentedAsync`. O método `CloudBlobContainer.ListBlobsSegmentedAsync` retorna um `BlobResultSegment`. Ele que contém objetos `IListBlobItem` que podem ser convertidos em objetos `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. O snippet de código a seguir enumera todos os blobs em um contêiner de blobs. Cada blob é convertido para o objeto apropriado, com base em seu tipo. Seu nome (ou URI no caso de um `CloudBlobDirectory`) é adicionado a uma lista.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    A seguir, é exibido o método `ListBlobs` concluído:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Em **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** e clique com o botão direito em **Blobs**.

2. No menu de contexto, selecione **Adicionar** > **Exibir**.

1. Na caixa de diálogo **Adicionar Exibição**, insira `ListBlobs` para o nome da exibição e selecione **Adicionar**.

1. Abra `ListBlobs.cshtml` e substitua o conteúdo pelo código a seguir:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** > **Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último elemento `<li>` na lista, adicione o HTML a seguir para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Execute o aplicativo e selecione **Listar blobs** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Captura de tela da lista de blobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Baixar blobs

Esta seção ilustra como baixar um blob. Você pode mantê-lo no armazenamento local ou ler o conteúdo em uma cadeia de caracteres. O código de exemplo faz referência ao *test-blob-container* criado na seção, [Criar um contêiner de blobs](#create-a-blob-container).

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado `DownloadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. No método `DownloadBlob`, obtenha um objeto `CloudBlobContainer` que representa uma referência ao contêiner de blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob ao chamar o método `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para baixar um blob, use o método `CloudBlockBlob.DownloadToStream`. O código a seguir transfere o conteúdo do blob para um objeto de fluxo. Em seguida, esse objeto é mantido em um arquivo local. (Mude *&lt;local-file-name>* para o nome de arquivo totalmente qualificado que representa onde você deseja baixar o blob). 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    A seguir, o método `ListBlobs` concluído é exibido (com um caminho totalmente qualificado para o arquivo que está sendo criado):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** > **Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último elemento `<li>` na lista, adicione o HTML a seguir para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Execute o aplicativo e selecione **Baixar blob** para baixar o blob. O blob especificado na chamada do método `CloudBlobContainer.GetBlockBlobReference` é baixado no local especificado na chamada do método `File.OpenWrite`. O texto *êxito!* deve aparecer no navegador. 

## <a name="delete-blobs"></a>Excluir blobs

As seguintes etapas ilustram como excluir um blob:

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado `DeleteBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. No método `DeleteBlob`, obtenha um objeto `CloudBlobContainer` que representa uma referência ao contêiner de blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob ao chamar o método `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para excluir um blob, use o método `Delete`.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    O método `DeleteBlob` concluído deve aparecer da seguinte maneira:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Modos de Exibição** > **Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último elemento `<li>` na lista, adicione o HTML a seguir para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Execute o aplicativo e selecione **Excluir blob** para excluir o blob especificado na chamada do método `CloudBlobContainer.GetBlockBlobReference`. O texto *êxito!* deve aparecer no navegador. Selecione o botão **Voltar** do navegador e, em seguida, selecione **Listar blobs** para verificar se o blob não está mais no contêiner.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a armazenar, listar e recuperar blobs no Armazenamento do Azure usando ASP.NET Core. Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento na Tabela do Azure e serviços conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introdução ao armazenamento na Fila do Azure e serviços conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
