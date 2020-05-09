---
title: Recursos de transação do Marketplace comercial
description: Este artigo descreve as considerações sobre preços, cobrança, faturamento e pagamento para a opção de transação do Marketplace comercial.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 4aeae69dd50e8c233a1903f6f2c7bd7795b8d7b9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857222"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Recursos de transação do Marketplace comercial

## <a name="transactions-by-publishing-option"></a>Transações por opção de publicação

O editor ou a Microsoft é responsável por gerenciar transações de licença de software para ofertas no Marketplace comercial. A opção de publicação que você escolher para sua oferta determinará quem gerencia a transação. Consulte [determinar sua opção de publicação](./determine-your-listing-type.md#choose-a-publishing-option) para obter disponibilidade e explicações de cada opção de publicação.

### <a name="list-trial-and-byol-publishing-options"></a>Opções de publicação de lista, avaliação e BYOL

Os editores com recursos de comércio existentes podem escolher opções de publicação de lista, avaliação e BYOL (traga sua própria licença) para fins de aquisição promocional e de usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhuma taxa de transação associada. Os Publicadores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros, ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

A opção Transact Publishing aproveita os recursos do Microsoft Commerce e fornece uma experiência de ponta a ponta desde a descoberta e a avaliação até a compra e a implementação. As ofertas do Transact são cobradas em uma assinatura da Microsoft existente ou em um cartão de crédito, permitindo que a Microsoft hospede transações do Marketplace de nuvem em nome do Publicador.

Você escolhe a opção Transact ao criar uma nova oferta no Partner Center. Na página **instalação da oferta** , em **detalhes da instalação**, selecione "Sim, eu gostaria de vender pela Microsoft e ter transações de host da Microsoft em meu nome". Esta opção será mostrada somente se o Transact estiver disponível para seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral do Transact

Ao usar a opção Transact Publishing, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta para a assinatura do Azure do cliente. O Publicador deve considerar a cobrança de taxas de infraestrutura e suas próprias tarifas de licenciamento de software ao selecionar um modelo de cobrança e um tipo de oferta.

Atualmente, a opção Transact Publishing tem suporte para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicativos do Azure
- Aplicativos SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

Para **máquinas virtuais e aplicativos do Azure**, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. As tarifas de uso de infraestrutura são cobradas e apresentadas separadamente das tarifas de licenciamento do provedor de software na fatura do cliente.

Para **aplicativos SaaS**, o Publicador deve considerar as tarifas de uso da infraestrutura do Azure e as taxas de licenciamento de software como um único item de custo.  Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação usada, as taxas de licença de software são as seguintes:

- **Gratuito** – sem custos para licenças de software.
- BYOL ( **traga sua própria licença** ) – quaisquer encargos aplicáveis para licenças de software são gerenciados diretamente entre o Publicador e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.
- Pré- **pago** – as taxas de licença de software são apresentadas como uma taxa de preço por hora, por núcleo (vCPU) com base na infraestrutura do Azure usada. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.
- **Preços de assinatura** – as taxas de licença de software são apresentadas como uma taxa de recorrência mensal ou anual, cobrada como uma taxa fixa ou por estação. Isso se aplica somente a aplicativos SaaS e aplicativos gerenciados pelo aplicativo do Azure.
- **Avaliação de software gratuita** – sem custos para licenças de software por 30 ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação pré-paga ou assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, devoluções e estornos de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar seu software, sujeito aos termos e condições do mercado comercial da Microsoft e do seu contrato de licenciamento de usuário final. Você deve fornecer seu próprio contrato de licenciamento de usuário final ou selecionar o [contrato padrão](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação Transact, você pode disponibilizar uma licença de software gratuita por 30 ou 90 dias. Esse recurso de descontação não inclui o custo do uso de infraestrutura do Azure orientado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, você pode realizar uma oferta privada, completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas têm suporte de todas as três opções de Transact Publishing.

Essa opção permite um preço mais alto ou mais baixo do que a oferta publicamente disponível. Ofertas privadas podem ser usadas para desconto ou adição de um Premium a uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio da listagem de branco de sua assinatura do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

**Pré-paga** 

O pagamento conforme o uso tem a seguinte estrutura de custo:

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

**BYOL (traga sua própria licença)**

O BYOL tem a seguinte estrutura de custo:

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

Essa opção deve ser configurada para vender pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual. Se você habilitar a opção **vender através da Microsoft** para uma oferta de SaaS, você terá a seguinte estrutura de custo:

| Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
| Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
| *O cliente é cobrado pela Microsoft*    |  *$100 por mês (o Publicador deve considerar os custos incorridos ou da infraestrutura de passagem na taxa de licença)*  |
||

Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.

Os parceiros qualificados para a taxa reduzida de serviço do Marketplace verão uma taxa de transação reduzida nas ofertas de SaaS de maio de 2019 até junho de 2020.

Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $90 para o Publicador:

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \*A Microsoft paga a você 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   US $80,00 por mês <br> \*$90 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \*A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \*$10     |

Para determinados produtos SaaS que você publica em nosso mercado comercial, a Microsoft reduzirá sua **taxa de serviço do Marketplace** de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%. Para que sua oferta seja qualificada, pelo menos uma de suas ofertas deve ser designada pela Microsoft como uma venda conjunta de IP de venda pronta ou de IP. Para receber essa taxa de serviço do Marketplace reduzida para o mês, a qualificação deve ser atendida pelo menos cinco dias úteis antes do final do mês do calendário anterior. A taxa reduzida de serviço do Marketplace não se aplicará a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio do Marketplace comercial. Essa taxa reduzida estará disponível para ofertas qualificadas, com encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020. Após esse período, a taxa retornará ao seu valor normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento** – você pode usar o método de faturamento preferido do cliente para fornecer assinatura ou taxas de licença de software PAYGO.

**Enterprise Agreement** – se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, suas tarifas de licença de software serão cobradas usando esse método de faturamento como um custo discriminado, separados de qualquer custo de uso específico do Azure.

**Cartões de crédito e fatura mensal** – os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

**Créditos gratuitos e compromisso monetário** – alguns clientes optam por pagar o Azure com um compromisso monetário na Enterprise Agreement ou foram fornecidos créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Cobrança e coleções** – a cobrança de licença de software do Publicador é apresentada usando o método de faturamento selecionado pelo cliente e segue a linha do tempo de faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e informações para a opção de publicação de transação estão disponíveis por meio da seção análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter mais informações e políticas legais, consulte o [contrato do editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponível no Partner Center).

Para obter ajuda sobre perguntas de cobrança, contate o [suporte do editor do Marketplace comercial](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="transact-requirements"></a>Transact requisitos

Esta seção aborda os requisitos do Transact para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção Transact Publishing, independentemente do modelo de preços da oferta.
- As informações financeiras obrigatórias incluem a conta de pagamento e o perfil de imposto.

Para obter mais informações sobre como configurar essas contas, consulte [gerenciar sua conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)).

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A opção de publicação de transações está disponível apenas para uso com os seguintes tipos de oferta de mercado:

- **Máquina virtual** – selecione dentre os modelos gratuitos, traga sua própria licença ou pague conforme o uso, e apresente os SKUs definidos no nível da oferta. Na fatura do Azure do cliente, a Microsoft apresenta as taxas de licença de software do Publicador separadamente das taxas de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são impulsionadas pelo uso do software do editor.
- **Aplicativos do Azure: modelo de solução ou aplicativo gerenciado** – deve provisionar uma ou mais máquinas virtuais e efetuar pull da soma dos preços da máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preço, em vez do preço da máquina virtual. Em alguns casos, as tarifas de uso da infraestrutura do Azure são passadas para o cliente separadamente de taxas de licença de software, mas no mesmo demonstrativo de cobrança. No entanto, se você configurar uma oferta de aplicativo gerenciado para encargos de infraestrutura de ISV, os recursos do Azure serão cobrados para o Publicador e o cliente receberá uma tarifa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gerenciamento.

## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
