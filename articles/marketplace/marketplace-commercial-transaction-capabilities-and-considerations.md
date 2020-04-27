---
title: Os recursos do Microsoft Commercial Marketplace Transact
description: Este artigo descreve as considerações sobre preços, cobrança, faturamento e pagamento para a opção Transact do Microsoft Commercial Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: d3e3afecc441a010ff28ef8970a1da7b1e99ffc6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160521"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Recursos de transação do Marketplace comercial

Este artigo aborda os seguintes tópicos relacionados ao commerce para o Marketplace comercial da Microsoft:

* Responsabilidade de transação para diferentes opções de publicação
* Visão geral de transações
* Transact modelos de cobrança
* Transact requisitos

## <a name="transactions-by-publishing-option"></a>Transações por opção de publicação

O editor ou a Microsoft é responsável por gerenciar transações de licença de software para ofertas no Marketplace comercial. A opção de publicação que você escolher para sua oferta determinará quem gerencia a transação. Consulte [determinar sua opção de publicação](./determine-your-listing-type.md#choose-a-publishing-option) para obter disponibilidade e explicações de cada opção de publicação.

### <a name="list-trial-and-byol-publishing-options"></a>Opções de publicação de lista, avaliação e BYOL

Os editores com recursos de comércio existentes podem escolher opções de publicação de lista, avaliação e BYOL (traga sua própria licença) para fins de aquisição promocional e de usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhuma taxa de transação associada. Os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: pedido, atendimento, medição, faturamento, faturamento, pagamento e cobrança. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

A opção Transact Publishing aproveita os recursos do Microsoft Commerce e fornece uma experiência de ponta a ponta da descoberta e da avaliação à compra e à implementação. As ofertas do Transact são cobradas em uma assinatura da Microsoft existente ou em um cartão de crédito, permitindo que a Microsoft hospede transações do Marketplace de nuvem em nome do Publicador.

Você escolhe a opção Transact ao criar uma nova oferta no Partner Center. Na página **instalação da oferta** , em **detalhes da instalação**, selecione "Sim, eu gostaria de vender pela Microsoft e ter transações de host da Microsoft em meu nome". Esta opção será mostrada somente se o Transact estiver disponível para seu tipo de oferta.

## <a name="transact-general-overview"></a>Visão geral de transações

Ao usar a opção Transact Publishing, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O Publicador deve considerar a cobrança de taxas de infraestrutura e as próprias tarifas de licenciamento de software do editor, ao selecionar um modelo de cobrança e um tipo de oferta.

Atualmente, a opção Transact Publishing tem suporte para os seguintes tipos de oferta: máquinas virtuais, aplicativos do Azure e aplicativos SaaS.

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

**Para máquinas virtuais e aplicativos do Azure**

Para máquinas virtuais e aplicativos do Azure, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. As tarifas de uso de infraestrutura são cobradas e apresentadas separadamente das tarifas de licenciamento do provedor de software na fatura do cliente.

**Para aplicativos SaaS**

Para aplicativos SaaS, o editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único.  Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação usada, as taxas de licença de software do Publicador podem ser apresentadas da seguinte maneira:  

* Grátis: Sem custo para licenças de software.

* Traga sua própria licença (BYOL): todas as cobranças aplicáveis por licenças de software são gerenciadas diretamente entre o editor e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.

* Pague conforme o uso: as taxas de licença de software são apresentadas como uma taxa de preços por vCPU (por hora e por núcleo) com base na infraestrutura do Azure usada. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.

* Preços de assinatura: as taxas de licença de software são apresentadas como uma taxa de recorrência mensal ou anual, cobrada como uma taxa fixa ou por estação. Isso se aplica somente a aplicativos SaaS e aplicativos gerenciados pelo aplicativo do Azure.

* Avaliação gratuita de software: sem cobrança de licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação pré-paga ou assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, devoluções e estornos de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar o software do Publicador, sujeito aos termos e condições do mercado comercial da Microsoft e do contrato de licenciamento do usuário final do editor. Os editores devem fornecer seu contrato de licenciamento de usuário final ou selecionar o [contrato padrão](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação em transações, o editor pode disponibilizar uma licença de software gratuitamente por 30 dias ou 90 dias. Esse recurso de desconto não inclui o custo do uso da infraestrutura do Azure que é impulsionado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, os editores podem realizar transações de uma oferta privada, completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas são suportadas por todas as três opções de publicação de transações.

Essa opção permite um preço mais alto ou mais baixo do que a oferta publicamente disponível. Ofertas privadas podem ser usadas para desconto ou adicionar um prêmio para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio de white listando sua assinatura do Azure no nível da oferta.


### <a name="examples"></a>Exemplos

**Pré-paga** 

* Se você ativar a opção Pague conforme o uso, terá a seguinte estrutura de custos.

|Custo de sua licença  | US $ 1,00 por hora  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 1,14 por hora*       |

* Nesse cenário, a Microsoft fatura US$ 1,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US$ 1,14 por hora  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença|   US $ 0,80 por hora     |
|A Microsoft mantém 20% do seu custo de licença  |  US $ 0,20 por hora       |
|A Microsoft mantém a 100% do custo de uso do Azure | US $ 0,14 por hora |

**BYOL (traga sua própria licença)**

* Se você habilitar a opção de BYOL, você tem a seguinte estrutura de custo.

|Custo de sua licença  | Valor de licença negociada e faturada por você  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 0,14 por hora*       |

* Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US $ 0,14 por hora  |
|---------|---------|
|A Microsoft mantém o custo de uso do Azure    |   US $ 0,14 por hora     |
|A Microsoft mantém 0% do seu custo de licença   |  US $ 0.00 por hora       |

**Assinatura de aplicativo SaaS**

Essa opção deve ser configurada para vender pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual.
*    Se você habilitar a opção vender por meio da Microsoft para uma oferta de SaaS, você terá a seguinte estrutura de custo.

|Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
|*O cliente é cobrado pela Microsoft*    |  *US $ 100,00 por mês (observação: o editor deve contabilizar quaisquer custos incorridos ou de infraestrutura de passagem no valor da licença)*  |

* Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.
* Os parceiros qualificados para a taxa reduzida de serviço do Marketplace verão uma taxa de transação reduzida nas ofertas de SaaS de maio de 2019 até junho de 2020. Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $90 para o Publicador.

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \*A Microsoft paga a você 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   US $80,00 por mês <br> \*$90 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \*A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \*$10     |

* **Taxa reduzida de serviço do Marketplace:** Para determinados produtos SaaS que você publica em nosso mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%.  Para que seu produto seja qualificado, pelo menos um de seus produtos deve ser designado pela Microsoft como uma das vendas de covenda de IP pronta ou de IP. Para receber essa taxa de serviço do Marketplace reduzida para o mês, a qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do final do mês do calendário anterior. A taxa reduzida de serviço do Marketplace não se aplicará a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio de nosso mercado comercial.  Essa taxa de serviço do Marketplace reduzida estará disponível para ofertas qualificadas, com encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a taxa de serviço do Marketplace voltará ao seu valor normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento**

O Publicador pode usar o método de faturamento preferido do cliente para fornecer assinatura ou taxas de licença de software PAYGO.

**Contrato Enterprise** 

Se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, suas tarifas de licença de software serão cobradas usando esse método de faturamento como um custo discriminado, separados de qualquer custo de uso específico do Azure.

**Cartões de crédito e a fatura mensal** 

Os clientes também podem pagar usando um cartão de crédito e uma fatura mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

Por exemplo, se o cliente compra usando um cartão de crédito:

|Descrição    |    Data  |
|----------|----------|
|Período de ordem   | 15 de agosto de 2018 – 30 de agosto de 2018 |
|Prazo final (mês)   | 30 de agosto de 2018 |
|Data de cobrança | 1 de setembro de 2018 |
|Data de pagamento do cliente | 1 de setembro de 2018 |
|Efetue a caução de período (cartões de crédito, 30 dias) | 1 de setembro de 2018-30 de setembro de 2018 |
|Início do período de coleta | 1 de setembro de 2018 |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2018 |
|Data de cálculo de pagamento (mensalmente no dia 15) | 1 de outubro de 2018 |
|Data de pagamento | 15 de outubro de 2018 |

Se o cliente comprar usando um Enterprise Agreement:

| Descrição |    Data  |
|----------|----------|
|Período de ordem | 15 de agosto de 2018 – 30 de agosto de 2018 |
|Prazo final (trimestre) | 30 de setembro de 2018 |
|Data de cobrança | 15 de outubro de 2018 |
|Efetue a caução de período (cartões de crédito, 30 dias) | n/d |
|Início do período de coleta | 15 de outubro de 2018 |
|Término do período de coleta (máximo, 90 dias) | 15 de janeiro de 2019 |
|Data de pagamento do cliente | 30 de dezembro de 2018 |
|Data de cálculo de pagamento (mensalmente no dia 15) | 15 de janeiro de 2019 |
|Data de pagamento | 15 de fevereiro de 2019 |

**Créditos gratuitos e compromisso monetário** 

Alguns clientes optam por pagar antecipadamente o Azure com um compromisso monetário no Enterprise Agreement ou receberam créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Cobrança e coleções** 

O faturamento da licença do software do editor é apresentado usando o método de faturamento selecionado pelo cliente e segue o cronograma do faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

* Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

* Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e informações para a opção de publicação de transação estão disponíveis por meio da seção análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter mais informações e políticas legais, consulte o [contrato do editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponível no Partner Center).

Para obter ajuda sobre perguntas de cobrança, entre em contato com o [suporte do editor do Marketplace comercial](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="transact-requirements"></a>Transact requisitos

Os requisitos de transação para diferentes tipos de oferta são abordados nesta seção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

* Uma conta Microsoft e informações financeiras são necessárias para a opção Transact Publishing, independentemente do modelo de preços da oferta.
* As informações financeiras obrigatórias incluem a conta de pagamento e o perfil de imposto.

Para obter mais informações sobre como configurar essas contas, consulte [gerenciar sua conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)).

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A opção de publicação de transações está disponível apenas para uso com os seguintes tipos de oferta de mercado:

**Máquina Virtual** 

Selecione modelos gratuitos, traga sua licença própria ou pague conforme o preço e apresente-os como SKUs definidos no nível da oferta. Na fatura do Azure do cliente, a Microsoft apresenta as taxas de licença de software do Publicador separadamente das taxas de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são impulsionadas pelo uso do software do editor.

**Aplicativos do Azure: modelo de solução ou aplicativo gerenciado** 

Deve provisionar uma ou mais máquinas virtuais e pulls por meio da soma dos preços de máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preço, em vez do preço da máquina virtual. Em alguns casos, as tarifas de uso da infraestrutura do Azure são passadas para o cliente separadamente de taxas de licença de software, mas no mesmo demonstrativo de cobrança. No entanto, se você configurar uma oferta de aplicativo gerenciado para encargos de infraestrutura de ISV, os recursos do Azure serão cobrados para o Publicador e o cliente receberá uma tarifa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gerenciamento.

## <a name="next-steps"></a>Próximas etapas

* Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
* Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
