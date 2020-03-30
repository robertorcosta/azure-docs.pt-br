---
title: Azure ExpressRoute CrossConnnections Desenvolvimento e integração da API
description: Este artigo fornece uma visão geral detalhada para os parceiros expressRoute sobre o tipo de recurso expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187019"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Desenvolvimento e integração da API expressRoute CrossConnnections

A API do ExpressRoute Partner Resource Manager permite que os parceiros ExpressRoute gerenciem a configuração de camada 2 e camada 3 dos circuitos ExpressRoute do cliente. A API do ExpressRoute Partner Resource Manager introduz um novo tipo de recurso, **expressRouteCrossConnections**. Os parceiros usam esse recurso para gerenciar circuitos ExpressRoute do cliente.

## <a name="workflow"></a>Fluxo de trabalho

O recurso expressRouteCrossConnections é um recurso de sombra para o circuito ExpressRoute. Quando um cliente do Azure cria um circuito ExpressRoute e seleciona um parceiro ExpressRoute específico, a Microsoft cria um recurso expressRouteCrossConnections na assinatura de gerenciamento do Azure ExpressRoute do parceiro. Ao fazer isso, a Microsoft define um grupo de recursos para criar o recurso expressRouteCrossConnections. O padrão de nomeação para o grupo de recursos é **CrossConnection-* PeeringLocation***; onde PeeringLocation = o Local da Rota Expressa. Por exemplo, se um cliente criar um circuito ExpressRoute em Denver, o CrossConnection será criado na assinatura do Parceiro Azure no seguinte grupo de recursos: **CrossConnnection-Denver**.

Os parceiros ExpressRoute gerenciam a configuração de camada 2 e camada 3, emitindo operações REST contra o recurso expressRouteCrossConnections.

## <a name="benefits"></a>Benefícios

Benefícios da mudança para o recurso expressRouteCrossConnections:

* Quaisquer melhorias futuras para parceiros ExpressRoute serão disponibilizadas no recurso ExpressRouteCrossConnection.

* Os parceiros podem aplicar o [Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/overview) ao recurso expressRouteCrossConnection. Esses controles podem definir permissões para as quais as contas dos usuários podem modificar o recurso expressRouteCrossConnection e adicionar/atualizar/excluir configurações de peering.

* O recurso expressRouteCrossConnection expõe APIs que podem ser úteis na solução de problemas de conexões ExpressRoute. Isso inclui tabela ARP, resumo da tabela de rota bgp e detalhes da tabela de rota bgp. Esse recurso não é suportado por APIs clássicas de implantação.

* Os parceiros também podem procurar as comunidades anunciadas no peering da Microsoft usando o recurso *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Etapas de desenvolvimento e integração de API

Para desenvolver contra a API parceira, os parceiros ExpressRoute aproveitam a configuração de um cliente de teste e parceiro de teste. A configuração do cliente de teste será usada para criar circuitos ExpressRoute em locais de teste que mapeiam para dispositivos e portas manenetas. A configuração do parceiro de teste é usada para gerenciar os circuitos ExpressRoute criados no local de peering de teste.

### <a name="1-enlist-subscriptions"></a>1. Aliste assinaturas

Para solicitar o parceiro de teste e testar a configuração do cliente, inscreva duas assinaturas do Pay-As-You-Go Azure para o seu contato de engenharia do ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Esta assinatura será usada para gerenciar circuitos ExpressRoute criados em locais de peering de teste em dispositivos e portas manenetas.

* **ExpressRoute_API_Dev_Customer_Sub:** Esta assinatura será usada para criar circuitos ExpressRoute em locais de teste que mapeiam para dispositivos e portas manenetas.

Os locais de peering de teste: dispositivos e portas manenetas não são expostos aos clientes de produção por padrão. Para criar circuitos ExpressRoute que mapeiem a configuração do teste, um sinalizador de recurso de assinatura precisa ser ativado.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registre a assinatura Dev_Provider para acessar a API expressRouteCrossConnections

Para acessar a API expressRouteCrossConnections, a assinatura do parceiro precisa ser inscrita no **Provedor de Recursos Microsoft.Network**. Siga as etapas dos [provedores de recursos do Azure e digite](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) artigo para concluir o processo de registro.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Configurar a autenticação para chamadas de API rest do Azure Resource Manager

A maioria dos serviços do Azure exige que o código do cliente seja autenticado com o Resource Manager, usando credenciais válidas, antes de chamar APIs de serviço. A autenticação é coordenada entre os vários atores pelo Azure AD e fornece ao cliente um token de acesso como prova de autenticação.

O processo de autenticação envolve duas etapas principais:

1. [Registre o cliente.](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)
2. [Crie a solicitação de acesso](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Forneça permissão do Contribuinte de Rede ao aplicativo do cliente

Uma vez configurada a autenticação com sucesso, você precisa conceder acesso ao Contribuinte de Rede ao seu aplicativo cliente, a Dev_Provider_Sub. Para conceder permissão, entre no [portal do Azure](https://ms.portal.azure.com/#home) e complete as seguintes etapas:

1. Navegue até Assinaturas e selecione o Dev_Provider_Sub
2. Navegue até o Controle de Acesso (IAM)
3. Adicionar atribuição de função
4. Selecione a função de contribuinte da rede
5. Atribuir acesso ao usuário, grupo ou principal de serviço do Azure AD
6. Selecione seu aplicativo cliente
7. Salvar alterações

### <a name="5-develop"></a>5. Desenvolver

Desenvolver contra a [API expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Consulte [a API Rest Connections Do ExpressRoute CrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) para obter documentação da API REST.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre todas as APIs ExpressRoute REST, consulte [APIs ExpressRoute REST](https://docs.microsoft.com/rest/api/expressroute/).