---
title: Gerenciar uma conexão private endpoint no Azure
description: Saiba como gerenciar conexões de ponto final privado no Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452972"
---
# <a name="manage-a-private-endpoint-connection"></a>Gerenciar uma conexão de ponto de extremidade privado
O Azure Private Link funciona em um modelo de fluxo de chamada de aprovação no qual o consumidor do serviço Private Link pode solicitar uma conexão com o provedor de serviços para consumir o serviço. O provedor de serviços pode então decidir se permite que o consumidor se conecte ou não. O Azure Private Link permite que os provedores de serviços gerenciem a conexão de ponto final privado em seus recursos. Este artigo fornece instruções sobre como gerenciar as conexões private endpoint.

![Gerenciar pontos finais privados](media/manage-private-endpoint/manage-private-endpoint.png)

Existem dois métodos de aprovação de conexão que um consumidor de serviço Private Link pode escolher:
- **Automático**: Se o consumidor de serviço tiver permissões RBAC no recurso do prestador de serviços, o consumidor poderá escolher o método de aprovação automática. Neste caso, quando a solicitação chega ao recurso do prestador de serviços, nenhuma ação é necessária do prestador de serviços e a conexão é automaticamente aprovada. 
- **Manual**: Ao contrário, se o consumidor de serviço não tiver permissões rbac no recurso do prestador de serviços, o consumidor pode escolher o método de aprovação manual. Neste caso, a solicitação de conexão aparece nos recursos do serviço como **Pendentes**. O provedor de serviços tem que aprovar manualmente a solicitação antes que as conexões possam ser estabelecidas. Em casos manuais, o consumidor de serviços também pode especificar uma mensagem com a solicitação para fornecer mais contexto ao prestador de serviços. O provedor de serviços tem as seguintes opções para escolher para todas as conexões private endpoint: **Aprovado,** **Rejeitado**, **Remover**.

A tabela abaixo mostra as várias ações do provedor de serviços e os estados de conexão resultantes para Private Endpoints.  O provedor de serviços também pode alterar o estado de conexão da conexão de ponto final privado posteriormente sem intervenção do consumidor. A ação atualizará o estado do ponto final no lado do consumidor. 


|Ação do provedor de serviços   |Estado de ponto final privado do consumidor de serviços   |Descrição   |
|---------|---------|---------|
|Nenhum    |    Pendente     |    A conexão é criada manualmente e está pendente de aprovação pelo proprietário do recurso Private Link.       |
|Aprovar    |  Aprovado       |  A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.     |
|Rejeitar     | Rejeitado        | A conexão foi rejeitada pelo proprietário do recurso do link privado.        |
|Remover    |  Desconectado       | A conexão foi removida pelo proprietário do recurso de link privado, o ponto final privado torna-se informativo e deve ser excluído para limpeza.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Gerenciar conexões de ponto de extremidade privado nos recursos de PaaS do Azure
Portal é o método preferido para gerenciar conexões de ponto final privado nos recursos do Azure PaaS. Atualmente, não temos suporte ao PowerShell/CLI para gerenciar conexões com recursos do Azure PaaS.
1. Entre no Portal do Azure em https://portal.azure.com.
2. Navegue até o Private Link Center.
3. Em **Resources,** selecione o tipo de recurso que deseja gerenciar as conexões de ponto final privado.
4. Para cada um de seus tipos de recursos, você pode visualizar o número de conexões de ponto final privado associadas a ele. Você pode filtrar os recursos conforme necessário.
5. Selecione as conexões de ponto final privado.  Nas conexões listadas, selecione a conexão que deseja gerenciar. 
6. Você pode alterar o estado da conexão selecionando entre as opções na parte superior.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gerenciar conexões de ponto final privado em um serviço de Private Link de propriedade do cliente/parceiro

O Azure PowerShell e o Azure CLI são os métodos preferidos para gerenciar conexões privadas endpoint em Serviços de Parceiros Microsoft ou serviços de propriedade do cliente. Atualmente, não temos nenhum suporte de portal para gerenciar conexões em um serviço de Private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Use os seguintes comandos PowerShell para gerenciar conexões de ponto final privados.  
#### <a name="get-private-link-connection-states"></a>Obtenha estados de conexão Private Link 
Use `Get-AzPrivateLinkService` o cmdlet para obter as conexões Private Endpoint e seus estados.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprove uma conexão private endpoint 
 
Use `Approve-AzPrivateEndpointConnection` o cmdlet para aprovar uma conexão Private Endpoint. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Negar conexão private endpoint 
 
Use `Deny-AzPrivateEndpointConnection` o cmdlet para rejeitar uma conexão Private Endpoint. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Remover conexão de ponto final privado 
 
Use `Remove-AzPrivateEndpointConnection` o cmdlet para remover uma conexão Private Endpoint. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Use `az network private-link-service update` para gerenciar suas conexões private endpoint. O estado de conexão ```azurecli connection-status``` é especificado no parâmetro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre pontos de final privados](private-endpoint-overview.md)
 
