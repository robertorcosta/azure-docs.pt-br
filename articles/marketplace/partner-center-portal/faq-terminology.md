---
title: Perguntas e terminologias freqüentes para as análises do Mercado Comercial no Partner Center
description: Aprenda a abordar perguntas geralmente feitas sobre análises de mercado comercial. Inclui um dicionário de dados para terminologia de análises.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d53ae01fb302382ca5bc2d31e729e153aea6c838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286074"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Perguntas e terminologia freqüentemente feitas para análises de mercado comercial

Este artigo aborda perguntas comumente feitas sobre mensagens de análise no Partner Center e também fornece um dicionário de terminologia de análise.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Esta seção fornece respostas a perguntas comumente feitas sobre **mensagens no Analytics Available Yet** no Partner Center.

**Não consigo visualizar meus dados de análise no Partner Center. Quando acuso essas páginas, vejo a mensagem abaixo. Por que isso?**

![Ainda não há dados para suas ofertas.](./media/analytics-faq-no-data.png)

Por que você pode estar recebendo esta mensagem:

- Não existem aquisições atualmente para suas ofertas publicadas no marketplace. Isso pode significar que suas ofertas estão ao vivo no mercado e ganhando visualizações dos clientes nas páginas de exibição de produtos, mas os clientes ainda não tomaram medidas para comprá-las e implantá-las.
- A publicação de sua oferta pode estar em processo e ainda não está viva. Somente ofertas ao vivo podem ser adquiridas pelos clientes. Para verificar o status de suas ofertas, consulte Visão geral no [painel Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Para obter mais informações, consulte [Painel de resumo em análises de mercado comercial](./summary-dashboard.md).
- Suas ofertas podem ser listadas como **Contact Me**, que são ofertas somente de lista e não podem ser compradas por clientes no marketplace. Embora essas ofertas gerem leads e sejam compartilhadas com você, os pedidos não são criados para essas ofertas, pois não podem ser compradas. Para verificar seu tipo de listagem de oferta, vá para a página de configuração.

**Eu sei que tenho dados de análise, mas a mensagem abaixo está aparecendo:**

![Nenhum dado para o intervalo de datada dado](./media/analytics-faq-data-range.png)

Se você está recebendo esta mensagem, isso significa que você tem dados de análise, mas não há dados para o intervalo de datas selecionado. Selecione um intervalo de datas diferente ou um intervalo de datas personalizado sustapara visualizar qualquer dado desde 2010. Para obter mais informações, consulte a seção Faixa de data do Painel de [Resumo em análises de mercado comercial](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença do Azure | Cliente, Pedido | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como Canal |
| Tipo de licença do Azure: Provedor de Soluções de Nuvem | Cliente, Pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio do Provedor de Soluções de Nuvem, que atua como revendedor.|
| Tipo de licença do Azure: Enterprise | Cliente, Pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um Contrato Enterprise, assinado diretamente com a Microsoft.|
| Tipo de licença do Azure: Enterprise por meio de Revendedor  | Cliente, Pedido | O cliente final adquire a oferta do Azure e do seu Marketplace através de um revendedor que facilita seu Contrato Empresarial com a Microsoft.|  |
| Tipo de licença do Azure: Pagamento Conforme o Uso| Cliente, Pedido | O cliente final adquire a Oferta Azure e seu Marketplace através de um contrato "Pague como Você Vai", assinado diretamente com a Microsoft.||
| Nome da instância de nuvem| Order| A Microsoft Cloud em que ocorreu uma implantação de VM.||
| Nome da instância da nuvem: Azure Global| Order| A nuvem pública global da Microsoft.|| |
| Nome da instância da nuvem: Governo Azure | Order| Nuvens microsoft específicas do governo para um dos seguintes governos: China, Alemanha ou Estados Unidos da América.| |
| Cidade do cliente| Cliente| O nome da cidade fornecido pelo cliente. A cidade poderia ser diferente da cidade na assinatura do Azure de um cliente.||
| Idioma de comunicação do cliente  | Cliente| O idioma preferido pelo cliente para comunicação.||
| Nome da empresa do cliente | Cliente, Pedido | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na Assinatura Azure de um cliente.|  |
| País/Região do cliente | Cliente, Pedido | O nome do país fornecido pelo cliente. O país pode ser diferente do país na Assinatura Azure de um cliente.|  |
| Email do cliente| Cliente| O endereço de e-mail fornecido pelo cliente final. O e-mail pode ser diferente do endereço de e-mail da Assinatura Azure de um cliente.||
| Nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na Assinatura Azure de um cliente.| |
| ID do cliente | Cliente, Pedido | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace.|  |
| Código postal do cliente  | Cliente| O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na Assinatura Azure de um cliente.| |
| Estado do cliente| Cliente| O estado (endereço) fornecido pelo cliente. O Estado poderia ser diferente do estado fornecido na Assinatura Azure de um cliente.| |
| Data da aquisição| Cliente| A primeira data em que o cliente comprou qualquer oferta publicada por você.| |
| Data da perda| Cliente| A última data em que o cliente cancelou a última de todas as ofertas compradas anteriormente.||
| É novo cliente  | Order| O valor identificará um novo cliente adquirindo uma ou mais de suas ofertas pela primeira vez (ou não). O valor será "Sim" se dentro do mesmo mês de calendário para "Data Adquirida". O valor será "Não" se o cliente tiver comprado alguma de suas ofertas antes do mês de calendário relatado. |
| É Preview SKU| Order| O valor permitirá que você saiba se você tiver marcado o SKU como "visualização". O valor será "Sim" se o SKU tiver sido marcado de acordo, e apenas as assinaturas do Azure autorizadas por você podem implantar e usar essa imagem. O valor será "Não" se o SKU não tiver sido identificado como "visualização".  |
| É o contato promocional opt in| Cliente| O valor informará se o cliente optou proativamente pelo contato promocional dos editores. Não estamos apresentando a opção aos clientes no momento; portanto, indicamos "Não" de forma generalizada. Após a implantação desse recurso, começaremos a atualizar de acordo.|
| Tipo de licença do Marketplace| Order| O método de cobrança da oferta do Marketplace.||
| Tipo de licença do Marketplace: cobrado por meio do Azure| Order| A Microsoft é seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (Cartão de crédito PAYG ou fatura Enterprise)||
| Tipo de licença do Marketplace: traga sua própria licença | Order| A VM requer uma chave de licença fornecida pelo cliente para implantar. A Microsoft não fatura clientes por listar suas ofertas desta forma através do marketplace.||
| Tipo de licença do Marketplace: gratuito| Order| A oferta está configurada para ser gratuita para todos os usuários. A Microsoft não fatura clientes pelo uso desta oferta.||
| Tipo de licença do Marketplace: Microsoft como revendedor  | Order| A Microsoft é o revendedor para esta oferta do Marketplace.|  |
| ID de assinatura de marketplace | Cliente, Pedido | O identificador exclusivo associado à Assinatura Azure que o cliente usou para comprar sua oferta de Marketplace. ID foi anteriormente o Azure Subscription GUID.||
| Nome da oferta  | Order| O nome da oferta do Marketplace.|| |
| Tipo de Oferta  | Order| O tipo de oferta do Microsoft Marketplace.|||
| Tipo de oferta: aplicativo gerenciado  | Ordem | Use o aplicativo Azure: tipo de oferta de aplicativo gerenciado quando as seguintes condições são necessárias: Você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução baseada em IaaS inteira. Você ou seu cliente exigem que a solução seja gerenciada por um parceiro. |
| Tipo de oferta: Aplicativo Azure| Ordem | Use o modelo de oferta de solução do Aplicativo Azure quando sua solução requer implantação e automação de configuração adicionais além de uma VM simples.||
| Tipo de Oferta: Serviço de Consultoria| Order| Serviços de Consultoria no Azure Marketplace ajudam a conectar os clientes aos serviços para dar suporte a aumentar seu uso do Azure.| |
| Tipo de Oferta: Container | Order| Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.||
| Tipo de Oferta: Dynamics 365 Business Central| Order| Use este tipo de oferta quando sua solução estiver integrada com a Dynamics 365 para Finanças e Operações| |
| Tipo de Oferta: Dinâmica 365 para Engajamento do Cliente | Order| Use este tipo de oferta quando sua solução estiver integrada com a Dynamics 365 para engajamento do cliente.||
| Tipo de oferta: Módulo de Borda IoT | Order| Os módulos Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou seu próprio código específico de solução. |
| Tipo de Oferta: Aplicativo Power BI | Order| Use o tipo de oferta do aplicativo Power BI quando você implantar um aplicativo integrado ao Power BI.|  |
| Tipo de Oferta: Aplicativo SaaS| Order| Use o tipo de oferta de aplicativo SaaS para permitir que o cliente compre a solução técnica baseada em SaaS como uma assinatura.||
| Tipo de Oferta: Máquina Virtual | Order| Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente.||
| Tipo de Oferta: Extensão do Visual Studio Marketplace  | Order| Tipo de oferta anteriormente disponível para desenvolvedores de extensão Do Zure DevOps. Daqui para frente, os desenvolvedores de extensão Do Azure DevOps podem vender sua extensão diretamente aos clientes. As ofertas de extensão podem ser configuradas como pagas ou incluindo um teste. |
| Data de cancelamento do pedido| Order| A data em que o pedido do Marketplace foi cancelado.||
| ID do pedido| Order| O identificador exclusivo do pedido do cliente para o seu serviço marketplace. As ofertas baseadas no uso da Máquina Virtual não estão associadas a um pedido.| |
| Data de compra do pedido| Order| A data em que o pedido do Marketplace foi criado.|||
| Status do pedido| Order| O status de um pedido do Marketplace no momento em que os dados foram atualizados pela última vez.|     |
| Status do pedido: ativo  | Order| O cliente comprou um pedido e não cancelou seu pedido.|         |
| Status do pedido: cancelado | Order| O cliente comprou anteriormente um pedido e, posteriormente, cancelou seu pedido.||
| E-mail do provedor| Cliente| O endereço de e-mail do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente é uma Empresa através do Revendedor, este será o revendedor. Se um Provedor de Soluções em Nuvem (CSP) estiver envolvido, este será o CSP.|
| Nome do Provedor| Cliente| O nome do provedor envolvido no relacionamento entre a Microsoft e o cliente final. Se o cliente é uma Empresa através do Revendedor, este será o revendedor. Se um Provedor de Soluções em Nuvem (CSP) estiver envolvido, este será o CSP.|
| SKU| Order| Nome da SKU definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode estar associada a uma única oferta.||
| Data de término da avaliação gratuita| Order| A data em que o período de avaliação gratuita deste pedido terminará ou terminou.||

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para obter gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para sua oferta, consulte [Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, consulte [Orders Dashboard em análises de mercado comercial](./orders-dashboard.md).
- Para a Máquina Virtual (VM) oferece métricas de uso e faturamento medido, consulte [O Painel de Uso em análises de mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análises de Mercado Comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O Painel de Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
