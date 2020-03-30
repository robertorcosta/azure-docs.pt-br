---
title: Faturamento de mercado comercial | Mercado Azure
description: Este artigo aborda temas relacionados ao comércio para o mercado comercial.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279735"
---
# <a name="commercial-marketplace-billing"></a>Faturamento do mercado comercial

Este artigo aborda tópicos relacionados ao comércio para o mercado comercial:

- [Opções de publicação no marketplace](#marketplace-publishing-options)
- [Visão geral de transações](#transact-general-overview)
- [Transact modelos de cobrança](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opções de publicação no marketplace

O mercado comercial oferece várias opções de publicação para editores.

### <a name="list-and-trial-publishing-options"></a>Opções de publicação de liste e teste

Os editores podem aproveitar a lista, o teste e trazer opções de publicação de sua própria licença (BYOL) para fins promocionais e de aquisição de usuários. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há taxa de transação associada. Os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: pedido, atendimento, medição, faturamento, faturamento, pagamento e cobrança. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

Além das opções de publicação de lista e teste, a opção de publicação transana está disponível para os editores. Essa opção aproveita os recursos de comércio globalmente disponíveis da Microsoft e permite que a Microsoft hospede transações de mercado em nuvem em nome do editor.

## <a name="transact-general-overview"></a>Visão geral de transações

Ao usar a opção de publicação transato, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de ofertas para a assinatura do Azure do cliente. O editor deve considerar o faturamento das taxas de infra-estrutura e as taxas de licenciamento de software do próprio editor, ao selecionar um modelo de faturamento e tipo de oferta.

A opção de publicação Transact é atualmente suportada para os seguintes tipos de ofertas: máquinas virtuais, aplicativos Azure e aplicativos SaaS.

![Transact no Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

#### <a name="for-virtual-machines-and-azure-applications"></a>Para máquinas virtuais e aplicativos Azure

Para máquinas virtuais e aplicativos Azure, as taxas de uso da infra-estrutura do Azure são cobradas na assinatura do Azure do cliente. As taxas de uso da infra-estrutura são precificadas e apresentadas separadamente das taxas de licenciamento do provedor de software na fatura do cliente.

#### <a name="for-saas-apps"></a>Para aplicativos SaaS

Para aplicativos SaaS, o editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único. Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação utilizada, as taxas de licença de software do editor podem ser apresentadas da seguinte forma:

- *Grátis*: Não há custo para licenças de software.
- *Traga sua própria licença (BYOL):* Quaisquer taxas aplicáveis para licenças de software são gerenciadas diretamente entre o editor e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. (Máquinas Virtuais e Aplicativos do Azure somente).
- *Pay-as-you-go*: As taxas de licença de software são apresentadas como uma taxa de preço por hora, por núcleo (vCPU) com base na infra-estrutura do Azure usada. Este modelo só se aplica a máquinas virtuais e aplicações Azure.
- *Preços de assinatura*: As taxas de licença de software são apresentadas como uma taxa mensal ou anual, cobrada como uma taxa fixa ou por assento. Este modelo só se aplica a aplicativos SaaS e Aplicativos Azure - Aplicativos Gerenciados.
- *Teste de software gratuito*: Sem custo para licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação paga ou por assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras de licenças de software, devoluções e cobranças de reações. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite que os clientes peçam, licenciem e usem software de editor, sujeitando-se aos termos e condições tanto do marketplace comercial da Microsoft quanto do contrato de licenciamento do usuário final do editor. Os editores devem fornecer seu contrato de licenciamento de usuário final ou selecionar o [Contrato Padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação em transações, o editor pode disponibilizar uma licença de software gratuitamente por 30 dias ou 90 dias. Esse recurso de desconto não inclui o custo do uso da infraestrutura do Azure que é impulsionado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de faturamento para rentabilizar uma oferta, os editores podem fazer uma oferta privada, completa com preços negociados e específicos para negócios, ou configurações personalizadas. As ofertas privadas são suportadas por todas as três opções de publicação de transações.

Esta opção permite preços maiores ou mais baixos do que a oferta disponível publicamente. Ofertas privadas podem ser usadas para desconto ou adicionar um prêmio para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio de white listando sua assinatura do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

#### <a name="pay-as-you-go"></a>Pré-pago

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

### <a name="bring-your-own-license-byol"></a>Traga sua própria licença (BYOL)

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

### <a name="saas-app-subscription"></a>Assinatura do Aplicativo SaaS

Esta opção deve ser configurada para vender através da Microsoft e pode ser precificada a uma taxa fixa ou por usuário mensal ou anual.

• Se você habilitar a opção Vender através da Microsoft para uma oferta SaaS, então você terá a seguinte estrutura de custos.

|Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
|*O cliente é cobrado pela Microsoft*    |  *US $ 100,00 por mês (observação: o editor deve contabilizar quaisquer custos incorridos ou de infraestrutura de passagem no valor da licença)*  |

* Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.
* Os parceiros que se qualificaram para a Taxa de Serviço de Marketplace Reduzida verão uma taxa de transação reduzida nas ofertas do SaaS de maio de 2019 até junho de 2020. Neste cenário, a Microsoft cobra US$ 100,00 pela sua licença de software e paga US$ 90,00 ao editor.

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \*A Microsoft paga 90% do custo da sua licença para quaisquer aplicativos SaaS qualificados   |   US $80,00 por mês <br> \*$90.00 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \*A Microsoft mantém 10% do custo da sua licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \*$10.00     |

**Taxa de serviço de marketplace reduzida:** Para certos produtos SaaS que você publica em nosso Mercado Comercial, a Microsoft reduzirá sua taxa de serviço de marketplace de 20% (conforme descrito no Acordo Microsoft Publisher) para 10%.  Para que seu Produto se qualifique, pelo menos um de seus produtos deve ser designado pela Microsoft como co-venda IP pronta ou co-venda IP priorizada. Para receber essa taxa de serviço de marketplace reduzida para o mês, a elegibilidade deve ser cumprida pelo menos cinco (5) dias úteis antes do final do mês calendário anterior. A taxa de serviço de marketplace reduzida não se aplicará a VMs, Aplicativos Gerenciados ou quaisquer outros produtos disponibilizados através de nosso Marketplace Comercial.  Esta taxa de serviço de marketplace reduzida estará disponível para ofertas qualificadas, com taxas de licença cobradas pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse prazo, a Taxa de Serviço do Marketplace voltará ao seu valor normal.
