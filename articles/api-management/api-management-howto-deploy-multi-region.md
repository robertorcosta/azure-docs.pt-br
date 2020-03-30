---
title: Implantar serviços de gerenciamento de API do Azure para várias regiões do Azure
titleSuffix: Azure API Management
description: Saiba como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442661"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure

O Azure API Management suporta a implantação de várias regiões, o que permite que os editores de API distribuam um único serviço de gerenciamento de API do Azure em qualquer número de regiões Azure suportadas. O recurso multi-região ajuda a reduzir a latência de solicitação percebida pelos consumidores de API geograficamente distribuídos e melhora a disponibilidade de serviços se uma região ficar offline.

Um novo serviço de gerenciamento de API do Azure contém inicialmente apenas uma [unidade][unit] em uma única região azure, a região primária. Regiões adicionais podem ser adicionadas às regiões primárias ou secundárias. Um componente gateway de gerenciamento de API é implantado em todas as regiões primárias e secundárias selecionadas. As solicitações de API recebidas são automaticamente direcionadas para a região mais próxima. Se uma região ficar offline, as solicitações de API serão automaticamente roteadas ao redor da região com falha para o próximo gateway mais próximo.

> [!NOTE]
> Apenas o componente gateway do Gerenciamento de API é implantado em todas as regiões. O componente de gerenciamento de serviços e o portal do desenvolvedor estão hospedados apenas na região Primária. Portanto, no caso da paralisação da região primária, o acesso ao portal do desenvolvedor e a capacidade de alterar a configuração (por exemplo, adicionando APIs, aplicando políticas) serão prejudicados até que a região Primária volte a funcionar. Enquanto a região principal estiver offline disponível, as regiões secundárias continuarão a servir o tráfego de API usando a configuração mais recente disponível para eles.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Implantar uma instância do serviço de Gerenciamento de API em uma nova região

> [!NOTE]
> Se você ainda não tiver criado uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][create an api management service instance].

No portal do Azure, navegue até a página **Escala e preço** da instância do serviço de Gerenciamento de API.

![Guia Escala][api-management-scale-service]

Para implantar uma nova região, clique em **+ Adicionar região** na barra de ferramentas.

![Adicionar região][api-management-add-region]

Selecione o local na lista suspensa e defina o número de unidades com o controle deslizante.

![Especificar unidades][api-management-select-location-units]

Clique em **Adicionar** para colocar sua seleção na tabela Locais.

Repita esse processo até que todos os locais estejam configurados e clique em **Salvar** na barra de ferramentas para iniciar o processo de implantação.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Excluir uma instância do serviço de Gerenciamento de API de um local

No portal do Azure, navegue até a página **Escala e preço** da instância do serviço de Gerenciamento de API.

![Guia Escala][api-management-scale-service]

Para o local em que você deseja remover, abra o menu de contexto usando o botão **...** na extremidade direita da tabela. Selecione a opção **Excluir**.

Confirme a exclusão e clique em **Salvar** para aplicar as alterações.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Rotear chamadas à API para serviços regionais de back-end

O Gerenciamento de API do Azure apresenta apenas uma URL de serviço de back-end. Mesmo que haja instâncias do Gerenciamento de API do Azure em várias regiões, o gateway de API ainda encaminhará solicitações para o mesmo serviço de back-end, que é implantado em apenas uma região. Nesse caso, o ganho de desempenho será proveniente apenas das respostas armazenadas em cache dentro do Gerenciamento de API do Azure em uma região específica para a solicitação, mas entrar em contato com o back-end em todo o mundo ainda pode causar alta latência.

Para aproveitar totalmente a distribuição geográfica do sistema, você deve ter serviços de back-end implantados nas mesmas regiões como instâncias do Gerenciamento de API do Azure. Então, usando as políticas e a propriedade `@(context.Deployment.Region)`, é possível rotear o tráfego para instâncias locais do back-end.

1. Navegue até a instância do Gerenciamento de API do Azure e clique em **APIs** no menu esquerdo.
2. Selecione a API desejada.
3. Clique em **Editor de códigos** na lista suspensa de seta no **Processamento de entrada**.

    ![Editor de códigos de API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Use a política `set-backend` combinada com as políticas condicionais `choose` para construir uma política de roteamento apropriada na seção `<inbound> </inbound>` do arquivo.

    Por exemplo, o arquivo XML abaixo funcionaria para as regiões Oeste dos EUA e Leste da Ásia:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Você também pode fazer frente aos seus serviços de back-end com [o Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)direcionar as chamadas da API para o Gerenciador de Tráfego e deixá-lo resolver o roteamento automaticamente.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Use roteamento personalizado para gateways regionais de Gerenciamento de API

A API Management encaminha as solicitações para um _gateway_ regional com base [na menor latência.](../traffic-manager/traffic-manager-routing-methods.md#performance) Embora não seja possível substituir essa configuração no Gerenciamento de API, você pode usar seu próprio Gerenciador de Tráfego com regras de roteamento personalizadas.

1. Crie seu próprio [Gerenciador de Tráfego Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Se você estiver usando um domínio personalizado, [use-o com o Gerenciador](../traffic-manager/traffic-manager-point-internet-domain.md) de Tráfego em vez do serviço de gerenciamento de API.
1. [Configure os pontos finais regionais de Gerenciamento de API no Gerenciador de Tráfego](../traffic-manager/traffic-manager-manage-endpoints.md). Os pontos finais regionais `https://<service-name>-<region>-01.regional.azure-api.net`seguem o `https://contoso-westus2-01.regional.azure-api.net`padrão de URL de, por exemplo.
1. [Configure os pontos finais regionais de gerenciamento de API no Gerenciador de Tráfego](../traffic-manager/traffic-manager-monitoring.md). Os pontos finais de status `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`regionais `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`seguem o padrão de URL de , por exemplo .
1. Especifique o método de [roteamento](../traffic-manager/traffic-manager-routing-methods.md) do Gerenciador de Tráfego.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
