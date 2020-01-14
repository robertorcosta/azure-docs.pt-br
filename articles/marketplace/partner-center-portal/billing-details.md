---
title: Cobrança do Marketplace comercial | Azure Marketplace
description: Este artigo aborda os tópicos relacionados ao comércio para o Marketplace comercial.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 0fda04bbfa632f429539ddc0ad90941cb645226b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934634"
---
# <a name="commercial-marketplace-billing"></a>Cobrança do Marketplace comercial 

Este artigo aborda os tópicos relacionados ao commerce para o Marketplace comercial:

- [Opções de publicação do Marketplace](#marketplace-publishing-options) 
- [Visão geral do Transact](#transact-general-overview)
- [Modelos de cobrança de transação](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opções de publicação no marketplace 

O Marketplace comercial oferece várias opções de publicação para Publicadores.

### <a name="list-and-trial-publishing-options"></a>Opções de publicação de lista e de avaliação

Os editores podem aproveitar as opções de publicação lista, avaliação e BYOL (traga sua própria licença) para fins de aquisição promocional e de usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhuma taxa de transação associada. Os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: pedido, atendimento, medição, faturamento, faturamento, pagamento e cobrança. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

Além das opções de publicação de lista e de avaliação, a opção Transact Publishing está disponível para editores. Essa opção aproveita os recursos de comércio globalmente disponíveis da Microsoft e permite que a Microsoft hospede transações de Marketplace de nuvem em nome do Publicador.

## <a name="transact-general-overview"></a>Visão geral de transações

Ao usar a opção Transact Publishing, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O Publicador deve considerar a cobrança de taxas de infraestrutura e as próprias tarifas de licenciamento de software do editor, ao selecionar um modelo de cobrança e um tipo de oferta. 

Atualmente, a opção Transact Publishing tem suporte para os seguintes tipos de oferta: máquinas virtuais, aplicativos do Azure e aplicativos SaaS. 

![Transact-lo no Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

#### <a name="for-virtual-machines-and-azure-applications"></a>Para máquinas virtuais e aplicativos do Azure

Para máquinas virtuais e aplicativos do Azure, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. As tarifas de uso de infraestrutura são cobradas e apresentadas separadamente das tarifas de licenciamento do provedor de software na fatura do cliente.

#### <a name="for-saas-apps"></a>Para aplicativos SaaS

Para aplicativos SaaS, o editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único. Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação usada, as taxas de licença de software do Publicador podem ser apresentadas da seguinte maneira:

- *Gratuito*: sem custos para licenças de software.
- *BYOL (traga sua própria licença)* : quaisquer encargos aplicáveis para licenças de software são gerenciados diretamente entre o Publicador e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. (Máquinas Virtuais e Aplicativos do Azure somente).
- Pré- *pago*: as taxas de licença de software são apresentadas como uma taxa de preço por hora, por núcleo (vCPU) com base na infraestrutura do Azure usada. Esse modelo se aplica somente a máquinas virtuais e aplicativos do Azure.
- *Preços de assinatura*: as taxas de licença de software são apresentadas como uma taxa de recorrência mensal ou anual, cobrada como uma taxa fixa ou por estação. Esse modelo se aplica somente a aplicativos SaaS e aplicativos gerenciados pelo aplicativo do Azure.
- *Avaliação de software gratuita*: sem custos para licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação pré-paga ou de assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras de licenças de software, Devoluções e cobranças. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar o software do Publicador, sujeito aos termos e condições do mercado comercial da Microsoft e do contrato de licenciamento do usuário final do editor. Os editores devem fornecer seu contrato de licenciamento de usuário final ou selecionar o [contrato padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação em transações, o editor pode disponibilizar uma licença de software gratuitamente por 30 dias ou 90 dias. Esse recurso de desconto não inclui o custo do uso da infraestrutura do Azure que é impulsionado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, os editores podem realizar transações de uma oferta privada, completa com preços negociados e específicos do negócio ou configurações personalizadas. As ofertas privadas são suportadas por todas as três opções de publicação de transações.

Essa opção permite um preço mais alto ou mais baixo do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontos ou adicionar um Premium para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio de white listando sua assinatura do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

#### <a name="pay-as-you-go"></a>Pago pelo uso

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

### <a name="bring-your-own-license-byol"></a>BYOL (traga sua própria licença)

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

### <a name="saas-app-subscription"></a>Assinatura de aplicativo SaaS

Essa opção deve ser configurada para vender pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual.

• Se você habilitar a opção vender por meio da Microsoft para uma oferta de SaaS, você terá a seguinte estrutura de custo.

|Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
|*O cliente é cobrado pela Microsoft*    |  *US $ 100,00 por mês (observação: o editor deve contabilizar quaisquer custos incorridos ou de infraestrutura de passagem no valor da licença)*  |

* Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.
* Os parceiros qualificados para a taxa reduzida de serviço do Marketplace verão uma taxa de transação reduzida nas ofertas de SaaS de maio de 2019 até junho de 2020. Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $90 para o Publicador.

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \* a Microsoft paga 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   US $80,00 por mês <br> \* $90 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \* a Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \* $10     |

**Taxa reduzida de serviço do Marketplace:** Para determinados produtos SaaS que você publica em nosso mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%.  Para que seu produto seja qualificado, pelo menos um de seus produtos deve ser designado pela Microsoft como uma das vendas de covenda de IP pronta ou de IP. Para receber essa taxa de serviço do Marketplace reduzida para o mês, a qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do final do mês do calendário anterior. A taxa reduzida de serviço do Marketplace não se aplicará a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio de nosso mercado comercial.  Essa taxa de serviço do Marketplace reduzida estará disponível para ofertas qualificadas, com encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a taxa de serviço do Marketplace voltará ao seu valor normal.
