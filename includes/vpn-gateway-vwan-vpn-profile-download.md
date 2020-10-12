---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87515336"
---
## <a name="1-download-the-file"></a>1. Baixar o arquivo

Execute os seguintes comandos: Copie a URL do resultado no navegador para baixar o arquivo zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrair o arquivo zip

Extraia o arquivo zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genérico
* OpenVPN (se você tiver habilitado o OpenVPN com o **certificado do Azure** ou com as configurações de **autenticação RADIUS** no gateway). Para o Gateway de VPN, consulte [Criar um locatário](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Para a WAN Virtual, consulte [Criar um locatário — VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperar informações

Na pasta **AzureVPN**, navegue até o arquivo ***azurevpnconfig.xml*** e abra-o com o Bloco de notas. Anote o texto entre as marcas a seguir.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Ao adicionar uma conexão, use as informações coletadas na etapa anterior na página de detalhes do perfil. Os campos correspondem às seguintes informações:

   * **Público:** Identifica o recurso de destinatário ao qual o token é destinado
   * **Emissor:** Identifica o STS (serviço de token de segurança) que emitiu o token e o locatário do Azure AD
   * **Locatário:** Contém um identificador exclusivo e imutável do locatário do diretório que emitiu o token
   * **FQDN:** O FQDN (nome de domínio totalmente qualificado) no gateway de VPN do Azure
   * **ServerSecret:** A chave pré-compartilhada do gateway de VPN

## <a name="folder-contents"></a>Conteúdo da pasta

* O **pasta genérica** contém o certificado do servidor público e o arquivo VpnSettings.xml. O arquivo VpnSettings.xml contém as informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter as pastas **WindowsAmd64** e **WindowsX86**. Essas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Você precisa de direitos de administrador no cliente para instalá-los.
