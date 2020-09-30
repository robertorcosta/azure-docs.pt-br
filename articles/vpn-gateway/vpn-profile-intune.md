---
title: Criar um perfil do Intune para clientes VPN do Azure
titleSuffix: Azure VPN Gateway
description: Saiba como criar um perfil personalizado do Intune para implantar perfis de cliente VPN do Azure
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 7105597ec34e804c2f2b85b01feb4824d63005c5
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578114"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Criar um perfil do Intune para implantar perfis de cliente VPN

Você pode implantar perfis para clientes de VPN do Azure (Windows 10) usando Microsoft Intune. Este artigo ajuda você a criar um perfil do Intune usando configurações personalizadas.

> [!NOTE]
> Esse método só funcionará para a implantação de perfis que usam Azure Active Directory ou um certificado comum para autenticação de cliente. Se forem usados certificados de cliente exclusivos, cada usuário terá que selecionar o certificado correto manualmente no cliente de VPN do Azure.
>

## <a name="prerequisites"></a>Pré-requisitos

* Os dispositivos já estão registrados com o MDM do Intune.
* O cliente VPN do Azure para Windows 10 já está implantado no computador cliente.
* Somente o Windows versão 19H2 ou superior tem suporte.

## <a name="modify-xml"></a><a name="xml"></a>Modificar XML

Nas etapas a seguir, usamos um XML de exemplo para um perfil de OMA-URI personalizado para o Intune com as seguintes configurações:

* Conectar automaticamente
* Detecção de rede confiável habilitada.

Para outras opções com suporte, consulte o artigo [CSP VPNv2](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) .

1. Baixe o perfil de VPN do portal do Azure e extraia o arquivo de *azurevpnconfig.xml* do pacote.
1. Copie e cole o texto abaixo em um novo arquivo do editor de texto.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Modifique a entrada entre ```<ServerUrlList>``` e ```</ServerUrlList>``` com a entrada de seu perfil baixado (azurevpnconfig.xml). Altere o FQDN "TrustedNetworkDetection" para se ajustar ao seu ambiente.
1. Abra o perfil baixado do Azure (azurevpnconfig.xml) e copie todo o conteúdo para a área de transferência, destacando o texto e pressionando (Ctrl) + C. 
1. Cole o texto copiado da etapa anterior no arquivo criado na etapa 2 entre as ```<CustomConfiguration>  </CustomConfiguration>``` marcas. Salve o arquivo com uma extensão XML.
1. Anote o valor nas ```<name>  </name>``` marcas. Este é o nome do perfil. Você precisará desse nome ao criar o perfil no Intune. Feche o arquivo e lembre-se do local onde ele foi salvo.

## <a name="create-intune-profile"></a>Criar perfil do Intune

Nesta seção, você criará um perfil de Microsoft Intune com configurações personalizadas.

1. Entre no Intune e navegue até **dispositivos – > perfis de configuração**. Selecione **+ Criar perfil**.

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="Perfis de configuração":::
1. Em **Plataforma**, selecione **Windows 10 e posteriores**. Para **perfil**, selecione **personalizado**. Em seguida, selecione **Criar**.
1. Dê um nome e uma descrição ao perfil e, em seguida, selecione **Avançar**.
1. Na guia **definições de configuração** , selecione **Adicionar**.

    * **Nome:** Insira um nome para a configuração.
    * **Descrição:** Descrição opcional.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (essas informações podem ser encontradas no arquivo de azurevpnconfig.xml na <name> </name> marca).
    * **Tipo de dados:** Cadeia de caracteres (arquivo XML).

   Selecione o ícone de pasta e escolha o arquivo que você salvou na etapa 6 nas etapas de [XML](#xml) . Selecione **Adicionar**.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="Perfis de configuração" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. Selecione **Avançar**.
1. Em **atribuições**, selecione o grupo para o qual você deseja enviar a configuração por push. Em seguida, selecione **Avançar**.
1. As regras de aplicabilidade são opcionais. Defina todas as regras, se necessário, e selecione **Avançar**.
1. Na página **revisar + criar** , selecione **criar**.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="Perfis de configuração":::
1. Seu perfil personalizado agora é criado. Para as Microsoft Intune etapas para implantar esse perfil, consulte [atribuir perfis de usuário e de dispositivo](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign).
 
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre conexões ponto a site, confira [Sobre ponto a site](point-to-site-about.md).
