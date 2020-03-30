---
title: Criar, configurar clusters de pacotes de segurança corporativa - Azure
description: Saiba como criar e configurar clusters de pacotes de segurança corporativa no Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436022"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Crie e configure clusters de pacotes de segurança corporativa no Azure HDInsight

O Enterprise Security Package (ESP) para o Azure HDInsight oferece acesso à autenticação baseada no Active Directory, suporte a vários usuários e controle de acesso baseado em função para seus clusters Apache Hadoop no Azure. Os clusters HDInsight ESP permitem que organizações que aderem a políticas rígidas de segurança corporativa processem dados confidenciais com segurança.

Este guia mostra como criar um cluster Azure HDInsight habilitado para ESP. Ele também mostra como criar uma VM Windows IaaS na qual o Active Directory e o Domain Name System (DNS) estão habilitados. Use este guia para configurar os recursos necessários para permitir que os usuários locais entrem em um cluster HDInsight habilitado para ESP.

O servidor criado funcionará como um substituto para *o seu ambiente real* no local. Você o usará para as etapas de configuração e configuração. Mais tarde você repetirá os passos em seu próprio ambiente.

Este guia também ajudará você a criar um ambiente de identidade híbrida usando o sincronia de hash de senha com o Azure Active Directory (Azure AD). O guia complementa [o uso do ESP no HDInsight](apache-domain-joined-architecture.md).

Antes de usar esse processo em seu próprio ambiente:

* Configure o Active Directory e o DNS.
* Habilite o Azure AD.
* Sincronize as contas de usuário no local para o Azure AD.

![Diagrama de arquitetura Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Crie um ambiente local

Nesta seção, você usará um modelo de implantação do Azure Quickstart para criar novas VMs, configurar o DNS e adicionar uma nova floresta de Diretório Ativo.

1. Vá para o modelo de implantação Quickstart para [criar uma VM Azure com uma nova floresta de diretório ativo](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecione **Implantar para Azure**.
1. Entre em sua assinatura do Azure.
1. Na **Criar uma VM Azure com uma nova página aD Forest,** forneça as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Subscription|Selecione a assinatura onde deseja implantar os recursos.|
    |Resource group|Selecione **Criar novo**e digite o nome`OnPremADVRG`|
    |Location|Selecione um local.|
    |Nome de Usuário do Administrador|`HDIFabrikamAdmin`|
    |Senha do Administrador|Digite uma senha.|
    |Nome de domínio|`HDIFabrikam.com`|
    |Prefixo Dns|`hdifabrikam`|

    Mantenha os valores padrão restantes.

    ![Modelo para criar uma VM Azure com uma nova Floresta Ad Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Revise os **Termos e Condições**e selecione **Concordo com os termos e condições indicados acima.**
1. Selecione **Comprar**e monitore a implantação e aguarde a conclusão. A implantação leva cerca de 30 minutos para ser concluída.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configure usuários e grupos para acesso a clusters

Nesta seção, você criará os usuários que terão acesso ao cluster HDInsight até o final deste guia.

1. Conecte-se ao controlador de domínio usando a área de trabalho remota.
    1. A partir do portal Azure, navegue até **grupos** > de recursos**OnPremADVRG** > **adVM** > **Connect**.
    1. Na lista de paradas de **endereço IP,** selecione o endereço IP público.
    1. Selecione **Baixar arquivo RDP**e, em seguida, abra o arquivo.
    1. Use `HDIFabrikam\HDIFabrikamAdmin` como nome de usuário.
    1. Digite a senha escolhida para a conta de administração.
    1. Selecione **OK**.

1. A partir do painel do **gerenciador de servidor do** controlador de domínio, navegue até usuários**e computadores ativos do diretório**de **ferramentas.** > 

    ![No painel do Gerenciador de servidores, abra o Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Crie dois novos usuários: **HDIAdmin** e **HDIUser**. Esses dois usuários farão login nos clusters HDInsight.

    1. Na página **Usuários e Computadores do Active Directory,** clique com o botão direito do mouse `HDIFabrikam.com`e navegue até o **Novo** > **Usuário**.

        ![Crie um novo usuário do Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na **página Novo Objeto - Usuário,** digite `HDIUser` o **nome primeiro** e o nome de **logon do usuário**. Os outros campos serão autopovoados. Em seguida, selecione **Next**.

        ![Crie o primeiro objeto de usuário de admin](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Na janela pop-up que aparece, digite uma senha para a nova conta. Selecione **Senha nunca expira**e, em seguida, **OK** na mensagem pop-up.
    1. Selecione **Next**e, em seguida, **termine** para criar a nova conta.
    1. Repita as etapas acima `HDIAdmin`para criar o usuário .

        ![Criar um segundo objeto de usuário de admin](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Crie um grupo de segurança global.

    1. A partir **de Usuários e Computadores do Active Directory,** clique com o botão direito do `HDIFabrikam.com`mouse e navegue até o **Novo** > **Grupo**.

    1. Digite `HDIUserGroup` a caixa de texto **nome do grupo.**

    1. Selecione **OK**.

    ![Crie um novo grupo de Diretório Ativo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Criar um novo objeto](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Adicionar membros ao **HDIUserGroup**.

    1. Clique com o botão direito do mouse **em HDIUser** e **selecione Adicionar a um grupo...**.
    1. No **Inserir os nomes do objeto** `HDIUserGroup`para selecionar a caixa de texto, digite . Em seguida, selecione **OK**e **OK** novamente no pop-up.
    1. Repita as etapas anteriores para a conta **HDIAdmin.**

        ![Adicione o HDIUser membro ao grupo HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Agora você criou seu ambiente active directory. Você adicionou dois usuários e um grupo de usuários que podem acessar o cluster HDInsight.

Os usuários serão sincronizados com o Azure AD.

### <a name="create-an-azure-ad-directory"></a>Criar um diretório do AD do Azure

1. Entre no portal do Azure.
1. Selecione Criar um `directory` **recurso** e digitar . Selecione **Azure Active Directory** > **Create**.
1. Em nome da `HDIFabrikam` **Organização,** digite .
1. Em nome de `HDIFabrikamoutlook`domínio **inicial,** digite .
1. Selecione **Criar**.

    ![Criar um diretório do AD do Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Criar um domínio personalizado

1. A partir do seu novo **Diretório Ativo do Azure,** em **Manage,** selecione **Nomes de domínio personalizados**.
1. Selecione **+ Adicione domínio personalizado**.
1. Em Nome de `HDIFabrikam.com`domínio **personalizado,** digite e selecione Adicionar **domínio**.
1. Em [seguida, complete Adicione suas informações de DNS ao registrador de domínios](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Criar um domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Criar um grupo

1. A partir do seu novo **Diretório Ativo do Azure,** em **Gerenciar,** selecione **Grupos**.
1. Selecione **+ Novo grupo**.
1. Na caixa de texto `AAD DC Administrators`do nome do **grupo,** digite .
1. Selecione **Criar**.

## <a name="configure-your-azure-ad-tenant"></a>Configure seu inquilino Azure AD

Agora você configurará seu inquilino Azure AD para que você possa sincronizar usuários e grupos da instância do Active Directory no local para a nuvem.

Crie um administrador de inquilino do Active Directory.

1. Faça login no portal Azure e selecione seu inquilino Azure AD, **HDIFabrikam**.

1. Navegar para **gerenciar** > **usuários** > **Novo usuário**.

1. Digite os seguintes detalhes para o novo usuário:

    **Identidade**

    |Propriedade |Descrição |
    |---|---|
    |Nome de usuário|Insira `fabrikamazureadmin` na caixa de texto. Na lista de desímpara de nomes de domínio, selecione`hdifabrikam.com`|
    |Nome| Digite `fabrikamazureadmin`.|

    **Senha**
    1. Selecione **Deixe-me criar a senha**.
    1. Digite uma senha segura de sua escolha.

    **Grupos e funções**
    1. Selecione **0 grupos selecionados**.
    1. Selecione **administradores AAD DC**e, em seguida, **selecione**.

    ![A caixa de diálogo Grupos Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selecione **Usuário**.
    1. Selecione **o administrador global**e, em seguida, **selecione**.

    ![A caixa de diálogo de função Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selecione **Criar**.

1. Em seguida, faça com que o novo usuário faça login no portal do Azure, onde será solicitado que ele mude a senha. Você precisará fazer isso antes de configurar o Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronize os usuários no local para o Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configurar o Microsoft Azure Active Directory Connect

1. A partir do controlador de domínio, baixe [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Abra o arquivo executável que você baixou e concorde com os termos da licença. Selecione **Continuar**.

1. Selecione **Usar configurações expressas**.

1. Na página **Conecte-se ao Azure AD,** digite o nome de usuário e a senha do administrador global do Azure AD. Use o `fabrikamazureadmin@hdifabrikam.com` nome de usuário que você criou quando configurou o inquilino do Active Directory. Em seguida, selecione **Next**.

    ![A página "Conecte-se ao Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na página **Conecte-se aos Serviços de Domínio do Diretório Ativo,** digite o nome de usuário e a senha de uma conta de administração corporativa. Use o `HDIFabrikam\HDIFabrikamAdmin` nome de usuário e sua senha que você criou anteriormente. Em seguida, selecione **Next**.

   ![A página "Conecte-se ao Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na página **de configuração de login do Azure AD,** selecione **Next**.
   ![A página "Configuração de login azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na **página Pronto para configurar,** selecione **Instalar**.

   ![A página "Pronto para configurar"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na página **Configuração completa** selecione **Sair**.
   ![A página "Configuração completa"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Após a sincronização concluída, confirme se os usuários criados no diretório IaaS estão sincronizados com o Azure AD.
   1. Entre no portal do Azure.
   1. Selecione usuários do **Azure Active Directory** > **HDIFabrikam** > **.**

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário que você pode usar para configurar o Azure AD Domain Services (Azure AD DS). Para obter mais informações, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Entre no portal do Azure.
1. Selecione Criar um `managed identity` **recurso** e digitar . Selecione **Criação de identidade** > gerenciada atribuída pelo**usuário**.
1. Para o nome `HDIFabrikamManagedIdentity`do **recurso,** digite .
1. Selecione sua assinatura.
1. Em **grupo Resource,** selecione `HDIFabrikam-CentralUS`Criar **novo** e inserir .
1. Em **Location,** selecione **Central US**.
1. Selecione **Criar**.

![Criar uma nova identidade gerenciada atribuída pelo usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Habilitar o Azure AD DS

Siga estas etapas para habilitar o Azure AD DS. Para obter mais informações, consulte [Ativar o Azure AD DS usando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Crie uma rede virtual para hospedar o Azure AD DS. Execute o seguinte código PowerShell.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Entre no portal do Azure.
1. Selecione Criar `Domain services` **recurso,** digite e selecione **Azure AD Domain Services** > **Create**.
1. Na página **Básica:**
    1. Em **nome do diretório,** selecione o diretório Azure AD que você criou: **HDIFabrikam**.
    1. Para **nome de domínio DNS,** digite *HDIFabrikam.com*.
    1. Selecione sua assinatura.
    1. Especifique o grupo de recursos **HDIFabrikam-CentralUS**. Para **localização,** selecione **Central US**.

        ![Detalhes básicos do Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na página **Rede,** selecione a rede **(HDIFabrikam-VNET)** e a sub-rede **(Sub-net AADDS)** que você criou usando o script PowerShell. Ou escolha **Criar novo** para criar uma rede virtual agora.

    ![A etapa "Criar rede virtual"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na página do **grupo Administrador,** você deve ver uma notificação de que um grupo chamado **Administradores AAD DC** já foi criado para administrar esse grupo. Você pode modificar a adesão deste grupo se quiser, mas neste caso você não precisa alterá-lo. Selecione **OK**.

    ![Veja o grupo de administradores do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na página **Sincronização,** habilite a sincronização completa selecionando **Tudo** > **OK**.

    ![Habilitar a sincronização do Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na página **Resumo,** verifique os detalhes do Azure AD DS e selecione **OK**.

    ![O resumo de "Habilitar serviços de domínio Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Depois de habilitar o Azure AD DS, um servidor DNS local é executado nas VMs AD do Azure.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configure sua rede virtual Azure AD DS

Use as seguintes etapas para configurar sua rede virtual Azure AD DS **(HDIFabrikam-AADDSVNET)** para usar seus servidores DNS personalizados.

1. Localize os endereços IP de seus servidores DNS personalizados.
    1. Selecione `HDIFabrikam.com` o recurso Azure AD DS.
    1. Em **Gerenciar,** selecione **Propriedades**.
    1. Encontre os endereços IP em **endereço IP na rede virtual**.

    ![Localize endereços IP DNS personalizados para Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configure **o HDIFabrikam-AADDSVNET** para usar endereços IP personalizados 10.0.0.4 e 10.0.0.5.

    1. Em **Configurações,** selecione **servidores DNS**.
    1. Selecione **Personalizado**.
    1. Na caixa de texto, digite o primeiro endereço IP *(10.0.0.4*).
    1. Selecione **Salvar**.
    1. Repita as etapas para adicionar o outro endereço IP *(10.0.0.5*).

Em nosso cenário, configuramos o Azure AD DS para usar endereços IP 10.0.0.4 e 10.0.0.5, configurando o mesmo endereço IP na rede virtual Azure AD DS:

![A página de servidores DNS personalizados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Protegendo o tráfego LDAP

O Lightweight Directory Access Protocol (LDAP) é usado para ler e gravar no Azure Active Directory. Você pode tornar o tráfego LDAP confidencial e seguro usando a tecnologia SSL (Secure Sockets Layer) ou Transport Layer Security (TLS). Você pode habilitar o LDAP sobre SSL (LDAPS) instalando um certificado devidamente formatado.

Para obter mais informações sobre LDAP seguro, consulte [Configurar LDAPS para um domínio gerenciado pelo Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Nesta seção, você cria um certificado auto-assinado, baixa o certificado e configura LDAPS para o domínio gerenciado do **AD** AD Azure Azure.

O script a seguir cria um certificado para **HDIFabrikam**. O certificado é salvo no *caminho LocalMachine.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Qualquer utilitário ou aplicativo que crie uma solicitação válida \#do Public Key Cryptography Standards (PKCS) 10 pode ser usado para formar a solicitação de certificado SSL.

Verifique se o certificado está instalado na loja **Pessoal** do computador:

1. Inicie o Microsoft Management Console (MMC).
1. Adicione o snap-in **Certificados** que gerencia certificados no computador local.
1. Expandir **certificados (computador local)** > **Certificados****pessoais** > . Um novo certificado deve existir na loja **Pessoal.** Este certificado é emitido para o nome de host totalmente qualificado.

    ![Verifique a criação de certificados locais](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. No painel à direita, clique com o botão direito do mouse no certificado que você criou. Aponte para **todas as tarefas**e selecione **Exportar**.

1. Na página **Exportar chave privada,** selecione **Sim, exporte a tecla privada**. O computador onde a chave será importada precisa da chave privada para ler as mensagens criptografadas.

    ![A página chave de exportação privada do assistente de exportação de certificados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na página **'Formato de arquivo de exportação',** deixe as configurações padrão e selecione **'Seguir '''**
1. Na página **Senha,** digite uma senha para a chave privada. Para **criptografia,** selecione **TripleDES-SHA1**. Em seguida, selecione **Next**.
1. Na página **Arquivo para Exportação,** digite o caminho e o nome do arquivo de certificado exportado e, em seguida, selecione **Next**. O nome do arquivo tem que ter uma extensão .pfx. Este arquivo está configurado no portal Azure para estabelecer uma conexão segura.
1. Habilite o LDAPS para um domínio gerenciado pelo Azure AD DS.
    1. No portal Azure, selecione o domínio `HDIFabrikam.com`.
    1. Em **Gerenciar,** selecione **LDAP seguro**.
    1. Na página **Secure LDAP,** em **Secure LDAP,** selecione **Habilitar**.
    1. Procure o arquivo de certificado .pfx que você exportou em seu computador.
    1. Digite a senha do certificado.

    ![Habilitar o LDAP seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Agora que você habilitou o LDAPS, certifique-se de que ele é acessível ativando a porta 636.
    1. No grupo de recursos **HDIFabrikam-CentralUS,** selecione o grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG**.
    1. Em **Configurações,** selecione **''Inbound'** > regras de segurança**adicionar**.
    1. Na **página Adicionar regra de segurança de entrada,** digite as seguintes propriedades e selecione **Adicionar**:

        | Propriedade | Valor |
        |---|---|
        | Fonte | Qualquer |
        | Intervalos de portas de origem | * |
        | Destino | Qualquer |
        | Faixa de porto de destino | 636 |
        | Protocolo | Qualquer |
        | Ação | Allow |
        | Prioridade | \<Número desejado> |
        | Nome | Port_LDAP_636 |

    ![A caixa de diálogo "Adicionar regra de segurança de entrada"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** é a identidade gerenciada atribuída pelo usuário. A função HDInsight Domain Services Contributor está habilitada para a identidade gerenciada que permitirá que essa identidade leia, crie, modifique e exclua operações de serviços de domínio.

![Criar uma identidade gerenciada atribuída ao usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Crie um cluster HDInsight habilitado para ESP

Esta etapa requer os seguintes pré-requisitos:

1. Criar um novo grupo de recursos *HDIFabrikam-WestUS* na localização **West US**.
1. Crie uma rede virtual que hospedará o cluster HDInsight habilitado para ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação de pares entre a rede virtual`HDIFabrikam-AADDSVNET`que hospeda o Azure AD DS (`HDIFabrikam-HDIVNet`) e a rede virtual que hospedará o cluster HDInsight habilitado para ESP ( ). Use o seguinte código PowerShell para espiar as duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta azure Data Lake Storage Gen2 chamada **Hdigen2store**. Configure a conta com a identidade gerenciada pelo usuário **HDIFabrikamManagedIdentity**. Para obter mais informações, consulte [Use Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configure dns personalizados na rede virtual **HDIFabrikam-AADDSVNET.**
    1. Acesse o portal do Azure > **grupos** > de recursos**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS servidores**.
    1. Selecione **Personalizado** e *digite 10.0.0.4* e *10.0.0.5*.
    1. Selecione **Salvar**.

        ![Salve configurações de DNS personalizadas para uma rede virtual](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Crie um novo cluster HDInsight Spark habilitado para ESP.
    1. Selecione **Personalizado (tamanho, configurações, aplicativos)**.
    1. Digite **detalhes** para o Básico (seção 1). Certifique-se de que o **tipo de cluster** é Spark **2.3 (HDI 3.6)**. Certifique-se de que o **grupo De recursos** é **HDIFabrikam-CentralUS**.

    1. Para **segurança + rede** (seção 2), preencha os seguintes detalhes:
        * Em **Enterprise Security Package,** **selecione Ativado**.
        * Selecione **o usuário de administrador do Cluster** e selecione a conta **HDIAdmin** que você criou como o usuário de administração no local. Clique em **Selecionar**.
        * Selecione **o grupo** > de acesso ao cluster**HDIUserGroup**. Qualquer usuário que você adicionar a este grupo no futuro poderá acessar os clusters HDInsight.

            ![Selecione o grupo de acesso ao cluster HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Complete as outras etapas da configuração do cluster e verifique os detalhes no resumo do **Cluster**. Selecione **Criar**.

1. Faça login na UI Ambari para o `https://CLUSTERNAME.azurehdinsight.net`cluster recém-criado em . Use seu nome de `hdiadmin@hdifabrikam.com` usuário de admin e sua senha.

    ![A janela de entrada da UI Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. No painel de cluster, selecione **Funções**.
1. Na página **Funções,** em **Atribuir funções a estas**, ao lado da função **Administrador de cluster,** digite o *grupo hdiusergroup*. 

    ![Atribuir a função de administrador de cluster ao hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Abra seu cliente Secure Shell (SSH) e faça login no cluster. Use o **hdiuser** que você criou na instância do Active Directory no local.

    ![Faça login no cluster usando o cliente SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Se você puder fazer login com essa conta, você configurou seu cluster ESP corretamente para sincronizar com a instância do Active Directory no local.

## <a name="next-steps"></a>Próximas etapas

Leia [uma introdução à segurança Apache Hadoop com ESP](hdinsight-security-overview.md).
