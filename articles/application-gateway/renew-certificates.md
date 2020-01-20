---
title: Renovar um certificado do Gateway do Aplicativo do Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway do aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278011"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway do Aplicativo

Em algum momento, você precisará renovar seus certificados se você tiver configurado o gateway de aplicativo para criptografia SSL.

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
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Próximos passos

Para saber como configurar o descarregamento SSL com o Gateway de Aplicativo do Azure, veja [Configurar descarregamento SSL](application-gateway-ssl-portal.md)
