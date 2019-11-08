---
title: Web Analytics
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d47a67be157f71a30a6ae155790ed78a78fd6743
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814657"
---
<a name="web-analytics"></a>Web Analytics
=============

Este artigo fornece instruções para aprender sobre o Web Analytics e usá-lo para melhor desenvolver seus negócios. No momento, essa guia Insights está disponível para qualquer oferta do AppSource.

Agora que você criou e publicou sua oferta, a próxima parte da sua jornada é controlar e medir seu sucesso. Com o **Web Analytics**, adicionamos a capacidade de ver exatamente como cada uma das ofertas está funcionando no Marketplace. Para iniciar sua jornada, navegue até a página de Insights no lado esquerdo do Portal do Cloud Partner para ver a nova guia de Análise.

![Página WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Você verá um dashboard avançado para a ID do Editor que foi criada com o Microsoft Power BI e que permite que você veja os dados de cada uma das suas ofertas, que são atualizados diariamente.

<a name="microsoft-campaigns"></a>**Campanhas da Microsoft**
-----------------------

Para ampliar suas ofertas e acompanhar o crescimento delas, habilitamos a capacidade de usar **campanhas da Microsoft** no Portal do Cloud Partner. Campanhas é um recurso que se tornou compatível com o Marketplace recentemente e que permite que você acompanhe os diferentes canais que estão enviando clientes em sua página de detalhes do aplicativo.

### <a name="how-to-make-a-campaign"></a>**Como fazer uma campanha**

A maneira mais simples de descrever as campanhas é que você está adicionando uma palavra ou um termo personalizado à URL que conduz à página de detalhes do aplicativo no Marketplace. Bing, Google, LinkedIn e muitos outros sites incentivam você a criar um link de anúncio do site deles no site desejado.

Em geral, esses esforços ajudarão a estimular novos clientes a usar seu produto e são essenciais para medir o sucesso de como cada um dos canais está se comportando. É aí que entram as campanhas.

Há duas maneiras de gerar sua própria campanha.

1. Adicione o parâmetro de consulta **mktcmpid** à URL. Ele descreve o que é a campanha e de qual página/evento esses clientes são provenientes.

Por exemplo, você pode usar: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avançado): Use uma de nossas IDs de campanha genéricas e compatíveis na URL. Desejamos poder aceitar marcas de referência adicionais que você eventualmente precise usar, portanto, damos suporte à convenção de reconhecer automaticamente estas marcas adicionais:
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

Por exemplo, você pode usar: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Você pode optar por ter uma combinação de várias destas identificações de campanha para identificar melhor várias fontes conduzindo tráfego para a campanha, por exemplo, a proveniência do cliente (email, blog, fonte de mídia social, etc.).

Por exemplo:

1. Referenciador do boletim informativo: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Referenciador do LinkedIn: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Assegurar que as campanhas passem por todas as suas páginas**

Pode haver um cenário em que suas campanhas têm uma página intermediária para a qual você está direcionando o tráfego e que, em seguida, envia os clientes para o Marketplace. É importante passar as IDs de campanha iniciais para a URL final que você envia para o Marketplace.

Aqui está um exemplo:

1. Funcionário de marketing compra anúncios do Google para direcionar o tráfego para a página de aterrissagem \' da empresa. Esta página de aterrissagem tem um link \"experimente meu produto\" que leva para <https://appsource.com>.
2. Um usuário clica no anúncio e chega à página de aterrissagem de sua empresa.
    1.  URL de referência = google.com
    2.  URL da página de aterrissagem = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. O usuário clica em no link \"experimente meu produto\" e entra no AppSource.
    1. URL de referência = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL da página de aterrissagem (**Verifique se esta URL tem utm\_campaign e utm\_source adicionados a ela**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Como avaliar o sucesso de uma campanha
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visitas da página por campanha**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Este é o detalhamento de cada uma das suas visitas diárias à página, classificadas segundo a campanha da qual elas vieram.

### <a name="conversion-rate-by-campaign"></a>**Taxa de conversão por campanha**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

De modo semelhante a como mostramos a taxa de conversão da oferta inteira, neste gráfico você pode ver o detalhamento de como está o desempenho das diferentes campanhas. Este gráfico deve ajudar a identificar a origem das campanhas com taxa de conversão mais elevada.

### <a name="distribution-by-campaign"></a>**Distribuição por campanha**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Semelhante ao modo como examinamos os domínios dos clientes, este gráfico permite que você veja a distribuição dos dados segundo a campanha pela qual os usuários estão chegando ao Marketplace. \_NoCampaign significa que o cliente não tinha uma ID de campanha na URL quando navegou para o Marketplace.

<a name="next-steps"></a>**Next Steps**
--------------

Agora que você tem a capacidade de acompanhar o sucesso da oferta, queremos encorajá-lo a criar suas próprias campanhas.

Se você tiver solicitações de recursos/perguntas, compartilhe-os por meio do link Comentários, localizado no canto superior direito.

![Comentários no Portal do Cloud Partner](./media/si-getting-started/WebAnalytics5.png)
