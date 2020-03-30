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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066206"
---
## <a name="1-download-the-file"></a>1. Baixe o arquivo

Execute os seguintes comandos: Copie a URL de resultado para o seu navegador, a fim de baixar o arquivo zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrair o arquivo zip

Extraia o arquivo zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genérico
* OpenVPN (Se você habilitou as configurações de autenticação OpenVPN e Azure AD no gateway. Para vpn Gateway, consulte [Criar um inquilino](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Para WAN Virtual, consulte [Criar um inquilino - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperar informações

Na pasta **AzureVPN,** navegue até o arquivo ***azurevpnconfig.xml*** e abra-o com o Bloco de Notas. Anote o texto entre as seguintes tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Ao adicionar uma conexão, use as informações coletadas na etapa anterior para a página de detalhes do perfil. Os campos correspondem às seguintes informações:

   * **Audiência:** Identifica o recurso destinatário para o qual o token é destinado
   * **Emissor:** Identifica o Serviço de Token de Segurança (STS) que emitiu o token, bem como o inquilino Azure AD
   * **Inquilino:** Contém um identificador imutável e único do inquilino do diretório que emitiu o token
   * **FQDN:** O nome de domínio totalmente qualificado (FQDN) no gateway Azure VPN
   * **ServerSecret:** A chave pré-compartilhada do gateway VPN

## <a name="folder-contents"></a>Conteúdo da pasta

* A **pasta genérica** contém o certificado de servidor público e o arquivo VpnSettings.xml. O arquivo VpnSettings.xml contém informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter pastas **WindowsAmd64** e **WindowsX86.** Essas pastas contêm o instalador para clientes SSTP e IKEv2 para clientes Windows. Você precisa de direitos de admin sobre o cliente para instalá-los.