---
title: Dimensione recursos e capacidades
description: Saiba como escalar um aplicativo no Azure App Service. Obtenha mais CPU, memória, espaço em disco e recursos extras.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659892"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Dimensione um aplicativo no Azure App Service

Este artigo mostra como dimensionar seu aplicativo no Serviço de Aplicativo do Azure. Há dois fluxos de trabalho para dimensionamento, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenha mais CPU, memória, espaço em disco e recursos adicionais como VMs (máquinas virtuais) dedicadas, domínios e certificados personalizados, slots de preparação, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta o número de instâncias de VM que executam seu aplicativo.
  Você pode dimensionar até 30 instâncias, dependendo do seu nível de preços. [Ambientes do Serviço de Aplicativo](environment/intro.md) no tipo de preço **Isolado** aumentam ainda mais sua contagem de expansão para 100 instâncias. Para saber mais sobre a escala horizontal, consulte [Escalar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá, você descobre como usar o dimensionamento automático, que serve para dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos em seu [plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md).
Elas não exigem a alteração do código ou a reimplantação do aplicativo.

Para obter informações sobre os preços e recursos de planos individuais do Serviço de Aplicativo, consulte [Detalhes de Preços dos Serviços de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um Plano do Serviço de Aplicativo do tipo **Gratuito** , é necessário remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para sua Assinatura do Azure. Para exibir ou alterar as opções da sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escale verticalmente seu tipo de preço

> [!NOTE]
> Para expandir para o tipo de preço **PremiumV2**, consulte [Configurar o tipo de preço PremiumV2 para o Serviço de Aplicativo](app-service-configure-premium-tier.md).
>

1. No seu navegador, abra o [portal Azure][portal].

1. Na página do aplicativo App Service, no menu esquerdo, selecione **Scale Up (plano de serviço de aplicativo)**.
   
3. Escolha seu nível e, em seguida, **selecione Aplicar**. Selecione as diferentes categorias (por exemplo, **Produção**) e também **Consulte opções adicionais** para mostrar mais níveis.
   
    ![Navegue para escalar verticalmente seu aplicativo do Azure.][ChooseWHP]

    Quando a operação estiver concluída, você verá um pop-up de notificação com uma marca de verificação de sucesso verde.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Escalar recursos relacionados
Se o seu aplicativo depender de outros serviços, como o Banco de Dados SQL do Azure ou o Armazenamento do Azure, você poderá expandir esses recursos separadamente. Esses recursos não são gerenciados pelo Plano do Serviço de Aplicativo.

1. Na **página Visão geral** do seu aplicativo, selecione o link **de grupo de recursos.**
   
    ![Escale verticalmente os recursos relacionados de seu aplicativo do Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **Resumo** da página **do grupo Recurso,** selecione um recurso que deseja dimensionar. A captura de tela a seguir mostra um recurso do Banco de Dados SQL.
   
    ![Navegue até a página do grupo de recursos para expandir seu aplicativo do Azure](./media/web-sites-scale/ResourceGroup.png)

    Para dimensionar o recurso relacionado, consulte a documentação para o tipo de recurso específico. Por exemplo, para escalar um único banco de dados SQL, consulte [Dimensionar recursos de banco de dados único sql](../sql-database/sql-database-single-database-scale.md). Para aumentar um banco de dados Azure para recursos MySQL, consulte [Dimension mysql resources](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, tais como os tamanhos de VM para cada tipo de preço, consulte [Detalhes de preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service).

Para uma tabela de limites de serviço, cotas, restrições e recursos com suporte em cada tipo de preço, consulte [Limites do Serviço de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

[Escalonar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Configure o nível PremiumV2 para serviço de aplicativo](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
