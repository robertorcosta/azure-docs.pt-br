---
title: Renovar um certificado do Gateway do Aplicativo do Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway do aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311957"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway do Aplicativo

Em algum momento, você precisará renovar seus certificados se tiver configurado o gateway de aplicativo para criptografia TLS/SSL.

Você pode renovar um certificado associado a um ouvinte usando o portal do Azure, o Azure PowerShell ou a CLI do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvinte de gateway do aplicativo. Clique no ouvinte que tem um certificado que precisa ser renovado e, em seguida, clique em **Renovar ou editar o certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Carregue o novo certificado PFX, atribua um nome, digite a senha e, em seguida, clique em **Salvar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar seu certificado usando o Azure PowerShell, use o seguinte script:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento de TLS com o gateway de Aplicativo Azure, consulte [Configurar o descarregamento de TLS](application-gateway-ssl-portal.md)
