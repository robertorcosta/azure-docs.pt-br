---
title: Criar uma nova oferta de serviço gerenciado no Marketplace comercial
description: Como criar uma nova oferta de serviço gerenciado para listagem no Azure Marketplace usando o portal do Marketplace comercial no Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147919"
---
# <a name="create-a-new-managed-service-offer"></a>Criar uma nova oferta de serviço gerenciado

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de serviço gerenciado do Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [publicar uma oferta de serviço gerenciado no Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) para gerenciar suas ofertas no portal do Cloud Partner.

O serviço gerenciado oferece ajuda para habilitar cenários de [Lighthouse do Azure](../../lighthouse/overview.md) . Quando um cliente aceita uma oferta de serviço gerenciado, ele é capaz de integrar recursos para o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Para começar a criar ofertas de serviço gerenciado, certifique-se de primeiro [criar uma conta do Partner Center](./create-account.md) e abra o [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com a página **visão geral** selecionada. Você deve ter um [nível de competência de plataforma de nuvem prata ou ouro](https://partner.microsoft.com/membership/cloud-platform-competency) ou ser um [Azure expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de serviço gerenciado.

![Painel do Marketplace comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Depois que uma oferta tiver sido publicada, edições para a oferta feita no Partner Center serão atualizadas somente no sistema e armazenará os frontais após a republicação. Certifique-se de enviar a oferta para publicação depois de fazer alterações.

## <a name="create-a-new-offer"></a>Criar uma oferta

Selecione o botão **+ nova oferta** e, em seguida, selecione o item de menu **serviço gerenciado** . A caixa de diálogo **nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>ID da oferta e alias

- **ID da oferta**: identificador exclusivo para cada oferta em sua conta. Essa ID será visível para os clientes no endereço de URL para a oferta do Marketplace. Essa ID pode conter apenas caracteres alfanuméricos minúsculos (incluindo hifens e sublinhados, mas sem espaço em branco), limitados a 50 caracteres e não pode ser alterada depois que você selecionar **criar**.  Por exemplo, se você inserir *Test-offer-1* aqui, a URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias da oferta**: o nome usado para fazer referência à oferta no Partner Center. Esse nome não será usado no Marketplace e será diferente do nome da oferta e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **criar**.

Depois de inserir sua **ID de oferta** e **alias de oferta**, selecione **criar**. Em seguida, você poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Instalação da oferta

A página **instalação da oferta** solicita as seguintes informações. Certifique-se de selecionar **salvar** depois de concluir esses campos.

## <a name="connect-lead-management"></a>Conectar gerenciamento de leads

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Observe que, de acordo com as [políticas de certificação de Serviços Gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), é necessário um **Destino de Lead**. Isso criará um registro em seu sistema CRM cada vez que um cliente implantar sua oferta.

Para obter mais informações, consulte [visão geral do gerenciamento de Lead](./commercial-marketplace-get-customer-leads.md).

Lembre-se de **salvar** a página antes de passar para a próxima seção.

## <a name="properties"></a>Propriedades

A página **Propriedades** permite que você defina as categorias usadas para agrupar sua oferta no Marketplace e os contratos legais que dão suporte à sua oferta. Selecione **salvar** depois de concluir esta página.

### <a name="category"></a>Categoria

Selecione no mínimo um e no máximo cinco categorias que serão usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Lembre-se de chamar como sua oferta dá suporte a essas categorias na descrição da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus próprios termos e condições legais no campo **termos e condições** . Você também pode fornecer a URL onde os termos e condições podem ser encontrados. Os clientes serão solicitados a aceitar esses termos antes que possam experimentar sua oferta.

## <a name="offer-listing"></a>Listagem de ofertas

A página de **listagem da oferta** permite que você defina detalhes do Marketplace (nome da oferta, descrição, imagens, etc.) para sua oferta.

> [!NOTE]
> A oferta de conteúdo de listagem (como descrição, documentos, capturas de tela, termos de uso etc.) não precisa estar em inglês, desde que a descrição da oferta comece com a frase ", este aplicativo está disponível apenas em [idioma diferente do inglês]". Também é aceitável fornecer uma URL de *Link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

### <a name="name"></a>Name

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto inserido para o **alias de oferta** quando você criou a oferta, mas pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). Esse resumo longo também pode ser usado nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta (até 3.000 caracteres). Essa descrição será exibida aos clientes na visão geral de listagem do Marketplace. Inclua a proposta de valor da sua oferta, os principais benefícios, a categoria e/ou as associações do setor, as oportunidades de compra no aplicativo e as divulgações necessárias.

Algumas dicas para escrever sua descrição:  

- Descreva claramente a proposição de valor da sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens em sua proposta de valor:
  - Descrição da oferta
  - O tipo de usuário que se beneficia da oferta
  - O cliente precisa ou problemático que a oferta aborda
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados da pesquisa.  
- Não dependa de recursos e em funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você oferece.  
- Use o vocabulário específico do setor ou palavras com base no benefício tanto quanto possível.

Para tornar a descrição da sua oferta mais atraente, use o editor de Rich Text para formatar sua descrição.

![Usando o editor de Rich Text](./media/text-editor2.png)

Use as instruções a seguir para usar o editor de Rich Text:

- Para alterar o formato do seu conteúdo, realce o texto que você deseja formatar e selecione um estilo de texto, conforme mostrado abaixo:

     ![Usando o editor de Rich Text para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com marcadores ou numerada ao texto, use as opções abaixo:

     ![Usando o editor de Rich Text para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo para o texto, use as opções abaixo:

     ![Usando o editor de Rich Text para recuar](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Link de política de privacidade

Insira a URL para a política de privacidade da sua organização (hospedada no seu site). Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Links úteis

Forneça documentos complementares opcionais online sobre sua solução. Adicione links úteis adicionais clicando em **+ Adicionar um link**.

### <a name="contact-information"></a>Informações de contato

Nesta seção, você deve fornecer o nome, o email e o número de telefone para um **contato de suporte** e um contato de **engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

### <a name="support-urls"></a>URLs de suporte

Se você tiver sites de suporte para **clientes globais do Azure** e/ou **clientes do Azure governamental**, forneça essas URLs aqui.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato. png.

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="marketplace-logos"></a>Logotipos do Marketplace

São necessários quatro tamanhos de logotipo: **Small (40x40)**, **Medium (90x90)**, **Large (115x115)** e **Wide (255x115)**. Siga estas diretrizes para seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
- As cores do tema do portal são branca e preta. Não use essas cores como a cor de fundo de seu logotipo. Use uma cor que destaque seu logotipo no portal. É recomendável usar cores primárias simples.
- Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não estão em branco, preto ou azul.
- A aparência de seu logotipo deve ser simples e evitar gradientes. Não use um fundo gradiente no logotipo.
- Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa.
- Verifique se o logotipo não está esticado.

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Todas as capturas de tela devem ser 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Opcionalmente, você pode adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Visualização

Antes de publicar sua oferta em tempo real para a oferta mais ampla do Marketplace, primeiro você precisará disponibilizá-la para um público de visualização limitado. Isso permite que você confirme como a oferta é exibida no Azure Marketplace antes de disponibilizá-la para os clientes. As equipes de suporte e engenharia da Microsoft também poderão exibir sua oferta durante esse período de visualização.

Você pode definir o público de visualização inserindo IDs de assinatura do Azure na seção do **público de visualização** . Você pode inserir até 10 IDs de assinatura manualmente ou carregar um arquivo. csv com até 100 IDs de assinatura.

Todos os clientes associados a essas assinaturas poderão exibir a oferta no Azure Marketplace antes que ela fique ativa. Certifique-se de incluir suas próprias assinaturas aqui para poder visualizar sua oferta.

## <a name="plan-overview"></a>Visão geral do plano

Cada oferta deve ter um ou mais planos (às vezes chamados de SKUs). Você pode adicionar vários planos para dar suporte a diferentes conjuntos de recursos com preços diferentes ou para personalizar um plano específico para um público seleto de clientes específicos. Os clientes podem ver os planos disponíveis para eles na oferta pai.

Na página **visão geral do plano** , selecione **+ criar novo plano**. Em seguida, insira uma **ID de plano** e um **nome de plano**. Ambos os valores podem conter apenas caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Esses valores podem ser visíveis para os clientes e não podem ser alterados depois que você publicar a oferta.

Selecione **criar** depois de inserir esses valores para continuar trabalhando em seu plano. Há três seções a serem concluídas: **lista de planos**, **preços e disponibilidade**e **configuração técnica**.

### <a name="plan-listing"></a>Lista de planos

Primeiro, forneça um **Resumo dos resultados da pesquisa** para o plano. Esta é uma breve descrição do seu plano (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

Em seguida, insira uma **Descrição** que forneça uma explicação mais detalhada do plano.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Atualmente, há apenas um modelo de preços que pode ser usado para oferta de serviço gerenciado: **traga sua própria licença (BYOL)**. Isso significa que você cobrará de seus clientes diretamente os custos relacionados a essa oferta, e a Microsoft não cobrará nenhum valor por você.

A seção de **visibilidade do plano** permite que você indique se esse plano deve ser [privado](../../marketplace/private-offers.md). Se você deixar a caixa **esta é um plano privado** desmarcada, seu plano não será restrito a clientes específicos (ou a um determinado número de clientes).

Para disponibilizar esse plano somente para clientes específicos, selecione **Sim**. Ao fazer isso, você precisará identificar os clientes fornecendo suas IDs de assinatura. Eles podem ser inseridos um por um (para até 10 assinaturas) ou carregando um arquivo. csv (para um máximo de 10.000 assinaturas em todos os planos). Inclua suas próprias assinaturas aqui para que você possa testar e validar a oferta.

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Você poderá [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) depois que um cliente aceitar uma oferta somente se você tiver incluído uma **autorização** com a **definição de função** definida para atribuição de [registro de serviços gerenciados excluir função](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando você publicou a oferta. Você também pode entrar em contato com o cliente e pedir que eles [removam seu acesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configuração técnica

Esta seção do seu plano cria um manifesto com informações de autorização para gerenciar recursos do cliente. Essas informações são necessárias para habilitar o [Gerenciamento de recursos delegados do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Certifique-se de examinar [locatários, funções e usuários em cenários de Lighthouse do Azure](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais funções têm suporte e as práticas recomendadas para definir suas autorizações.

> [!NOTE]
> Como observado acima, os usuários e as funções em sua entradas **autorização** serão aplicadas a todos os clientes que comprarem o plano. Se você quiser limitar o acesso a um cliente específico, será necessário publicar um plano privado para uso exclusivo.

#### <a name="manifest"></a>Manifest

Primeiro, forneça uma **Versão** para o manifesto. Use o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira sua **ID de Locatário**. Este é um GUID associado à ID de locatário do Azure Active Directory (Azure AD) da sua organização; ou seja, o locatário de gerenciamento do qual você vai acessar os recursos de seus clientes. Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da conta no lado superior direito do portal do Azure ou selecionando o **Mudar diretório**.

Se você publicar uma nova versão da sua oferta e precisar criar um manifesto atualizado, selecione **+ novo manifesto**. Certifique-se de aumentar o número de versão da versão de manifesto anterior.

#### <a name="authorization"></a>Autorização

As autorizações definem as entidades em seu locatário de gerenciamento que podem acessar recursos e assinaturas para clientes que compram o plano. Cada uma dessas entidades é atribuída a uma função interna que concede níveis específicos de acesso.

Você pode criar até vinte autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, você desejará atribuir funções a um grupo de usuários ou entidade de serviço do Azure AD, em vez de uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados. Ao atribuir funções aos grupos do Azure AD, certifique-se de [que o **tipo de grupo** é **segurança** e não **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para obter recomendações adicionais, confira [Locatários, funções e usuários em cenários do Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Para cada **Autorização**, você precisará fornecer o seguinte. Em seguida, você pode selecionar **+ Adicionar autorização** quantas vezes forem necessárias para adicionar mais usuários e definições de função.

- **ID de objeto do Azure ad**: o identificador do Azure AD de um usuário, grupo de usuários ou aplicativo que receberá determinadas permissões (conforme definido pela definição de função) aos recursos de seus clientes.
- **Nome de exibição do objeto do Azure ad**: um nome amigável para ajudar o cliente a entender a finalidade dessa autorização. O cliente verá esse nome ao delegar recursos.
- **Definição de função**: selecione uma das funções internas do Azure ad disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID do Objeto do Azure AD** terá nos recursos dos clientes. Para obter descrições dessas funções, consulte [funções internas](../../role-based-access-control/built-in-roles.md) e [suporte de função para o gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Conforme as novas funções internas aplicáveis forem adicionadas ao Azure, elas ficarão disponíveis aqui, embora possa haver algum atraso antes que elas apareçam.
- **Funções atribuíveis**: essa opção será exibida somente se você tiver selecionado administrador de acesso de usuário na **definição de função** para essa autorização. Nesse caso, você deve adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **ID de objeto do Azure ad** poderá atribuir essas funções a [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md), o que é necessário para [implantar políticas que podem ser corrigidas](../../lighthouse/how-to/deploy-policy-remediation.md). Observe que nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

> [!TIP]
> Para garantir que você possa [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) , se necessário, inclua uma **autorização** com a **definição de função** definida como atribuição de registro de [Serviços gerenciados excluir função](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

Depois de concluir todas as seções para seu plano, você pode selecionar **+ criar novo plano** quantas vezes forem necessárias para criar planos adicionais. Quando terminar, selecione **Salvar**.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualização

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do Portal. Você será redirecionado para a página **revisar e publicar** .

Se for a primeira vez que publicar essa oferta, você poderá:

- Consulte o status de conclusão de cada seção da oferta.
  - *Não iniciado* -significa que a seção não foi tocada e precisa ser concluída.
  - *Incompleto* -significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para a (s) seção (ões) e atualize-a.
  - *Concluir* -significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.
- Na seção **notas de certificação** , forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Nós lhe enviaremos um email quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Retorne ao Partner Center e selecione **Go-Live** para a oferta para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

### <a name="customer-experience-and-offer-management"></a>Experiência do cliente e gerenciamento de ofertas

Quando um cliente implanta sua oferta, ele poderá delegar assinaturas ou grupos de recursos para o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Para obter mais informações sobre esse processo, consulte [o processo de integração do cliente](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

Você pode [publicar uma versão atualizada de sua oferta](update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](../../lighthouse/how-to/view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá examinar as alterações e decidir se querem atualizar para a nova versão.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
- [Saiba mais sobre o Azure Lighthouse](../../lighthouse/overview.md)