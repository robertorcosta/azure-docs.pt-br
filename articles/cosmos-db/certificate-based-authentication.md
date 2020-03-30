---
title: Autenticação baseada em certificados com O Zure Cosmos DB e Active Directory
description: Saiba como configurar uma identidade Azure AD para autenticação baseada em certificados para acessar chaves do Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365772"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticação baseada em certificados para uma identidade Azure AD para acessar chaves de uma conta DB do Azure Cosmos

A autenticação baseada em certificado permite que o aplicativo cliente seja autenticado usando Azure Active Directory (AAD) com um certificado de cliente. É possível executar a autenticação baseada em certificado em um computador em que seja necessária uma identidade, como um computador local ou uma máquina virtual no Azure. Seu aplicativo pode então ler as teclas Azure Cosmos DB sem ter as chaves diretamente no aplicativo. Este artigo descreve como criar um aplicativo Azure AD de exemplo, configurá-lo para autenticação baseada em certificados, entrar no Azure usando a nova identidade do aplicativo e, em seguida, recuperar as chaves da sua conta do Azure Cosmos. Este artigo usa o Azure PowerShell para configurar as identidades e fornece um aplicativo de exemplo C# que autentica e acessa chaves da sua conta do Azure Cosmos.  

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

* Se você não tiver uma [assinatura do Azure,](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="register-an-app-in-azure-ad"></a>Registrar um aplicativo no Azure AD

Nesta etapa, você registrará um aplicativo web de exemplo em sua conta Azure AD. Este aplicativo é usado posteriormente para ler as chaves da sua conta Azure Cosmos DB. Use as seguintes etapas para registrar um aplicativo: 

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

1. Abra o painel **do Diretório Ativo** do Azure, vá para o painel de inscrições do **App** e selecione Novo **registro**. 

   ![Novo registro de inscrição no Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Preencha o **Formulário de Inscrição** com os seguintes detalhes:  

   * **Nome** – Forneça um nome para o seu aplicativo, pode ser qualquer nome como "sampleApp".
   * **Tipos de conta suportados** – Escolha **apenas Contas neste diretório organizacional (Diretório Padrão)** para permitir que os recursos em seu diretório atual acessem esse aplicativo. 
   * **URL redirecionamento** – Escolha o aplicativo do tipo **Web** e forneça uma URL onde seu aplicativo está hospedado, ele pode ser qualquer URL. Para este exemplo, você pode fornecer `https://sampleApp.com` uma URL de teste, como se estivesse tudo bem, mesmo que o aplicativo não exista.

   ![Registrando um aplicativo web de amostra](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selecione **Registrar** depois de preencher o formulário.

1. Depois que o aplicativo for registrado, anote o ID do **aplicativo (cliente)** e **o ID do objeto,** você usará esses detalhes nos próximos passos. 

   ![Obtenha os IDs de aplicação e objeto](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instale o módulo AzureAD

Nesta etapa, você instalará o módulo Azure AD PowerShell. Este módulo é necessário para obter o ID do aplicativo que você registrou na etapa anterior e associar um certificado auto-assinado a esse aplicativo. 

1. Abra o Windows PowerShell ISE com direitos de administrador. Se você ainda não tiver feito, instale o módulo AZ PowerShell e conecte-se à sua assinatura. Se você tiver várias assinaturas, você pode definir o contexto da assinatura atual como mostrado nos seguintes comandos:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalar e importar o módulo [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Inscreva-se em seu Azure AD

Faça login no seu Azure AD onde você registrou o aplicativo. Use o comando Connect-AzureAD para entrar em sua conta, digite suas credenciais de conta Do Azure na janela pop-up. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Abra outra instância do Windows PowerShell ISE e execute os seguintes comandos para criar um certificado auto-assinado e leia a chave associada ao certificado:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Crie a credencial baseada em certificados 

Em seguida, execute os seguintes comandos para obter o ID do objeto do seu aplicativo e criar a credencial baseada em certificado. Neste exemplo, definimos o certificado para expirar após um ano, você pode defini-lo para qualquer data de término necessária.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

O comando acima resulta na saída semelhante à captura de tela abaixo:

![Saída de criação de credenciais baseada em certificados](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configure sua conta do Azure Cosmos para usar a nova identidade

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

1. Navegue até sua conta do Azure Cosmos, abra a lâmina de **controle de acesso (IAM).**

1. Selecione **Adicionar** e **Adicionar atribuição de função**. Adicione o sampleApp que você criou na etapa anterior com a função **Contribuinte,** conforme mostrado na captura de tela a seguir:

   ![Configure a conta do Azure Cosmos para usar a nova identidade](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selecione **Salvar** depois de preencher o formulário

## <a name="register-your-certificate-with-azure-ad"></a>Registrar o certificado com o Azure AD

Você pode associar a credencial baseada em certificados com o aplicativo do cliente no Azure AD a partir do portal Azure. Para associar a credencial, você deve carregar o arquivo do certificado com as seguintes etapas:

No registro do aplicativo do Azure para o aplicativo cliente:

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

1. Abra o painel **do Diretório Ativo** do Azure, vá para o painel de inscrições do **App** e abra o aplicativo de exemplo que você criou na etapa anterior. 

1. Selecione **Certificados & segredos** e, em seguida, **Faça upload de certificado**. Navegue pelo arquivo de certificado que você criou na etapa anterior para carregar.

1. Selecione **Adicionar**. Depois que o certificado é carregado, a impressão digital, a data de início e os valores de expiração são exibidos.

## <a name="access-the-keys-from-powershell"></a>Acesse as chaves do PowerShell

Nesta etapa, você entrará no Azure usando o aplicativo e o certificado que criou e acessará as chaves da sua conta do Azure Cosmos. 

1. Inicialmente limpe as credenciais da conta do Azure que você usou para entrar em sua conta. Você pode limpar credenciais usando o seguinte comando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Em seguida, valide que você pode entrar no portal Do Zure usando as credenciais do aplicativo e acessar as chaves do Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

O comando anterior exibirá as chaves principais e secundárias da sua conta Do Azure Cosmos. Você pode visualizar o registro de atividades da sua conta do Azure Cosmos para validar se a solicitação de chaves get foi bem sucedida e o evento é iniciado pelo aplicativo "sampleApp".

![Validar a chamada de chaves de obter no Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Acesse as chaves de um aplicativo C# 

Você também pode validar este cenário acessando chaves de um aplicativo C#. O seguinte aplicativo de console C#, que pode acessar as chaves do Azure Cosmos DB usando o aplicativo registrado no Active Directory. Certifique-se de atualizar o inquilinoId, clientID, certName, nome do grupo de recursos, ID de assinatura, detalhes do nome da conta do Azure Cosmos antes de executar o código. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Este script produz as teclas-mestre primárias e secundárias, conforme mostrado na captura de tela a seguir:

![csharp saída de aplicação](./media/certificate-based-authentication/csharp-application-output.png)

Semelhante à seção anterior, você pode visualizar o registro de atividades da sua conta do Azure Cosmos para validar que o evento de solicitação de chaves get é iniciado pelo aplicativo "sampleApp". 


## <a name="next-steps"></a>Próximas etapas

* [Proteger chaves do Azure Cosmos usando o Azure Key Vault](access-secrets-from-keyvault.md)

* [Linha de base de segurança para Azure Cosmos DB](security-baseline.md)
