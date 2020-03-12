---
title: 'Gateway de VPN do Azure: sobre perfis de cliente VPN P2S'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 762f62fa0901672c447da42f416e5b003e7419b2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127302"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre perfis de cliente VPN P2S

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo o ajudará a obter e entender as informações necessárias para um perfil de cliente VPN.

## <a name="1-download-the-file"></a>1. baixar o arquivo

Execute os seguintes comandos: Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extraia o arquivo zip

Extraia o arquivo zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genéricos
* OpenVPN (se você tiver habilitado as configurações de autenticação do OpenVPN e do Azure AD no gateway. Consulte [criar um locatário](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. recuperar informações

Na pasta **AzureVPN** , navegue até o arquivo ***azurevpnconfig. xml*** e abra-o com o bloco de notas. Anote o texto entre as marcas a seguir.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Ao adicionar uma conexão, use as informações coletadas na etapa anterior para a página de detalhes do perfil. Os campos correspondem às seguintes informações:

   * **Público-alvo:** Identifica o recurso de destinatário para o qual o token se destina
   * **Emissor:** Identifica o serviço de token de segurança (STS) que emitiu o token, bem como o locatário do Azure AD
   * **Locatário:** Contém um identificador exclusivo e imutável do locatário do diretório que emitiu o token
   * **FQDN:** O FQDN (nome de domínio totalmente qualificado) no gateway de VPN do Azure
   * **ServerSecret:** A chave pré-compartilhada de gateway de VPN

## <a name="folder-contents"></a>Conteúdo da pasta

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows). Essa pasta não estará presente no arquivo zip se a autenticação do Azure AD estiver selecionada no gateway de VPN. Em vez disso, o azurevpnconfig. xml estará na pasta AzureVPN.

* A **pasta genérico** contém o certificado do servidor público e o arquivo VpnSettings. xml. O arquivo VpnSettings. xml contém as informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter pastas **WindowsAmd64** e **WindowsX86** . Essas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Você precisa de direitos de administrador no cliente para instalá-los.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para obter mais informações sobre ponto a site, consulte [sobre ponto a site](point-to-site-about.md).
