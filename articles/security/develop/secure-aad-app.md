---
title: Desenvolva um aplicativo Web AD seguro do Azure | Microsoft Docs
description: Este simples aplicativo de exemplo implementa práticas recomendadas de segurança que melhoram sua aplicação e a postura de segurança da sua organização quando você se desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810538"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Desenvolva um aplicativo seguro para um aplicativo AD do Azure
## <a name="overview"></a>Visão geral

Esta amostra é um simples Azure Active Directory com aplicativo web que se conecta a recursos de segurança para o desenvolvimento de aplicativos no Azure. O aplicativo implementa práticas recomendadas de segurança que podem ajudar a melhorar seu aplicativo e a postura de segurança da sua organização quando você desenvolve aplicativos no Azure.

Os scripts de implantação configuram a infra-estrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal Azure para vincular os componentes e serviços juntos. Esta amostra é direcionada para desenvolvedores experientes no Azure que trabalham dentro do setor de varejo e querem construir um Diretório Ativo Azure seguro com infra-estrutura segura do Azure. 


Ao desenvolver e implantar este aplicativo, você aprenderá como 
- Crie uma instância do Azure Key Vault, armazene e recupere segredos dele.
- Implante o Azure Web App, que é dedicado isolado com acesso a firewall front-end. 
- Crie e configure uma instância do Azure Application Gateway com um firewall que usa o OWASP Top 10 Ruleset. 
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure. 
- Configure a política e o centro de segurança do Azure para avaliar os compliancies. 

Depois de desenvolver e implantar este aplicativo, você terá configurado o seguinte aplicativo web de amostra, juntamente com as medidas de configuração e segurança descritas.

## <a name="architecture"></a>Arquitetura
O aplicativo é um aplicativo n-tier típico com três níveis. A camada front-end, back-end e banco de dados com componentes de monitoramento e gerenciamento secreto integrados são mostrados aqui:

![Arquitetura do aplicativo](./media/secure-aad-app/architecture.png)

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão na seção Arquitetura de implantação. 

A arquitetura consiste nesses componentes

- [Gateway de Aplicativo do Azure](../../application-gateway/index.yml). Fornece o gateway e o firewall para nossa arquitetura de aplicativos.
- [Insights de aplicativos](../../azure-monitor/app/app-insights-overview.md). Fornece um serviço APM (Application Performance Management, gerenciamento de desempenho de aplicativos) extensível em várias plataformas.
- [Azure Key Vault](../../key-vault/index.yml). Armazena e criptografa os segredos do nosso aplicativo e gerencia a criação de políticas de acesso ao seu redor.
- [Diretório Ativo do Azure](../../active-directory/fundamentals/active-directory-whatis.md). Fornece serviço de gerenciamento de identidade e acesso baseado em nuvem, login e recursos de acesso.
- [Sistema de nomes de domínio do Azure](../../dns/dns-overview.md). Forneça o serviço para hospedar o domínio.
- [Balançador de carga azure](../../load-balancer/load-balancer-overview.md). Fornece para dimensionar seus aplicativos e criar alta disponibilidade para seus serviços.
- [Azure Web App](../../app-service/overview.md).  Fornece um serviço baseado em HTTP para hospedagem de aplicativos web.
- [Centro de Segurança Azure](../../security-center/index.yml). fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem.
- [Política Azure](../../governance/policy/overview.md). Fornece a avaliação de seus recursos para o não cumprimento das políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
Modelagem de ameaças é o processo de identificar potenciais ameaças à segurança de sua empresa e aplicativo e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Esta amostra usou a [Ferramenta de Modelagem de Ameaças](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) da Microsoft para implementar a modelagem de ameaças para o aplicativo de amostra segura. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. Tempo e dinheiro serão economizados mais tarde usando isso.

Aqui está o modelo de ameaça para o aplicativo de amostra

![Modelo de ameaça](./media/secure-aad-app/threat-model.png)

Algumas ameaças de exemplo e potenciais vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Pré-requisitos
Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e visualizar o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código fonte localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar JSON de forma fácil de usar.

Você precisa de uma assinatura do Azure para implantar os recursos do aplicativo de exemplo. Se você não tiver uma assinatura do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar o aplicativo de exemplo.

Depois de instalar essas ferramentas, você está pronto para implantar o aplicativo no Azure.

### <a name="implementation-guidance"></a>Diretrizes de implementação
O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso Azure que está no [diagrama](#architecture)de arquitetura .

As quatro fases são

- Implantar o Cofre de Chaves Azure.
- Implantar aplicativos web do Azure.
- Implantar o Gateway de aplicativos com firewall de aplicativos web.
- Configure um Ad do Azure com aplicativo implantado.

Cada fase se baseia na anterior usando a configuração dos recursos anteriormente implantados.

Para concluir as etapas de implementação, certifique-se de que você instalou as ferramentas listadas em [Pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implantar o cofre de chaves do Azure
Nesta seção, você cria e implanta uma instância do Azure Key Vault que é usada para armazenar segredos e certificados.

Depois de concluir a implantação, você tem uma ocorrência do Azure Key Vault implantada no Azure.

Para implantar o Azure Key Vault usando o Powershell
 
1. Declare as variáveis para Azure Key Vault.
2. Registre o provedor do Azure Key Vault.
3. Crie o grupo de recursos para a ocorrência.
4. Crie a instância do Azure Key Vault no grupo de recursos criado na etapa 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>O usuário AD do Azure abaixo terá permissões de administrador para o Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registre os Provedores az
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Crie a instância do Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Adicione as políticas de administrador ao cofre de chaves
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Para criar uma política de acesso para permitir que um usuário obtenha e liste chaves criptográficas, certificados e segredos se você souber o Nome Principal do Usuário:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

É uma prática recomendada usar identidades gerenciadas para recursos do Azure em aplicativos que usam o Key Vault para acessar recursos. Sua postura de segurança aumenta quando as chaves de acesso ao Key Vault não são armazenadas em código ou configuração.

Um certificado raiz está incluído no recipiente. As medidas tomadas para obter o certificado são

1. Baixe o arquivo de certificado da Autoridade de [Certificado](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Decodfique seu arquivo de certificado:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Este script cria uma identidade atribuída para a instância do Serviço de Aplicativo que pode ser usada com o MSI para interagir com o Azure Key Vault sem segredos de codificação rígidos em código ou configuração.

Vá até a instância do Azure Key Vault no portal para autorizar a identidade atribuída na guia de diretiva de acesso. Selecione **Adicionar nova diretiva de acesso**. Em **Select principal,** procure o nome do aplicativo semelhante ao nome da instância do Serviço de Aplicativo criada.
Um princípio de serviço anexado ao aplicativo deve ser visível. Selecione-o e salve a página de diretiva de acesso, conforme mostrado na captura de tela a seguir.

Como o aplicativo só precisa recuperar chaves, selecione a permissão **Obter** nas opções de segredos, permitindo o acesso enquanto reduz os privilégios concedidos.

![Política de acesso ao Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Crie uma política de acesso do Key Vault*

Salve a diretiva de acesso e salve a nova alteração na guia **Políticas de acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implantar o Application Gateway com firewall de aplicativos web ativado
Em aplicativos web, não é recomendável que você exponha serviços diretamente para o mundo exterior na internet.
As regras de balanceamento de carga e firewall fornecem mais segurança e controle sobre o tráfego de entrada e ajudam a gerenciá-lo.

Para implantar uma instância do Gateway de aplicativos

1. Crie o grupo de recursos para abrigar o gateway de aplicativo.
2. Provisionar uma rede virtual para anexar ao gateway.
3. Crie uma sub-rede para o gateway na rede virtual.
4. Provisão um endereço IP público.
5. Provisão o gateway de aplicação.
6. Habilite o firewall do aplicativo web no gateway.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Implantar aplicativos web do Azure
O Azure App Service permite criar e hospedar aplicativos web usando as linguagens como Python, Ruby, C#e Java. O Azure também suporta contêineres personalizados, o que pode permitir que praticamente todas as linguagens de programação seja executada na plataforma Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Crie um plano de serviço de aplicativos em nível gratuito
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Criar um aplicativo Web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Antes de continuar, vá para a ui de configuração do sistema https://aka.ms/appservicecustomdns de nomes de domínio do Azure para o seu domínio personalizado e siga as instruções para configurar um registro CNAME para o nome de host "www" e aponte-o para o nome de domínio padrão do seu aplicativo web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Plano de serviço de upgrade para nível compartilhado (mínimo exigido por domínios personalizados)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Adicione um nome de domínio personalizado ao aplicativo web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="network"></a>Rede
Depois de concluir a implantação, você tem um gateway de aplicativo com firewall de aplicativo web ativado.

A instância do gateway expõe a porta 443 para HTTPS. Essa configuração garante que nosso aplicativo só esteja acessível na porta 443 através de HTTPS.

Bloquear portas não utilizadas e limitar a exposição à superfície de ataque é uma prática recomendada de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicionar grupos de segurança de rede à instância do Serviço de Aplicativo

As instâncias do App Service podem ser integradas a redes virtuais. Essa integração permite que eles sejam configurados com políticas de grupo de segurança de rede que gerenciam o tráfego de entrada e saída do aplicativo.

1. Para habilitar esse recurso, na lâmina de instância de serviço do Azure App, em **Configurações,** **seleciona Rede**. No painel direito, configure em **VNet Integration**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para serviço de aplicativos*
1. Na próxima página, selecione **Adicionar VNET (visualização)**.

1. No menu seguinte, selecione a rede virtual criada `aad-vnet`na implantação que começa com . Você pode criar uma nova sub-rede ou selecionar uma já existente.
   Neste caso, crie uma nova sub-rede. Defina o **intervalo de endereços** para **10.0.3.0/24** e nomeie a **sub-sub-rede de aplicativos de sub-rede**.

   ![Configuração de rede virtual do App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para serviço de aplicativos*

Agora que você habilitou a integração de rede virtual, você pode adicionar grupos de segurança de rede ao nosso aplicativo.

1. Use a caixa de pesquisa, procure grupos **de segurança da rede**. Selecione **grupos de segurança da rede** nos resultados.

    ![Procure por grupos de segurança de rede](./media/secure-web-app/nsg-search-menu.png)

    *Procure por grupos de segurança de rede*

2. No menu seguinte, selecione **Adicionar**. Digite o **nome** do NSG e do **grupo Resource** no qual ele deve estar localizado. Este NSG será aplicado à sub-rede do gateway de aplicativo.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois que o NSG for criado, selecione-o. Em sua lâmina, em **Configurações,** selecione **Regras de segurança de entrada**. Configure essas configurações para permitir que as conexões entrem no gateway de aplicativo sobre a porta 443.

   ![Configure o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configure o NSG*

4. Nas regras de saída para o GATEWAY NSG, adicione uma regra que permite conexões de saída à instância do App Service, criando uma regra que visa a tag de serviço`AppService`

   ![Adicionar regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicionar regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o gateway envie regras de saída para uma rede virtual.

   ![Adicione outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicione outra regra de saída*

5. Na lâmina de sub-redes do NSG, selecione **Associate,** selecione a rede virtual criada na implantação e selecione a sub-rede de gateway chamada **gw-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como na etapa anterior, desta vez para a instância do Serviço de Aplicativo. Dê-lhe um nome. Adicione a regra de entrada para a porta 443 como você fez para o portal de aplicativos NSG.

   Se você tiver uma instância do App Service implantada em uma instância do App Service Environment, o que não é o caso deste aplicativo, você pode adicionar regras de entrada para permitir testes do Azure Service Health abrindo portas 454-455 nos grupos de segurança de entrada do seu App Service NSG. Aqui está a configuração:

   ![Adicionar regras para testes de saúde do Serviço Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para testes azure Service Health (somente o App Service Environment)*

Para limitar a superfície de ataque, modifique as configurações da rede app service para permitir que apenas o gateway do aplicativo acesse o aplicativo.
Para aplicar as configurações, vá para a guia de rede do App Service, selecionando a guia **Restrições de IP** e criando uma regra de permitir que apenas o IP do gateway do aplicativo acesse diretamente o serviço. Você pode recuperar o endereço IP do gateway a partir de sua página de visão geral. Na guia **CIDR de endereço IP,** digite `<GATEWAY_IP_ADDRESS>/32`o endereço IP neste formato: .

![Permitir apenas o gateway](./media/secure-web-app/app-allow-gw-only.png)

*Permitir apenas o IP gateway para acessar o Serviço de Aplicativo*

### <a name="azure-domain-name-system"></a>Sistema de nomes de domínio do Azure 
O Sistema de Nomes de Domínio do Azure, ou Sistema de Nome de Domínio do Azure, é responsável por traduzir (ou resolver) um site ou nome de serviço para seu endereço IP. O Azure Domainhttps://docs.microsoft.com/azure/dns/dns-overview) Name System (é um serviço de hospedagem para domínios do Domain Name System que fornece resolução de nomes usando a infra-estrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros do Sistema de Nomes de Domínio usando as mesmas credenciais, APIs, ferramentas e faturamento como outros serviços do Azure. O Azure Domain Name System também suporta domínios privados do Sistema de Nomes de Domínio.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
O Azure Disk Encryption utiliza o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

### <a name="identity-management"></a>Gerenciamento de identidades
As seguintes tecnologias fornecem recursos para gerenciar o acesso aos dados do titular de cartão no ambiente Azure
- O Azure Active Directory é o serviço de gerenciamento de identidade e diretório baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure Active Directory, incluindo usuários que acessam o WebApp do Azure.
- O controle de acesso baseado em função do Azure permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissão irrestrita aos recursos do Azure a todos os usuários, os administradores podem permitir que apenas determinadas ações acessem os dados do titular do cartão. O acesso à assinatura é limitado ao administrador da assinatura.
- O Azure Active Directory Privileged Identity Management permite que os clientes minimizem o número de usuários com acesso a determinadas informações, como dados do titular do cartão. Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O Azure Active Directory Identity Protection detecta potenciais vulnerabilidades que afetam as identidades de uma organização, configura respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-las.
### <a name="secrets-management"></a>Gerenciamento de segredos
A solução usa o Azure Key Vault para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os seguintes recursos do Azure Key Vault ajudam os clientes a proteger e acessar esses dados
   - Políticas de acesso avançadas são configuradas com base na necessidade.
   - As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
   - Todas as chaves e segredos no Key Vault têm datas de validade.
   - Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA protegida do módulo de segurança de hardware (HSM) de 2048 bits.
   - Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, . Arquivos PFX e senhas) usando chaves protegidas por módulos de segurança de hardware (HSMs). 
   - Use o RBAC (Role-Based Access Control, controle de acesso baseado em função) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo.     
   - Use o Key Vault para gerenciar seus certificados TLS com renovação automática. 
   - Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
   - As operações criptográficas permitidas para chaves são restritas às necessárias.
### <a name="azure-security-center"></a>Central de Segurança do Azure
Com a Central de Segurança do Azure, os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. Além disso 
   - O Azure Security Center acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.
   - A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança do Azure tem um conjunto de alertas de segurança predefinidos, que são disparados em caso de ameaça ou atividade suspeita. As regras de alerta personalizadas na Central de Segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados já coletados do ambiente.
   - A Central de Segurança do Azure fornece alertas de segurança e incidentes priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um relatório de inteligência contra ameaças é gerado para cada ameaça detectada, a fim de ajudar as equipes de resposta a incidentes a investigar e corrigir as ameaças.
### <a name="azure-application-gateway"></a>Gateway de Aplicativo do Azure 
   A arquitetura reduz o risco de vulnerabilidades de segurança usando um Gateway de Aplicativo do Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Recursos adicionais incluem
   - TLS de ponta a ponta.
   - Desabilitar o TLS v1.0 e v1.1.
   - Habilite o TLSv1.2.
   - Firewall de aplicativos web (modo de prevenção).
   - Modo de prevenção com regras OWASP 3.0.
   - Habilite o registro de diagnósticos.
   - Sondas de saúde personalizadas.
   - O Azure Security Center e um Azure Advisor fornecem proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.
### <a name="logging-and-auditing"></a>Registro em log e auditoria
Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
   - Registros de atividades: [os registros de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações realizadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
   - Registros de diagnóstico: [Os registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por cada recurso. Esses registros incluem registros do sistema de eventos do Windows, logs de armazenamento do Azure, registros de auditoria do Key Vault e registros de acesso e firewall do Application Gateway. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.
### <a name="azure-monitor-logs"></a>Logs do Azure Monitor
   Esses registros são consolidados nos [registros do Monitor Do Azure](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios de dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados juntos, independentemente de sua origem original. Além disso, o Azure Security Center integra-se aos logs do Azure Monitor, permitindo que os clientes usem consultas kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

   As seguintes [soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) de monitoramento do Azure são incluídas como parte desta arquitetura

   - [Avaliação do diretório ativo](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução Active Directory Health Check avalia o risco e a saúde dos ambientes do servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infra-estrutura de servidor implantada.
   - [Agente Saúde](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Agent Health informa quantos agentes estão implantados e sua distribuição geográfica, bem como quantos agentes, que não respondem e o número de agentes, que estão enviando dados operacionais.
   - [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução Análise do Log de Atividades ajuda com a análise dos logs de atividades do Azure em todas as assinaturas do Azure de um cliente.
### <a name="azure-monitor"></a>Azure Monitor
   [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)ajuda os usuários a rastrear o desempenho, manter a segurança e identificar tendências, permitindo que as organizações auditem, criem alertas e arquivem dados, incluindo o rastreamento de chamadas de API em seus recursos do Azure.
### <a name="application-insights"></a>Application Insights 
   O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de Gerenciamento de Desempenho de Aplicativos extensível para desenvolvedores da Web em várias plataformas. O Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web online. Ele inclui ferramentas de análise avançadas para ajudar os clientes a diagnosticarem problemas e entenderem o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar os clientes a aprimorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Cofre de Chave do Azure
   Crie um cofre para a organização em que armazenar chaves e mantenha a responsabilidade por tarefas operacionais como abaixo

   - Os dados armazenados no Key Vault incluem   
   - Chave do Application Insights
   - Chave de acesso ao armazenamento de dados
   - Cadeia de conexão
   - Nome da tabela de dados
   - Credenciais do Usuário
   - Políticas de acesso avançadas são configuradas em uma base de necessidade
   - As políticas de acesso do Key Vault são definidas com permissões mínimas necessárias para chaves e segredos
   - Todas as chaves e segredos no Key Vault têm datas de validade
   - Todas as chaves no Key Vault são protegidas pelo módulo de segurança de hardware (HSM) [Key Type = hardware security module (HSM) Protegido       
     Chave RSA de 2048 bits]
   - Todos os usuários/identidades recebem permissões mínimas necessárias usando o RBAC (Role-Based Access Control, controle de acesso baseado em função)
   - Aplicativos não compartilham um Key Vault, exceto se confiam uns nos outros e precisam ter acesso aos mesmos segredos em runtime
   - Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
   - Operações criptográficas permitidas para chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
   Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisava ser configurado estabelecendo com segurança uma conexão com os recursos implantados como parte desta arquitetura de referência de aplicativos web PaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

   Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes "túnelem" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O modo de túnel IPsec é usado nessa opção como um mecanismo de criptografia.

   Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

   Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementar o Diretório Ativo do Azure OIDC

1. Para clonar o repositório de código-fonte, use este comando Git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Atualize os URLs de redirecionamento
1.  Navegue até o Portal do Azure. No painel de navegação à esquerda, selecione o serviço Azure Active Directory e, em seguida, Registros de aplicativo.
2.  Na tela resultante, selecione o aplicativo WebApp-OpenIDConnect-DotNet-code-v2.
3.  Na guia Autenticação o Na seção Redirecionar URIs, selecione A Web na caixa de combinação e adicione as URIs de redirecionamento a seguir.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o Na seção Configurações avançadas definir URL de logout parahttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Na guia Marca o Atualize a URL da página inicial https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netpara o endereço do serviço do aplicativo, por exemplo .
        o Salvar a configuração.
5.  Se o aplicativo chamar uma api web, certifique-se de aplicar as alterações necessárias no projeto appsettings.json, para que ele chame a URL da API publicada em vez de localhost.
Publicação da amostra
    1.  Na guia Visão geral do Serviço de Aplicativo, baixe o perfil de publicação clicando no link Obter perfil de publicação e salve-o. Outros mecanismos de implantação, tais como por meio de controle do código-fonte, também podem ser usados.
    2.  Mude para o Visual Studio e vá para o projeto WebApp-OpenIDConnect-DotNet-code-v2. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione Publicar. Clique em Importar Perfil na barra inferior e importe o perfil de publicação que você baixou anteriormente.
    3.  Clique em Configurar e na guia Conexão, atualize a URL de destino para https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netque ela seja uma https na url da página inicial, por exemplo . Clique em Avançar.
    4.  Na guia Configurações, certifique-se de que a autenticação organizacional não está selecionada. Clique em Salvar. Clique em Publicar na tela principal.
    5.  O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto. Se você vir a página da Web padrão do projeto, a publicação terá sido bem-sucedida.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar autenticação multifatorial para diretório ativo do Azure
   Os administradores precisam garantir que as contas de assinatura no portal estejam protegidas. A assinatura é vulnerável a ataques porque gerencia os recursos que você criou. Para proteger a assinatura, habilite a Autenticação Multifatorial na guia Diretório Ativo do **Azure** da assinatura.

   Um Azure AD opera com base em políticas que são aplicadas a um usuário ou grupos de usuários que se encaixam em determinados critérios.
O Azure cria uma diretiva padrão especificando que os administradores precisam de autenticação de dois fatores para entrar no portal.
Depois de habilitar esta política, você pode ser solicitado a sair e entrar de volta no portal Azure.

Para habilitar o MFA para logins de admin

   1. Vá para a guia Diretório ativo do **Azure** no portal Azure
   2. Na categoria de segurança, selecione o acesso condicional. Você vê esta tela

       ![Acesso Condicional - Políticas](./media/secure-aad-app/ad-mfa-conditional-add.png)

Se você não pode criar uma nova política

   1. Vá para a guia **MFA.**
   2. Selecione um link de **avaliação azure** AD Premium Free para assinar a avaliação gratuita.

   ![Uma avaliação gratuita do Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Retorne à tela de acesso condicional.

   1. Selecione a nova guia de diretiva.
   2. Insira o nome da política.
   3. Selecione os usuários ou grupos para os quais deseja ativar o MFA.
   4. Em **Controles de acesso,** selecione a guia **Conceder** e, em seguida, **selecione Exigir autenticação multifatorial** (e outras configurações, se quiser).

   ![Exigir MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Você pode habilitar a diretiva selecionando a caixa de seleção na parte superior da tela ou fazê-lo na guia **Acesso Condicional.** Quando a política é ativada, os usuários precisam que o MFA faça login no portal.

   Há uma política de linha de base que requer MFA para todos os administradores do Azure. Você pode habilitá-lo imediatamente no portal. Habilitar essa diretiva pode invalidar a sessão atual e forçá-lo a fazer login novamente.

   Se a política de linha de base não estiver habilitada
   1.   Selecione **Exigir MFA para admins**.
   2.   Selecione **Usar a diretiva imediatamente**.

   ![Selecionar política de uso imediatamente](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Use o Azure Sentinel para monitorar aplicativos e recursos

   À medida que um aplicativo cresce, torna-se difícil agregar todos os sinais de segurança e métricas recebidos dos recursos e torná-los úteis de forma orientada à ação.

   O Azure Sentinel foi projetado para coletar dados, detectar os tipos de ameaças possíveis e fornecer visibilidade a incidentes de segurança.
Enquanto aguarda a intervenção manual, o Azure Sentinel pode contar com cartilhas pré-escritas para iniciar alertas e processos de gerenciamento de incidentes.

   O aplicativo de exemplo é composto por vários recursos que o Azure Sentinel pode monitorar.
Para configurar o Azure Sentinel, primeiro você precisa criar um espaço de trabalho do Log Analytics que armazena todos os dados coletados dos vários recursos.

Para criar este espaço de trabalho

   1. Na caixa de pesquisa no portal Azure, procure por **Log Analytics**. Selecione **espaços de trabalho do Log Analytics**.

   ![Pesquisar espaços de trabalho do Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Pesquisar espaços de trabalho do Log Analytics*

   2. Na página seguinte, **selecione Adicionar** e, em seguida, forneça um nome, grupo de recursos e localização para o espaço de trabalho.
   ![Criar um espaço de trabalho do Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Crie um espaço de trabalho do Log Analytics*

   3. Use a caixa de pesquisa para procurar **o Azure Sentinel**.

   ![Pesquisar por Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Procure o Azure Sentinel*

   4. Selecione **Adicionar** e, em seguida, selecione o espaço de trabalho log analytics que você criou anteriormente.

   ![Adicionar um espaço de trabalho do Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Adicionar um espaço de trabalho do Log Analytics*

   5. Na página **Azure Sentinel - Conectores de dados,** em **Configuração,** selecione **conectores de dados**. Você vê uma matriz de serviços do Azure que você pode vincular à instância de armazenamento do Log Analytics para análise no Azure Sentinel.

   ![Conectores de dados do Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Adicione um conector de dados ao Azure Sentinel*

   Por exemplo, para conectar o gateway de aplicativo, dê estas etapas:

   1. Abra a lâmina de ocorrência do Gateway do aplicativo Azure.
   2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
   3. Selecione **Adicionar configuração de diagnóstico**.

   ![Adicionar diagnósticos do Gateway de aplicativos](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Adicionar diagnósticos do Gateway de aplicativos*

   4. Na página Configurações de **Diagnóstico,** selecione o espaço de trabalho log analytics que você criou e selecione todas as métricas que deseja coletar e envie para o Azure Sentinel. Clique em **Salvar**.

   ![Configurações do conector Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considerações de custo
   Se você ainda não tem uma conta no Azure, você pode criar uma gratuita. Vá para a [página de conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

   Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. À medida que o aplicativo escala e os níveis e testes gratuitos oferecidos pelo Azure precisam ser atualizados para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="next-steps"></a>Próximas etapas
   Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Design](secure-design.md)
- [Desenvolver](secure-develop.md)
- [Implantar](secure-deploy.md)
