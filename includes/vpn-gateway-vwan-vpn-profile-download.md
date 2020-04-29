---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066206"
---
## <a name="1-download-the-file"></a>1. baixar o arquivo

Execute os seguintes comandos: Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extraia o arquivo zip

Extraia o arquivo zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genérico
* OpenVPN (se você tiver habilitado as configurações de autenticação do OpenVPN e do Azure AD no gateway. Para gateway de VPN, consulte [criar um locatário](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Para WAN virtual, consulte [criar um locatário-VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

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

* A **pasta genérico** contém o certificado do servidor público e o arquivo VpnSettings. xml. O arquivo VpnSettings. xml contém as informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter pastas **WindowsAmd64** e **WindowsX86** . Essas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Você precisa de direitos de administrador no cliente para instalá-los.