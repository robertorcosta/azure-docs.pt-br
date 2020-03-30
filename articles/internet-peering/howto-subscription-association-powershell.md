---
title: Associar ASN peer à assinatura do Azure usando o PowerShell
titleSuffix: Azure
description: Associar ASN peer à assinatura do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908980"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associar ASN peer à assinatura do Azure usando o PowerShell

Antes de enviar uma solicitação de peering, você deve primeiro associar sua ASN com a assinatura do Azure usando as etapas abaixo.

Se preferir, você pode completar este guia usando o [portal](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie o PeerASN para associar seu ASN à assinatura do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registre-se para provedor de recursos de peering
Registre-se para o provedor de recursos de peering em sua assinatura usando o comando abaixo. Se você não executar isso, então os recursos do Azure necessários para configurar o peering não são acessíveis.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Você pode verificar o status de registro usando os comandos abaixo:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Aguarde *que o RegistroEstado* gire "Registrado" antes de prosseguir. Pode levar de 5 a 30 minutos depois de executar o comando.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Atualize as informações de pares associadas a esta assinatura

Abaixo está um exemplo para atualizar as informações dos pares.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Nome corresponde ao nome do recurso e pode ser qualquer coisa que você escolher. No entanto, -peerName corresponde ao nome da sua empresa e precisa estar o mais próximo possível do seu perfil PeeringDB. Note que o valor para -peerName suporta apenas caracteres a-z, A-Z e espaço.

Uma assinatura pode ter várias ASNs. Atualize as informações de peering para cada ASN. Certifique-se de que "nome" é único para cada ASN.

Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Usamos essas informações durante o registro para validar os detalhes do peer, como informações de NOC, informações de contato técnico e sua presença nas instalações de peering etc.

Observe que no lugar de **{subscriptionId}** na saída acima, o ID de assinatura real será exibido.

## <a name="view-status-of-a-peerasn"></a>Exibir status de um PeerASN

Verifique o estado de validação ASN usando o comando abaixo:

```powershell
Get-AzPeerAsn
```

Abaixo está uma resposta de exemplo:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Aguarde que o Estado de Validação gire "Aprovado" antes de enviar uma solicitação de peering. Pode levar até 12 horas para essa aprovação.

## <a name="modify-peerasn"></a>Modificar peerasn
Você pode modificar as informações de contato do NOC a qualquer momento.

Veja um exemplo:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Excluir PeerAsn
A exclusão de um PeerASN não é suportada no momento. Se você precisar excluir o PeerASN, entre em contato com [a Microsoft .](mailto:peering@microsoft.com)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um Emparelhamento direto](howto-direct-powershell.md)
* [Converter um Emparelhamento direto herdado para o recurso do Azure](howto-legacy-direct-powershell.md)
* [Criar ou modificar o peering do Exchange](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)
