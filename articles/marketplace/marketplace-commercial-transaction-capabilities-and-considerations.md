---
title: Recursos de transação do marketplace comercial da Microsoft
description: Este artigo descreve as considerações de preços, cobrança, faturamento e pagamento para a opção de transação do marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 33c3cf4b9da33b15d7a7d1c14b0c3fb892b60121
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317800"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Recursos de transação do marketplace comercial

## <a name="transactions-by-publishing-option"></a>Opção de transações por publicação

O editor ou a Microsoft é responsável por gerenciar transações de licença de software para ofertas no marketplace comercial. A opção de publicação que você escolher para sua oferta determinará quem gerencia a transação. Consulte [Determinar sua opção de publicação](./determine-your-listing-type.md#choose-a-publishing-option) para obter disponibilidade e explicações de cada opção de publicação.

### <a name="list-trial-and-byol-publishing-options"></a>Opções de publicação de lista, avaliação e BYOL

Os editores com recursos de comércio existentes podem escolher opções de publicação de lista, avaliação e BYOL (traga sua própria licença) para fins de aquisição promocional e de usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhum valor de transação associado. Os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros, pedido, atendimento, medição, faturamento, faturamento, pagamento e cobrança. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

A opção de publicação de transação aproveita os recursos comerciais da Microsoft e fornece uma experiência de ponta a ponta desde a descoberta e a avaliação até a compra e a implementação. As ofertas de transação são cobradas em uma assinatura da Microsoft existente ou em um cartão de crédito, permitindo que a Microsoft hospede transações do marketplace na nuvem em nome do editor.

Escolha a opção de transação ao criar uma nova oferta no Partner Center. Na página **Oferecer instalação** em **Detalhes da instalação**, selecione “Sim, eu gostaria de vender pela Microsoft e ter transações de host da Microsoft em meu nome”. Esta opção será mostrada somente se a transação estiver disponível para seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral da transação

Ao usar a opção de publicação de transação, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O editor deve considerar a cobrança de valores de infraestrutura e seus próprios valores de licenciamento de software ao selecionar um modelo de cobrança e um tipo de oferta.

Atualmente, a opção de publicação de transação tem suporte para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicativos do Azure
- Aplicativos SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

Para **máquinas virtuais** e **aplicativos do Azure**, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. Os valores de uso da infraestrutura são precificados e apresentados separadamente dos valores de licenciamento do provedor de software na fatura do cliente.

Para **Aplicativos SaaS**, você como editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único.  Ele é representado como um valor fixo para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação usada, os valores de licença de software são os seguintes:

- **Gratuito** – sem custos para as licenças de software.
- **Traga sua própria licença** (BYOL) – todas as cobranças aplicáveis por licenças de software são gerenciadas diretamente entre o editor e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.
- **Pague conforme o uso** – os valores de licença de software são apresentados como uma taxa de preços por vCPU (por hora e por núcleo) com base na infraestrutura do Azure usada. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.
- **Preços de assinatura** – os valores de licença de software são apresentados como uma taxa de recorrência mensal ou anual, cobrada como um valor fixo ou por estação. Isso se aplica aos aplicativos SaaS (mensal ou anual) e somente aos aplicativos gerenciados pelo aplicativo do Azure (mensal).
- **Avaliação gratuita de software** – sem cobrança de licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação pré-paga ou assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, devoluções e estornos de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar seu software, sujeito aos termos e condições do marketplace comercial da Microsoft e do seu contrato de licenciamento de usuário final. Você deve fornecer seu próprio contrato de licenciamento de usuário final ou selecionar o [Contrato Standard](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação de transações, você pode disponibilizar uma licença de software gratuitamente por 30 ou 90 dias. Esse recurso de aplicação de desconto não inclui o custo do uso de infraestrutura do Azure orientado pelo uso da solução do parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, você pode realizar uma oferta privada e completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas têm suporte para todas as três opções de publicação de transações.

Essa opção permite um estabelecer um preço mais alto ou mais baixo do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para desconto ou adição de uma oferta premium. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio de uma lista de permissões com suas assinaturas do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

**Pré-paga** 

O Pagamento Conforme o Uso tem a seguinte estrutura de custo:

|Custo de sua licença  | US $ 1,00 por hora   |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 1,14 por hora*       |
||

Nesse cenário, a Microsoft fatura US$ 1,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US$ 1,14 por hora  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença|   US $ 0,80 por hora     |
|A Microsoft mantém 20% do seu custo de licença  |  US $ 0,20 por hora       |
|A Microsoft mantém a 100% do custo de uso do Azure | US $ 0,14 por hora |
||

**Traga sua própria licença (BYOL)**

BYOL tem a seguinte estrutura:

|Custo de sua licença  | Valor de licença negociada e faturada por você  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 0,14 por hora*       |
||

Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US $ 0,14 por hora  |
|---------|---------|
|A Microsoft mantém o custo de uso do Azure    |   US $ 0,14 por hora     |
|A Microsoft mantém 0% do seu custo de licença   |  US $ 0.00 por hora       |
||

**Assinatura de aplicativo SaaS**

Essa opção deve ser configurada para ser vendida pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual. Se você habilitar a opção **Vender pela Microsoft** para uma oferta de SaaS, você terá a seguinte estrutura de custo:

| Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
| Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
| *O cliente é cobrado pela Microsoft*    |  *US$ 100,00 por mês (o editor precisa contabilizar os custos incorridos ou de infraestrutura de passagem no valor da licença)*  |
||

Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.

Os parceiros qualificados para a redução do valor do serviço do marketplace verão um valor de transação reduzido nas ofertas de SaaS de maio de 2019 até junho de 2020.

Nesse cenário, a Microsoft cobra US$ 100,00 pela sua licença de software e paga US$ 90,00 ao editor:

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \* A Microsoft paga 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   US $80,00 por mês <br> \* US $90,00 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \* A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \* US $10,00     |

Para determinadas ofertas que você publica no mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%. Para que suas ofertas sejam qualificadas, suas ofertas devem ter sido designadas pela Microsoft como o IP do Azure co-vender incetivized. A qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do fim de cada mês civil para receber a taxa de serviço do Marketplace reduzida para o mês. A taxa reduzida de serviço do Marketplace se aplica ao IP do Azure covenda de incentivados SaaS, VMs, aplicativos gerenciados e qualquer outra oferta de IaaS transactável qualificada disponibilizada por meio do Marketplace comercial.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento** – Você pode usar o método de faturamento preferencial do cliente para entregar os valores de licença de software de assinatura ou PAYGO.

**Enterprise Agreement** – Se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, os valores de licença de software serão cobrados usando esse método de faturamento como um custo discriminado, separado dos custos de uso específicos do Azure.

**Cartões de crédito e fatura mensal** – Os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

**Créditos gratuitos e compromisso monetário** – Alguns clientes optam por pagar antecipadamente o Azure com um compromisso monetário no Enterprise Agreement ou receberam créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Faturamento e coleções** – O faturamento da licença do software do editor é apresentado usando o método de faturamento selecionado pelo cliente e segue o cronograma do faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e insights sobre a opção de publicação da transação estão disponíveis na seção Análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter mais informações e políticas legais, consulte o [Contrato do Editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponível no Partner Center).

Para obter ajuda sobre dúvidas de cobrança, entre em contato com o [suporte do editor do marketplace comercial](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact requisitos

Esta seção aborda os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta da Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- As informações financeiras obrigatórias incluem a conta de pagamento e o perfil fiscal.

Para obter mais informações sobre como configurar essas contas, consulte [gerenciar sua conta do Marketplace comercial no Partner Center](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A opção de publicação de transações está disponível apenas para uso com os seguintes tipos de oferta de mercado:

- **Máquina virtual** – selecione dentre os modelos gratuitos, traga sua própria licença ou pague conforme o uso de preços e apresente os planos definidos no nível da oferta. Na fatura do cliente do Azure, a Microsoft apresenta os valores de licença de software do editor separadamente dos valores de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são impulsionadas pelo uso do software do editor.

- **Aplicativo do Azure: modelo de solução ou aplicativo gerenciado** – deve provisionar uma ou mais máquinas virtuais e efetuar pull da soma dos preços da máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preço, em vez do preço da máquina virtual. Em alguns os casos, os valores de uso da infraestrutura do Azure são passados para o cliente separadamente dos valores de licença de software, mas no mesmo extrato de faturamento. No entanto, se você configurar uma oferta de aplicativo gerenciado para encargos de infraestrutura de ISV, os recursos do Azure serão cobrados para o Publicador e o cliente receberá uma tarifa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gerenciamento.

- **Aplicativo SaaS** -deve ser uma solução multilocatário, usar [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrá-lo com as [APIs de cumprimento de SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura do Azure é gerenciado e cobrado diretamente para você (o parceiro), portanto, você deve considerar as taxas de uso da infraestrutura do Azure e as taxas de licenciamento de software como um único item de custo. Para obter diretrizes detalhadas, consulte [criar uma nova oferta de SaaS no Marketplace comercial](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
