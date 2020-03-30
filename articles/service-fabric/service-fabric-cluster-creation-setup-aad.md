---
title: Configurar o Azure Active Directory para autenticação de cliente
description: Saiba como configurar o Azure AD (Azure Active Directory) para autenticar clientes para clusters do Service Fabric.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193366"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

Para clusters em execução no Azure, o Azure AD (Azure Active Directory) é recomendado para proteger o acesso a pontos de extremidade de gerenciamento. Este artigo descreve como configurar o Azure AD para autenticar clientes para um cluster service fabric.

Neste artigo, o termo "aplicativo" será usado para se referir aos [aplicativos do Azure Active Directory,](../active-directory/develop/developer-glossary.md#client-application)não aos aplicativos service fabric; a distinção será feita quando necessário. O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos.

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos Azure AD para controlar o acesso ao cluster: um aplicativo web e um aplicativo nativo. Depois que os aplicativos forem criados, você atribuirá aos usuários funções somente leitura e admin.

> [!NOTE]
> No Linux, você deve completar as seguintes etapas antes de criar o cluster. No Windows, você também tem a opção de configurar a [autenticação Azure AD para um cluster existente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> É um [problema conhecido](https://github.com/microsoft/service-fabric/issues/399) que aplicativos e nós em clusters habilitados para Linux AAD não podem ser visualizados no Portal Azure.



## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

1. [Clone o repo](https://github.com/Azure-Samples/service-fabric-aad-helpers) para o seu computador.
2. [Certifique-se de ter todos os pré-requisitos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para os scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicativos do Azure AD e atribuir usuários a funções

Usaremos os scripts para criar dois aplicativos Azure AD para controlar o acesso ao cluster: um aplicativo web e um aplicativo nativo. Depois de criar aplicativos para representar seu cluster, você criará usuários para as [funções suportadas pelo Service Fabric](service-fabric-cluster-security-roles.md): read-only e administrador.

Execute `SetupApplications.ps1` e forneça a ID de locatário, o nome do cluster e a URL de resposta do aplicativo Web como parâmetros.  Especifique também nomes de usuário e senhas para os usuários. Por exemplo: 

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo, Azure Governamental, Azure China e Azure Alemanha), você também deve especificar o parâmetro `-Location`.

Você pode encontrar o *TenantId* executando o comando `Get-AzureSubscription` do PowerShell. A execução desse comando exibe a TenantId para cada assinatura.

O *ClusterName* é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Serve apenas para tornar mais fácil mapear artefatos do Azure AD para o cluster do Service Fabric com que estão sendo usados.

*WebApplicationReplyUrl* é o ponto de extremidade padrão retornado pelo Azure AD para os usuários depois que eles concluem a entrada. Defina este ponto final como o ponto final do Service Fabric Explorer para o seu cluster. Se você estiver criando aplicativos Azure AD para representar um cluster existente, certifique-se de que essa URL corresponda ao ponto final do cluster existente. Se você estiver criando aplicativos para um novo cluster, planeje o ponto final que seu cluster terá e certifique-se de não usar o ponto final de um cluster existente. Por padrão, o ponto final do Service Fabric Explorer é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal do Azure][azure-portal], você verá duas novas entradas:

   * *ClusterNome*\_
   * *ClusterName*\_Cliente Nome do Cluster

O script imprime o JSON exigido pelo modelo do Azure Resource Manager quando você cria seu cluster habilitado para [AAD,](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)por isso é uma boa ideia manter a janela PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ajuda da solução de problemas na configuração do Azure Active Directory
Configurar e usar o Azure AD - Microsoft Azure Active Directory, pode ser um desafio, portanto, aqui estão algumas dicas sobre o que você pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar no Azure AD no Service Fabric Explorer, o navegador retornará para a home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo do certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
O usuário não recebeu uma função no aplicativo de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você habilite a "Atribuição de usuário necessária para acessar o aplicativo" da mesma forma feita por `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A conexão com o PowerShell falha e exibe o seguinte erro: “as credenciais especificadas são inválidas”
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", ao entrar no AD do Azure, a conexão falhará com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é igual à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>O Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar entrar no Azure AD no Service Fabric Explorer, a página retorna uma falha: “AADSTS50011: a &lt;URL&gt; do endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo: &lt;GUID&gt;”.

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no Azure AD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. Mas a URL não está listada na lista **URL DE RESPOSTA** do aplicativo Azure AD.

#### <a name="solution"></a>Solução
Na página de registro do aplicativo Azure AD para o cluster, selecione **Autenticação**e, na seção **Redirecionar URIs,** adicione a URL do Service Fabric Explorer à lista. Salve sua alteração.

![URL de resposta de aplicativo web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Conectar-se ao cluster usando autenticação Azure AD via PowerShell dá um erro ao fazer login: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando você tenta se conectar a um cluster de malha de serviço usando o Azure AD via PowerShell, a página de login retorna uma falha: "AADSTS50011: A url de resposta especificada na solicitação não corresponde às urls de resposta configuradas para o aplicativo: &lt;guid&gt;."

#### <a name="reason"></a>Motivo
Semelhante ao problema anterior, o PowerShell tenta autenticar contra o Azure AD, que fornece uma URL redirecionada que não está listada na lista **urLs do** aplicativo Azure AD Reply.  

#### <a name="solution"></a>Solução
Use o mesmo processo do problema anterior, mas `urn:ietf:wg:oauth:2.0:oob`a URL deve ser definida como , um redirecionamento especial para autenticação de linha de comando.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para se conectar ao cluster do Service Fabric, use o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, consulte o cmdlet [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer ao aplicativo do cluster (Web). Caso contrário, o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor quando o Azure AD está habilitado?
FabricClient e FabricGateway realizam autenticação mútua. Durante a autenticação do Azure AD, a integração do Azure AD fornece uma identidade de cliente para o servidor, e o certificado de servidor é usado pelo cliente para verificar a identidade do servidor. Para saber mais sobre certificados do Service Fabric, confira [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Próximas etapas
Depois de configurar os aplicativos do Azure Active Directory e definir as funções para os usuários, [configure e implante um cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
