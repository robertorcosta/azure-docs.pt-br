---
title: Azure Windows Communication Foundation (WCF) Relé híbrido no local/aplicativo em nuvem (.NET) | Microsoft Docs
description: Saiba como expor um serviço WCF local a um aplicativo Web na nuvem usando a Retransmissão do Azure
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212938"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Expor um serviço WCF local a um aplicativo Web na nuvem usando a Retransmissão do Azure

Este artigo mostra como criar um aplicativo de nuvem híbrida com o Microsoft Azure e o Visual Studio. Você cria um aplicativo que usa vários recursos do Azure na nuvem. Este tutorial ajuda você a aprender:

* Criar ou adaptar um serviço Web existente para consumo por uma solução de Web.
* Como usar o serviço de relé da Azure Windows Communication Foundation (WCF) para compartilhar dados entre um aplicativo Azure e um serviço web hospedado em outros lugares.

Você executará as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instalar os pré-requisitos para este tutorial.
> * Examinar o cenário.
> * Criar um namespace.
> * Crie um servidor no local.
> * Crie um aplicativo ASP .NET.
> * Execute o aplicativo localmente.
> * Implante o aplicativo web no Azure.
> * Execute o aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Uma assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos deste tutorial usam o Visual Studio 2019.
* SDK do Azure para .NET. Instale-o a partir da [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Como a Retransmissão do Azure ajuda com soluções híbridas

As soluções de negócios são tipicamente compostas por uma combinação de código personalizado e funcionalidade existente. O código personalizado aborda os requisitos de negócios novos e exclusivos. Soluções e sistemas que já estão em vigor fornecem funcionalidade sustal.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de requisitos de escala e custos operacionais mais baixos. Ao fazer isso, eles descobrem que os ativos de serviço existentes que gostariam de usar como blocos de construção para suas soluções estão dentro do firewall corporativo e fora de fácil alcance pela solução em nuvem. Muitos serviços internos não são construídos ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

[O Azure Relay](https://azure.microsoft.com/services/service-bus/) leva os serviços web existentes do WCF e torna esses serviços seguramente acessíveis a soluções que estão fora do perímetro corporativo sem exigir mudanças intrusivas na infra-estrutura de rede corporativa. Esses serviços de retransmissão ainda estão hospedados dentro de seu ambiente existente, mas delegam a escuta de sessões de entrada e solicitações para o serviço de retransmissão hospedado na nuvem. A Retransmissão do Azure também protege esses serviços contra acesso não autorizado usando a autenticação [SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-sas.md).

## <a name="review-the-scenario"></a>Examinar o cenário

Neste tutorial, você criará um site ASP.NET que permitirá ver uma lista de produtos na página do inventário de produtos.

![Cenário][0]

O tutorial supõe que você tem informações sobre produtos em um sistema local existente e utiliza a Retransmissão do Azure para acessar esse sistema. Um serviço web que é executado em um simples aplicativo de console simula essa situação. Contém um conjunto de produtos na memória. Você pode executar este aplicativo de console em seu próprio computador e implantar a função web no Azure. Ao fazer isso, você verá como a função da Web em execução no data center do Azure chama para o seu computador. Essa chamada acontece mesmo que seu computador certamente esteja por trás de pelo menos um firewall e uma camada de tradução de endereço de rede (NAT).

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar a desenvolver aplicativos do Azure, baixe as ferramentas e configure seu ambiente de desenvolvimento:

1. Instale o Azure SDK para .NET da [página de downloads](https://azure.microsoft.com/downloads/) do SDK.
1. Na coluna **.NET,** escolha a versão do [Visual Studio](https://www.visualstudio.com) que você está usando. Este tutorial usa o Visual Studio 2019.
1. Quando solicitado para executar ou salvar o instalador, **selecione Executar**.
1. Na caixa de diálogo **Instalador da Plataforma Web,** **selecione Instalar** e continuar com a instalação.

Uma vez que a instalação esteja concluída, você tem tudo o necessário para começar a desenvolver o aplicativo. O SDK inclui ferramentas que permitem que você desenvolva facilmente aplicativos do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um namespace

O primeiro passo é criar um namespace e obter uma chave [SAS (SAS) de Assinatura de Acesso Compartilhado.](../service-bus-messaging/service-bus-sas.md) Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do serviço de retransmissão. Uma chave SAS é automaticamente gerada pelo sistema quando um namespace do serviço é criado. A combinação do namespace de serviço e a chave SAS fornece as credenciais para o Azure autenticar o acesso a um aplicativo.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor local

Primeiro, você criará um sistema de catálogo de produtos local simulado.  Este projeto é um aplicativo de console do Visual Studio e usa o [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas do Barramento de Serviço e as definições da configuração. <a name="create-the-project"></a>

1. Inicie o Microsoft Visual Studio como um administrador. Para fazer isso, clique com o botão direito do mouse no ícone de programa do Visual Studio e selecione **Executar como administrador**.
1. No Visual Studio, selecione **Criar um projeto**.
1. Em **Criar um novo projeto,** selecione Console App **(.NET Framework)** para C# e selecione **Next**.
1. Nomeie o projeto *ProductsServer* e selecione **Criar**.

   ![Configure seu novo projeto][11]

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto **ProductsServer** e selecione **Gerenciar pacotes NuGet**.
1. Selecione **Procurar** e, em seguida, pesquise e escolha **WindowsAzure.ServiceBus**. Selecione **Instalar** e aceite os termos de uso.

   ![Selecionar pacote do NuGet][13]

   Os assemblies do cliente necessários foram referenciados.

1. Adicione uma nova classe para seu contrato de produto. No **Solution Explorer,** clique com o botão direito do mouse no projeto **ProductsServer** e selecione **Adicionar** > **classe**.
1. Em **Nome,** digite o nome *ProductsContract.cs* e selecione **Adicionar**.

Faça as seguintes alterações de código na sua solução:

1. Em *ProductsContract.cs*, substitua a definição do namespace pelo código a seguir, que define o contrato do serviço.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. Em *Program.cs,* substitua a definição de namespace pelo seguinte código, que adiciona o serviço de perfil e o host para ele.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. No **Gerenciador de Soluções**, clique duas vezes em **App.config** para abrir o arquivo no editor do Visual Studio. Na parte inferior `<system.ServiceModel>` do elemento, `<system.ServiceModel>`mas ainda dentro, adicione o seguinte código XML. Certifique-se `yourServiceNamespace` de substituir com o nome `yourKey` do seu namespace e com a chave SAS que você recuperou anteriormente do portal:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > O erro `transportClientEndpointBehavior` causado por é apenas um aviso e não é um problema de bloqueio para este exemplo.

1. Ainda no *App.config* `<appSettings>` , no elemento, substitua o valor da seqüência de conexão pela seqüência de conexão que você obteve anteriormente do portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Selecione Ctrl+Shift+B ou selecione **Build** > **Build Solution** para construir o aplicativo e verifique a precisão do seu trabalho até agora.

## <a name="create-an-aspnet-application"></a>Criar um aplicativo ASP.NET

Nesta seção, você criará um aplicativo ASP.NET simples que exibe os dados recuperados do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projeto

1. Certifique-se de que o Visual Studio está funcionando como administrador.
1. No Visual Studio, selecione **Criar um projeto**.
1. Em **Criar um novo projeto,** selecione ASP.NET Aplicativo Web **(.NET Framework)** para C# e selecione **Next**.
1. Nomeie o projeto *ProductsPortal* e selecione **Criar**.
1. Em **Criar um novo ASP.NET Aplicativo web,** escolha **MVC** e selecione **Alterar** em **Autenticação**.

   ![Selecione Aplicativo Web ASP.NET][16]

1. Em **Autenticação de alteração,** escolha **Nenhuma autenticação** e selecione **OK**. Para este tutorial, você está implantando um aplicativo que não precisa de um usuário para fazer login.

    ![Especificar a autenticação][18]

1. De volta **criar um novo aplicativo web ASP.NET**, selecione **Criar** para criar o aplicativo MVC.
1. Configure os recursos do Azure para um novo aplicativo web. Siga os passos em [Publicar seu aplicativo web](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Em seguida, volte a este tutorial e continue até o próximo passo.
1. No **Solution Explorer,** clique com o botão direito do mouse **Em Modelos** e, em seguida, **selecione Adicionar** > **classe**.
1. Nomeie a classe *Product.cs,* em seguida, **selecione Adicionar**.

    ![Criar modelo de produto][17]

### <a name="modify-the-web-application"></a>Modificar o aplicativo web

1. No *arquivo Product.cs* no Visual Studio, substitua a definição de namespace existente pelo seguinte código:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. No **Solution Explorer,** expanda **controladores**e clique duas vezes **HomeController.cs** para abrir o arquivo no Visual Studio.
1. Em *HomeController.cs*, substitua a definição do namespace existente pelo código a seguir:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. No **Solution Explorer,** **expanda o Views** > **Shared**e clique duas vezes **em _Layout.cshtml** para abrir o arquivo no editor do Visual Studio.
1. Alterar todas as `My ASP.NET Application` ocorrências de produtos para *Northwind Traders*.
1. Remova `Home`os `About`links `Contact` e links. No exemplo a seguir, exclua o código destacado.

    ![Excluir os itens da lista gerada][41]

1. No **Solution Explorer,** **expanda o Views** > **Home**e clique duas vezes em **Index.cshtml** para abrir o arquivo no editor do Visual Studio. Substitua todo o conteúdo do arquivo pelo código a seguir:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Para verificar a precisão do seu trabalho até agora, você pode selecionar Ctrl+Shift+B para construir o projeto.

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo para verificar se ele funciona.

1. Verifique se o **PortalDeProdutos** é o projeto ativo. Clique com o botão direito do mouse no nome do projeto no **Solution Explorer** e selecione Set as **Startup Project**.
1. No Visual Studio, selecione F5.

Seu aplicativo deve aparecer em execução em um navegador.

![Aplicativo Web][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

A próxima etapa é vincular o servidor de produtos local com o aplicativo ASP.NET.

1. Se ainda não estiver aberto, no Visual Studio, abra o projeto **ProductsPortal** que você criou na seção [Criar um aplicativo ASP.NET.](#create-an-aspnet-application)
1. Semelhante à etapa na [seção Criar servidor no local,](#create-an-on-premises-server) adicione o pacote NuGet às referências do projeto. No **Solution Explorer,** clique com o botão direito do mouse no projeto **ProductsPortal** e selecione **Gerenciar pacotes NuGet**.
1. Procure *WindowsAzure.ServiceBus* e selecione o item **WindowsAzure.ServiceBus**. Em seguida, termine a instalação e feche esta caixa de diálogo.
1. No **Solution Explorer,** clique com o botão direito do mouse no projeto **ProductsPortal** e selecione **Adicionar** > **item existente**.
1. Navegue até o arquivo *ProductsContract.cs* do projeto de console **ServidorDeProdutos**. Destaque *ProductsContract.cs*. Selecione a seta para baixo ao lado **de Adicionar**e, em seguida, escolha **Adicionar como Link**.

   ![Adicionar como um link][24]

1. Agora abra o arquivo *HomeController.cs* no editor do Visual Studio e substitua a definição de namespace pelo código a seguir. Certifique-se `yourServiceNamespace` de substituir com o nome `yourKey` do seu nome de serviço e com a sua chave SAS. Este código permite que o cliente ligue para o serviço no local, devolvendo o resultado da chamada.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. No **Solution Explorer,** clique com o botão direito do mouse na solução **ProductsPortal.** Certifique-se de clicar com o botão direito do mouse na solução, não no projeto. Selecione **Adicionar** > **projeto existente**.
1. Navegue até o projeto **ServidorDeProdutos** e clique duas vezes no arquivo de solução *ServidorDeProdutos.csproj* para adicioná-lo.
1. **ProductsServer** deve estar sendo executado para exibir os dados no **ProductsPortal**. No **Solution Explorer,** clique com o botão direito do mouse na solução **ProductsPortal** e selecione **Propriedades** para exibir Páginas **de Propriedade**.
1. Selecione **Common Properties** > **Startup Project** e escolha Vários projetos de **inicialização**. Certifique-se de que **o ProductsServer** e **ProductsPortal** apareçam, nessa ordem, e que a **ação** para ambos seja **Iniciar**.

      ![Vários projetos de inicialização][25]

1. Selecione **Dependências** > **do projeto propriedades comuns** no lado esquerdo.
1. Para **Projetos,** escolha **ProductsPortal**. Verifique se **ServidorDeProdutos** está selecionado.

    ![Dependências do projeto][26]

1. Para **projetos,** escolha **ProductsServer**. Certifique-se de que **o ProductsPortal** não está selecionado e, em seguida, selecione **OK** para salvar suas alterações.

## <a name="run-the-project-locally"></a>Executar o projeto localmente

Para testar o aplicativo localmente, no Visual Studio selecione F5. O servidor local, **ProductsServer**, deve começar primeiro, então o aplicativo **ProductsPortal** deve começar em uma janela do navegador. Desta vez, você verá que o inventário de produtos lista dados recuperados do sistema local de serviço de produto.

![Aplicativo Web][10]

Selecione **Atualizar** na página **ProdutosPortal.** Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` do **ServidorDeProdutos** for chamado.

Feche ambas as aplicações antes de prosseguir para a próxima seção.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implantar o projeto ProductsPortal em um aplicativo Web do Azure

O próximo passo é republicar o front-end do aplicativo Azure Web **ProductsPortal:**

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto **ProductsPortal** e selecione **Publicar**. Na página **Publicar,** **selecione Publicar**.

   > [!NOTE]
   > Você verá uma mensagem de erro na janela do navegador quando o projeto Web **PortalDeProdutos** for iniciado automaticamente após a implantação. Isso é esperado e ocorre porque o aplicativo **ServidorDeProdutos** não está sendo executado ainda.
   >

1. Copie a URL do aplicativo web implantado. Você vai precisar da URL mais tarde. Você também pode obter essa URL na janela Atividade de **Serviço do Aplicativo Azure** no Visual Studio:

   ![URL do aplicativo implantado][9]

1. Feche a janela do navegador para interromper o aplicativo em execução.

<a name="set-productsportal-as-web-app"></a>Antes de executar o aplicativo na nuvem, você deve garantir que o **ProductsPortal** seja lançado de dentro do Visual Studio como um aplicativo web.

1. No Visual Studio, clique com o botão direito do mouse no projeto **ProductsPortal** e selecione **Propriedades**.
1. Selecione **Web**. Em **Iniciar ação,** escolha **Iniciar URL**. Digite a URL para o seu aplicativo `https://productsportal20190906122808.azurewebsites.net/`web previamente implantado, neste exemplo, .

    ![URL inicial][27]

1. Selecione **'Salvar o** > **arquivo'**
1. Selecione **Build** > **Rebuild Solution**.

## <a name="run-the-application"></a>Executar o aplicativo

Selecione F5 para construir e executar o aplicativo. O servidor local, que é o aplicativo de console **ProductsServer,** deve começar primeiro, então o aplicativo **ProductsPortal** deve começar em uma janela do navegador, como mostrado aqui:

   ![Executar o aplicativo Web no Azure][1]

O inventário do produto lista dados recuperados do sistema local de serviço de produtos e exibe esses dados no aplicativo web. Verifique a URL para saber se **PortalDeProdutos** está em execução na nuvem, como um aplicativo Web do Azure.

   > [!IMPORTANT]
   > O aplicativo de console **ServidorDeProdutos** deve estar em execução e ser capaz de fornecer dados para o aplicativo **PortalDeProdutos**. Se o navegador exibir um erro, aguarde mais alguns segundos para **que o ProductsServer** carregue e exiba a seguinte mensagem e, em seguida, atualize o navegador.
   >

No navegador, atualize a página **ProdutosPortal.** Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` do **ServidorDeProdutos** for chamado.

![Saída atualizada][38]

## <a name="next-steps"></a>Próximas etapas

Vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Expor um serviço WCF local para um cliente do WCF fora da sua rede](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
