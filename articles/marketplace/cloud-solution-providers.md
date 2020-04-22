---
title: Provedores de soluções em nuvem | Mercado Azure
description: Os editores agora podem vender suas ofertas através do canal parceiro CSP (Microsoft Cloud Solution Provider).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: c4c8f21234cfbf040d57c0e1c8dbecbb698d7d50
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685917"
---
# <a name="cloud-solution-providers"></a>Provedores de Soluções de Nuvem

As ofertas de software podem atingir milhões de clientes qualificados da Microsoft atendidos por parceiros no programa Cloud Solution Provider (CSP), além da disponibilidade pública de ofertas através de [web storefronts da Microsoft.](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)

Os editores configuram ofertas de disponibilidade no programa CSP em uma base opt-in, para uma nova oferta ou uma já existente, permitindo que os parceiros vendam seus produtos e criem soluções empacotadas para os clientes.

Os editores são responsáveis por fornecer suporte de correção de quebra aos clientes finais e por fornecer um mecanismo para parceiros no programa CSP e/ou clientes entrarem em contato com você para obter suporte. É uma prática recomendada fornecer aos parceiros do programa CSP a documentação do usuário, treinamento e notificações de saúde/paralisação de serviços (conforme aplicável) para que os parceiros do programa CSP estejam equipados para lidar com solicitações de suporte de nível 1 dos clientes.  

As seguintes ofertas são elegíveis para serem optadas para serem vendidas por parceiros no programa CSP:

- Software como serviço (SaaS) oferece
- VMs (Máquinas Virtuais)
- Modelos de Solução
- Aplicativos gerenciados

> [!NOTE]
> Contêineres e Bring Your Own License (BYOL) VM SKUs são optados para serem vendidos por parceiros no programa CSP por padrão.

## <a name="how-to-configure-an-offering"></a>Como configurar uma oferta

A configuração de opt-in do programa CSP está configurada no Partner Center ou no Cloud Partner Portal oferecem experiência de criação. [Saiba mais sobre a mudança da experiência do editor.](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)

### <a name="partner-center-opt-in"></a>Opt-in do Partner Center

No Partner Center, você encontrará a experiência de opt-in sob o módulo de audiência do CSP Reseller.

![Público do Revendedor CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

No módulo de audiência do CSP Reseller, você tem três opções para escolher:

- Opção um: Qualquer parceiro no programa CSP
- Opção dois: Parceiros específicos no programa CSP que seleciono
- Opção três: Sem parceiros no programa CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opção um: Qualquer parceiro no programa CSP

![Qualquer parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ao escolher essa opção, todos os parceiros do programa CSP são elegíveis para revender sua oferta aos seus clientes.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opção dois: Parceiros específicos no programa CSP que seleciono

![Parceiros específicos no programa CSP que seleciono](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ao escolher esta opção, você autoriza quais parceiros no programa CSP são elegíveis para revender sua oferta.

Para autorizar parceiros, clique **em Selecionar parceiros CSP** e aparece um menu que permite pesquisar pelo nome do parceiro ou iD de inquilino do CSP Azure Active Directory (AAD).

![Selecione o menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Você pode aplicar filtros de pesquisa, como **País,** **Competência**ou **Habilidade.**

![Filtros de país, competência e habilidade para pesquisa de parceiros](media/marketplace-publishers-guide/csp-add-resellers.png)

Depois de escolher a lista de parceiros, selecione **Adicionar**.

![Lista de exemplos de parceiros autorizados no programa CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Uma tabela mostrando a lista de parceiros selecionados aparece na página de audiência do CSP Reseller.

![Tabela com lista de parceiros na página de audiência do CSP Revendedor](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selecione **Salvar rascunho** para registrar suas alterações.

Se essa oferta não for publicada, você precisará publicar sua oferta para disponibilizá-la aos seus parceiros selecionados.

>[!NOTE]
>Se você autorizar um parceiro no programa CSP em uma determinada região, ele pode vender a oferta para qualquer cliente que pertença a essa região específica. Consulte [Cloud Solution Provider programamercados regionais e moeda](https://docs.microsoft.com/partner-center/regional-authorization-overview) para obter mais informações sobre como as ofertas de CSP são classificadas em regiões.

Se você estiver atualizando a lista CSP de uma oferta já publicada, adicione os parceiros adicionais e selecione **o público do Sync CSP**.

Se você tiver uma oferta que já tenha uma lista de parceiros autorizados e quiser usar a mesma lista para outra oferta, use **Importação/Exportação**. Navegue até a oferta que tem a lista CSP e selecione **CSPs de exportação**. A função desenvolve um arquivo .csv que pode ser importado para outra oferta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opção três: Sem parceiros no programa CSP

![Nenhum parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ao escolher esta opção, você está optando por sua oferta fora do programa CSP. Você pode alterar esta seleção a qualquer momento.

### <a name="cloud-partner-portal-opt-in"></a>Opt-in do Cloud Partner Portal

No Portal do Parceiro na Nuvem, o opt-in é definido na guia Marketplace ou Storefront. A capacidade de escolher parceiros específicos no programa CSP só está disponível no Partner Center.

![Experiência opt-in do CSP no CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Desautorizaparceiros no programa CSP

Se você autorizou um parceiro no programa CSP e esse parceiro já revendeu o produto para seus clientes, você não poderá desautorizar esse parceiro.

Se um parceiro no programa CSP não vendeu seu produto para seus clientes e você gostaria de remover o CSP após a publicação de sua oferta, use as seguintes instruções:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso pré-preenchidos.

2. Para **Selecionar a versão do produto,** selecione Gerenciamento de **ofertas ao vivo**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva o problema,** selecione Atualizar a oferta **existente**.
5. Selecione **Next** para ser direcionado à **página Desemite detalhes** para inserir mais detalhes sobre o seu problema.
6. Use **Deauthorize CSP** como título de emissão e preencha o resto das seções necessárias.




## <a name="navigate-between-options"></a>Navegue entre opções

Use esta seção para navegar entre as três opções de revendedor CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navegue a partir da Opção um: Qualquer parceiro no programa CSP

Se sua oferta for atualmente **Opção 1: Qualquer parceiro no programa CSP** e você gostaria de navegar para qualquer uma das outras duas opções, use as seguintes instruções para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso pré-preenchidos.

2. Para **Selecionar a versão do produto,** selecione Gerenciamento de **ofertas ao vivo**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva o problema,** selecione Atualizar a oferta **existente**.
5. Selecione **Next** para ser direcionado à **página Desemite detalhes** para inserir mais detalhes sobre o seu problema.
6. Use **Deauthorize CSP** como título de emissão e preencha o resto das seções necessárias.

> [!NOTE]
> Se você está tentando navegar para a Opção dois e um parceiro no programa CSP já revendeu a oferta para seus clientes, esse parceiro já está por padrão em sua lista de parceiros autorizados.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navegue a partir da Opção Dois: Parceiros específicos no programa CSP que seleciono

Se sua oferta for atualmente **Opção 2: Parceiros específicos no programa CSP que seleciono** e você gostaria de navegar até a **Opção um: Qualquer parceiro no programa CSP,** use as seguintes instruções para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso pré-preenchidos.

2. Para **Selecionar a versão do produto,** selecione Gerenciamento de **ofertas ao vivo**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva o problema,** selecione Atualizar a oferta **existente**.
5. Selecione **Next** para ser direcionado à **página Desemite detalhes** para inserir mais detalhes sobre o seu problema.
6. Use **Deauthorize CSP** como título de emissão e preencha o resto das seções necessárias.

 Se sua oferta for atualmente **Opção 2: Parceiros específicos no programa CSP que seleciono** e você gostaria de navegar até a **Opção 3: Sem parceiros no programa CSP,** você só poderá navegar até essa opção se os parceiros do programa CSP que você havia autorizado anteriormente não tenham revendido sua oferta para clientes finais. Por favor, use as seguintes instruções para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso pré-preenchidos.

2. Para **Selecionar a versão do produto,** selecione Gerenciamento de **ofertas ao vivo**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva o problema,** selecione Atualizar a oferta **existente**.
5. Selecione **Next** para ser direcionado à **página Desemite detalhes** para inserir mais detalhes sobre o seu problema.
6. Use **Deauthorize CSP** como título de emissão e preencha o resto das seções necessárias.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navegar a partir da Opção 3: Sem parceiros no programa CSP

Se sua oferta for atualmente **Opção 3: Sem parceiros no programa CSP,** você pode navegar para qualquer uma das outras duas opções a qualquer momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Compartilhar materiais de vendas e suporte com parceiros no programa CSP

Para ajudar os parceiros no programa Cloud Solution Provider mais efetivamente representar sua oferta e se envolver com sua organização, você deve enviar materiais de vendas e suporte que estarão disponíveis para os revendedores. Esses recursos não serão expostos aos clientes nas vitrines do marketplace.

### <a name="partner-center-csp-channel"></a>Canal CSP do Partner Center

Se você optou pelo canal CSP no Partner Center, os editores devem inserir uma URL que hospeda materiais de marketing relevantes e canaliza informações de contato para o canal CSP sob o módulo de listagem de ofertas:

![Informações colaterais do Partner Center CSP](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canal CSP do Portal de Parceiros na Nuvem

Se você optou pelo canal CSP no Cloud Partner Portal, os editores devem inserir uma URL que hospeda materiais de marketing relevantes e canaliza informações de contato para o canal CSP:

![Informações colaterais do Portal CSP do Parceiro na Nuvem](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Próximas etapas

Visite o [Azure Marketplace e o AppSource Publisher Guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Para saber mais sobre os serviços de GTM do marketplace, consulte [serviços go-to-market](https://partner.microsoft.com/reach-customers/gtm).

Faça login no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) ou no Cloud [Partner Portal](https://cloudpartner.azure.com/) para criar e configurar sua oferta.
