---
title: Implantar um parceiro de segurança confiável do Azure Firewall Manager
description: Saiba como implantar uma segurança confiável do Azure Firewall Manager usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931312"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Implante um parceiro de segurança confiável (versão prévia)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Os parceiros de segurança confiáveis* no Azure Firewall Manager permitem que você use suas ofertas familiares e de segurança de terceiros como serviço (SECaaS) para proteger o acesso à Internet para seus usuários.

Para saber mais sobre cenários suportados e diretrizes de boas práticas, veja [Quais são os parceiros de segurança confiáveis (preview)?](trusted-security-partners.md).

Os parceiros de segurança suportados são **ZScaler** e **iboss** para esta pré-visualização. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A Versão prévia do Gerenciador de Firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` do PowerShell.

Em um prompt de comando do PowerShell, execute os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
A conclusão do registro do recurso demora até 30 minutos. Execute o seguinte comando para verificar o status do registro:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implantar um provedor de segurança de terceiros em um novo hub

1. Entre no Portal do Azure em https://portal.azure.com.
2. Em **Pesquisar,** digite **o Firewall Manager** e selecione-o em **Serviços**.
3. Navegue até **Começar**. Selecione **Criar um Hub Virtual Protegido**. 
4. Digite seu grupo de assinatura e recursos, selecione uma região suportada e adicione suas informações de HUB e WAN virtuais. 
5. **O gateway DE IMPLANTAÇÃO VPN** é ativado por padrão. Um Gateway VPN é necessário para implantar um parceiro de segurança confiável no hub. 
6. Selecione **a seguir: Firewall Azure**
   > [!NOTE]
   > Parceiros de segurança confiáveis se conectam ao seu hub usando túneis VPN Gateway. Se você excluir o Gateway VPN, as conexões com seus parceiros de segurança confiáveis serão perdidas.
7. Se você quiser implantar o Azure Firewall para filtrar o tráfego privado junto com o provedor de serviços de terceiros para filtrar o tráfego da Internet, selecione uma diretiva para o Firewall Do Azure. Veja os [cenários suportados](trusted-security-partners.md#key-scenarios).
8. Se você quiser implantar apenas um provedor de segurança de terceiros no hub, selecione **O Firewall Azure: Ativado/desativado** para configurá-lo **como Desativado**. 
9. Selecione **A seguir: Parceiros de segurança confiáveis**.
10. Selecione **o Parceiro de Segurança Confiável** para defini-lo como **Ativado**. Selecione um parceiro. 
11. Selecione **Avançar**. 
12. Revise o conteúdo e selecione **Criar**.

A implantação do gateway VPN pode levar mais de 30 minutos.

Para verificar se o hub foi criado, navegue até o Azure Firewall Manager->Secured Hubs. Selecione a página 'Visão geral' >do hub para mostrar o nome do parceiro e o status como **Conexão de segurança Pendente**.

Uma vez que o hub seja criado e o parceiro de segurança seja configurado, continue conectando o provedor de segurança ao hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implantar um provedor de segurança de terceiros em um hub existente

Você também pode selecionar um hub existente em uma WAN virtual e convertê-lo em um *hub virtual protegido*.

1. Em **Como Começar,** selecione **Converter hubs existentes**.
2. Selecione uma assinatura e um hub existente. Siga o resto das etapas para implantar um provedor de terceiros em um novo hub.

Lembre-se de que um gateway VPN deve ser implantado para converter um hub existente em um hub protegido com provedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configure provedores de segurança de terceiros para se conectar a um hub seguro

Para configurar túneis para o GATEWAY VPN do seu hub virtual, os provedores de terceiros precisam de direitos de acesso ao seu hub. Para isso, associe um diretor de serviço ao seu grupo de assinatura ou recurso e conceda direitos de acesso. Em seguida, você deve dar essas credenciais ao terceiro usando seu portal.

1. Criar o principal de serviço do Azure Active Directory (AD): Você pode pular a URL de redirecionamento. 

   [Como usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adicione direitos de acesso e escopo para o diretor do serviço.
   [Como usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Você pode limitar o acesso apenas ao seu grupo de recursos para um controle mais granular.
3. Siga o [ZScaler: Configurando as instruções de integração de WAN virtuais do Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) para:

   - Entre no portal do parceiro e adicione suas credenciais para dar ao parceiro confiável acesso ao seu hub protegido.
   - Sincronize os hubs virtuais no portal parceiro e configure o túnel para o hub virtual. Você pode fazê-lo assim que suas credenciais de autenticação Azure AD forem validadas.
   
4. Você pode olhar para o status de criação de túneis no portal WAN Virtual do Azure no Azure. Uma vez que os túneis se **mostrem conectados** tanto no Azure quanto no portal parceiro, continue com os próximos passos para configurar rotas para selecionar quais filiais e VNets devem enviar tráfego da Internet para o parceiro.

## <a name="configure-route-settings"></a>Configurar configurações de rota

1. Navegue até o Azure Firewall Manager -> Secured Hubs. 
2. Selecione um hub. O status do Hub deve agora mostrar **provisionado** em vez de **conexão**de segurança pendente .

   Certifique-se de que o provedor de terceiros possa se conectar ao hub. Os túneis no gateway VPN devem estar em um estado **conectado.** Esse estado reflete mais a conexão de saúde entre o hub e o parceiro terceirizado, em comparação com o status anterior.
3. Selecione o hub e navegue até **configurações de rota**.

   Quando você implanta um provedor de terceiros no hub, ele converte o hub em um *hub virtual protegido*. Isso garante que o provedor de terceiros esteja anunciando uma rota 0.0.0.0/0 (padrão) para o hub. No entanto, as conexões E sites VNet conectados ao hub não recebem essa rota a menos que você opte por quais conexões devem obter essa rota padrão.
4. Em **tráfego na Internet,** selecione **VNet-para-Internet** ou **Branch-to-Internet** ou ambos para que as rotas sejam configuradas enviar através de terceiros.

   Isso só indica que tipo de tráfego deve ser encaminhado para o hub, mas ainda não afeta as rotas em VNets ou filiais. Essas rotas não são propagadas para todos os VNets/filiais conectados ao hub por padrão.
5. Você deve selecionar **conexões seguras** e selecionar as conexões nas quais essas rotas devem ser definidas. Isso indica quais VNets/filiais podem começar a enviar tráfego da Internet para o provedor de terceiros.
6. Nas **configurações de Rota,** selecione **Conectar conexões** tráfego da Internet e selecione o VNet ou as ramificações *(sites* na WAN Virtual) a serem protegidas. Selecione **Tráfego seguro da Internet**.
   ![Tráfego seguro da Internet](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navegue de volta para a página hubs. O status de parceiro de **segurança confiável** do hub agora deve ser **protegido**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de Agência ou Internet VNet via serviço de terceiros

Em seguida, você pode verificar se as máquinas virtuais VNet ou o site da filial podem acessar a Internet e validar se o tráfego está fluindo para o serviço de terceiros.

Após terminar as etapas de configuração de rota, as máquinas virtuais VNet, bem como os sites de filial, são enviados uma rota de serviço 0/0 para terceiros. Você não pode RDP ou SSH nessas máquinas virtuais. Para fazer login, você pode implantar o serviço [Azure Bastion](../bastion/bastion-overview.md) em um VNet peered.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Proteja sua rede na nuvem com o Azure Firewall Manager Preview usando o portal Azure](secure-cloud-network.md)




