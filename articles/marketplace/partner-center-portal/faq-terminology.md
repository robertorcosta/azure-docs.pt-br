---
title: Perguntas frequentes e terminologia de análise do marketplace comercial – Partner Center
description: Obtenha respostas para perguntas frequentes sobre a análise do marketplace comercial no Partner Center. Este artigo inclui um dicionário de dados da terminologia de análise.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: eb91e498fa757c5cec7bd466c60aaf7e8758304c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744575"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologia e perguntas comuns sobre análise do marketplace comercial

Este artigo aborda as perguntas mais frequentes sobre as mensagens de análise no Partner Center e também fornece um dicionário de terminologia de análise.

## <a name="common-questions"></a>Perguntas comuns

**Não consigo exibir meus dados de análise no Partner Center. Quando acesso essas páginas, vejo a mensagem abaixo. Por que é isso?**

![Não há dados para suas ofertas ainda](./media/analytics-faq-no-data.png)

Por que você está recebendo essa mensagem:

- No momento, não existem aquisições das suas ofertas publicadas no marketplace. Isso pode significar que suas ofertas estão ativas no marketplace e obtendo visualizações de clientes nas páginas de exibição do produto, mas os clientes ainda não compraram nem implantaram.
- A publicação da sua oferta pode estar em processo e ainda não está ativa. Somente as ofertas ativas podem ser adquiridas pelos clientes. Para verificar o status das suas ofertas, consulte Visão geral no [painel Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Para obter mais informações, confira [Noções básicas sobre o painel de resumo na análise do marketplace comercial](./summary-dashboard.md)
- Suas ofertas podem estar listadas como **Entrar em contato comigo**, que são ofertas somente de lista e não podem ser adquiridas por clientes no marketplace. Embora essas ofertas gerem leads e sejam compartilhadas com você, não é possível fazer pedidos, pois elas não podem ser adquiridas. Para verificar o tipo de listagem da oferta, acesse a página de configuração.

**Sei que tenho dados de análise, mas a mensagem abaixo está aparecendo:**

![Não há dados para o intervalo de datas fornecido](./media/analytics-faq-data-range.png)

Se estiver recebendo esta mensagem, significa que você tem dados de análise, mas não para o intervalo de datas selecionado. Selecione um intervalo de datas diferente ou um intervalo de datas personalizado para exibir os dados desde 2010. Para obter mais informações, consulte a seção Intervalo de datas do [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md)

## <a name="dictionary-of-data-terms"></a>Termos do dicionário de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença do Azure | Cliente, pedido | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como Canal |
| Tipo de licença do Azure: Provedor de soluções de nuvem | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio do Provedor de Soluções de Nuvem, que atua como revendedor.|
| Tipo de licença do Azure: Enterprise | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um Contrato Enterprise, assinado diretamente com a Microsoft.|
| Tipo de licença do Azure: Enterprise por meio do revendedor  | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um revendedor que facilita seu Contrato Enterprise com a Microsoft.|  |
| Tipo de licença do Azure: Pré-pago| Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um contrato “Pré-pago”, assinado diretamente com a Microsoft.||
| Nome da instância de nuvem| Order| A Microsoft Cloud em que ocorreu uma implantação de VM.||
| Nome da instância da nuvem: Azure Global| Order| A nuvem pública global da Microsoft.|| |
| Nome da instância da nuvem: Azure Government | Order| Nuvens da Microsoft específicas do governo para um dos seguintes governos: China, Alemanha ou Estados Unidos da América.| |
| Cidade do cliente| Cliente| O nome da cidade fornecido pelo cliente. A cidade pode ser diferente da cidade na Assinatura do Azure de um cliente.||
| Idioma de comunicação do cliente  | Cliente| O idioma preferido pelo cliente para comunicação.||
| Nome da empresa do cliente | Cliente, pedido | O nome da empresa fornecido pelo cliente. O nome pode ser diferente do nome na Assinatura do Azure de um cliente.|  |
| País/região do cliente | Cliente, pedido | O nome do país/região fornecido pelo cliente. O país/região pode ser diferente do país/região na Assinatura do Azure de um cliente.|  |
| Email do cliente| Cliente| O endereço de email fornecido pelo cliente final. O email pode ser diferente do endereço de email na Assinatura do Azure de um cliente.||
| Nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na Assinatura do Azure de um cliente.| |
| ID do cliente | Cliente, pedido | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace.|  |
| Código postal do cliente  | Cliente| O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na Assinatura do Azure de um cliente.| |
| Estado do cliente| Cliente| O estado (endereço) fornecido pelo cliente. O estado pode ser diferente do estado fornecido na Assinatura do Azure de um cliente.| |
| Data da aquisição| Cliente| A primeira data em que o cliente comprou qualquer oferta publicada por você.| |
| Data da perda| Cliente| A última data em que o cliente cancelou a última de todas as ofertas adquiridas anteriormente.||
| É cliente novo  | Order| O valor identificará um novo cliente adquirindo uma ou mais das suas ofertas pela primeira vez (ou não). O valor será "Sim" se estiver no mesmo mês do calendário que a "data de aquisição". O valor será "não" se o cliente tiver comprado uma das suas ofertas antes do mês do calendário informado. |
| É SKU de versão prévia| Order| O valor informará se você marcou o SKU como "versão prévia". O valor será "Sim" se o SKU tiver sido devidamente marcado e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem. O valor será "Não" se o SKU não tiver sido identificado como "versão prévia".  |
| Aceitou contato promocional| Cliente| O valor indicará que o cliente aceitou proativamente o contato promocional dos fornecedores. Não estamos apresentando a opção aos clientes no momento; portanto, indicamos "Não" de forma generalizada. Após a implantação desse recurso, começaremos a atualizar de acordo.|
| Tipo de licença do Marketplace| Order| O método de cobrança da oferta do Marketplace.||
| Tipo de licença do Marketplace: Cobrado por meio do Azure| Order| A Microsoft é seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (Cartão de crédito PAYG ou fatura Enterprise)||
| Tipo de licença do Marketplace: Traga sua própria licença | Order| A VM requer uma chave de licença fornecida pelo cliente para ser implantada. A Microsoft não cobra dos clientes as listagens das ofertas dessa maneira por meio do marketplace.||
| Tipo de licença do Marketplace: Grátis| Order| A oferta está configurada como gratuita para todos os usuários. A Microsoft não cobra dos clientes o uso desta oferta.||
| Tipo de licença do Marketplace: Microsoft como revendedor  | Order| A Microsoft é o revendedor para esta oferta do Marketplace.|  |
| ID da assinatura do Marketplace | Cliente, pedido | O identificador exclusivo da assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace. Anteriormente, a identificação era o GUID da assinatura do Azure.||
| Nome da oferta  | Order| O nome da oferta do Marketplace.|| |
| Tipo de Oferta  | Order| O tipo de oferta do Microsoft Marketplace.|||
| Tipo de oferta: Aplicativo gerenciado  | Order, | Use o aplicativo do Azure: tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias: Você pode implantar uma solução baseada em assinatura para o cliente usando uma VM ou uma solução inteira com base em IaaS. Você ou seu cliente exige que a solução seja gerenciada por um parceiro. |
| Tipo de oferta: Aplicativo do Azure| Order, | Use o tipo de oferta Aplicativo Azure: modelo de solução quando a solução exigir mais automação de implantação e configuração do que uma VM simples.||
| Tipo de oferta: Serviço de consultoria| Order| Serviços de Consultoria no Azure Marketplace ajudam a conectar os clientes aos serviços para dar suporte a aumentar seu uso do Azure.| |
| Tipo de oferta: Contêiner | Order| Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.||
| Tipo de oferta: Dynamics 365 Business Central| Order| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 for Finance and Operations| |
| Tipo de oferta: Dynamics 365 for Customer Engagement | Order| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 for Customer Engagement.||
| Tipo de oferta: Módulo do IoT Edge | Order| Os módulos do Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o código específico da sua própria solução. |
| Tipo de oferta: Aplicativo do Power BI | Order| Use o tipo de oferta Aplicativo do Power BI ao implantar um aplicativo integrado com Power BI.|  |
| Tipo de oferta: Aplicativo SaaS| Order| Use o tipo de oferta de aplicativo SaaS para permitir que o cliente compre a solução técnica baseada em SaaS como uma assinatura.||
| Tipo de oferta: Máquina Virtual | Order| Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente.||
| Tipo de oferta: Extensão do Visual Studio Marketplace  | Order| Tipo de oferta disponível anteriormente para os desenvolvedores de extensão do Azure DevOps. De agora em diante, os desenvolvedores de extensão do Azure DevOps poderão vender suas extensões diretamente para os clientes. As ofertas de extensão podem ser configuradas como pagas ou incluir uma avaliação gratuita. |
| Data de cancelamento do pedido| Order| A data em que o pedido do Marketplace foi cancelado.||
| ID do pedido| Order| O identificador exclusivo do pedido do cliente para o serviço do Marketplace. As ofertas baseadas no uso da máquina virtual não estão associadas a um pedido.| |
| Data de compra do pedido| Order| A data em que o pedido do Marketplace foi criado.|||
| Status do pedido| Order| O status de um pedido do Marketplace no momento em que os dados foram atualizados pela última vez.|     |
| Status do pedido: Ativo  | Order| O cliente fez um pedido e não o cancelou.|         |
| Status do pedido: cancelado | Order| O cliente fez um pedido e, depois, o cancelou.||
| Email do provedor| Cliente| O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| Nome do Provedor| Cliente| O nome do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| SKU| Order| Nome do SKU definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode estar associado a uma única oferta.||
| Data de término da avaliação gratuita| Order| A data em que o período de avaliação gratuita deste pedido terminará ou terminou.||

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no marketplace comercial do Partner Center, confira [Análise para o marketplace comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre os pedidos em um formato gráfico para download, confira [Painel de pedidos na análise do marketplace comercial](./orders-dashboard.md).
- Para as métricas de cobrança de uso e de medição de VM (máquina virtual), confira [Painel de Uso em análises de marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes sobre as ofertas no Azure Marketplace e no AppSource, confira [Painel de classificações e revisões na análise do marketplace comercial](./ratings-reviews.md).
