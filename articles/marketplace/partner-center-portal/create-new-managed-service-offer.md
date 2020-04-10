---
title: Crie uma nova oferta de Serviço Gerenciado no Mercado Comercial
description: Como criar uma nova oferta de Serviço Gerenciado para listagem no Azure Marketplace usando o portal Commercial Marketplace no Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 6baf5d77b70fa6e2717b492163826f27d80fbb88
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991613"
---
# <a name="create-a-new-managed-service-offer"></a>Crie uma nova oferta de serviço gerenciado

> [!IMPORTANT]
> Estamos mudando o gerenciamento de suas ofertas de Serviço Gerenciado do Portal de Parceiros na Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Publicar uma oferta de Serviço Gerenciado ao Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) para gerenciar suas ofertas no Cloud Partner Portal.

O Serviço Gerenciado oferece ajuda para habilitar cenários [do Farol Do Azure.](../../lighthouse/overview.md) Quando um cliente aceita uma oferta de Serviço Gerenciado, ele é capaz de embarcar recursos para [o gerenciamento de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Para começar a criar ofertas de Serviço Gerenciado, certifique-se de criar primeiro [uma conta do Partner Center](./create-account.md) e abrir o painel do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a página **Visão Geral** selecionada. Você deve ter um nível de competência da [Plataforma Silver ou Gold Cloud](https://partner.microsoft.com/membership/cloud-platform-competency) ou ser um [MSP especialista do Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerenciado.

![Painel de mercado comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Uma vez publicada uma oferta, as edições da oferta feita no Partner Center só serão atualizadas no sistema e nas fachadas das lojas após a republicação. Por favor, certifique-se de enviar a oferta para publicação depois de fazer alterações.

## <a name="create-a-new-offer"></a>Criar uma oferta

Selecione o botão **+ Novo oferta** e selecione o item menu Serviço **Gerenciado.** A caixa de diálogo **Nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>Oferecer ID e alias

- **ID de oferta**: Identificador exclusivo para cada oferta em sua conta. Este ID será visível para os clientes no endereço URL para a oferta de marketplace. Este ID só pode conter caracteres alfanuméricos minúsculos (incluindo hífens e sublinhados, mas sem espaço em branco), limitados a 50 caracteres, e não podem ser alterados depois de selecionar **Criar**.  Por exemplo, se você inserir *test-offer-1* aqui, o URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Ofereça alias**: O nome usado para se referir à oferta dentro do Partner Center. Este nome não será usado no mercado, e é diferente do nome da oferta e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **Criar**.

Depois de inserir seu **alias de ID de oferta** e **oferta,** selecione **Criar**. Você poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Configuração da oferta

A página **de configuração Oferta** pede as seguintes informações. Certifique-se de selecionar **Salvar** depois de concluir esses campos.

## <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Observe que, de acordo com as [políticas de certificação de Serviços Gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), é necessário um **Destino de Lead**. Isso criará um registro em seu sistema de CRM cada vez que um cliente implantar sua oferta.

Para obter mais informações, consulte [visão geral do gerenciamento de líderes](./commercial-marketplace-get-customer-leads.md).

Lembre-se **de salvar** a página antes de passar para a próxima seção.

## <a name="properties"></a>Propriedades

A página **Propriedades** permite definir as categorias usadas para agrupar sua oferta no mercado e os contratos legais que suportam sua oferta. Selecione **Salvar** depois de concluir esta página.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e no máximo cinco categorias que serão usadas para colocar sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de chamar como sua oferta suporta essas categorias na descrição da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus próprios termos e condições legais no campo **Termos e condições.** Você também pode fornecer a URL onde seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de tentar sua oferta.

## <a name="offer-listing"></a>Listagem de ofertas

A página **de listagem de ofertas** permite definir detalhes do mercado (oferecer nome, descrição, imagens, etc.) para sua oferta.

> [!NOTE]
> O conteúdo de listagem de ofertas (como a descrição, documentos, capturas de tela, termos de uso, etc.) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]." Também é aceitável fornecer uma *URL de Link útil* para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem de Oferta.

### <a name="name"></a>Nome

O nome que você inserir aqui será mostrado aos clientes como o título da sua lista de ofertas. Este campo está prepovoado com o texto que você inseriu para **oferecer alias** quando você criou a oferta, mas você pode alterar esse valor. Este nome pode ser registrado (e você pode incluir símbolos de marca ou direitos autorais). O nome não pode ser mais de 50 caracteres e não pode incluir nenhum emoji.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição de sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa de sua oferta (até 256 caracteres). Este longo resumo também pode ser usado em resultados de pesquisa de marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa de sua oferta (até 3.000 caracteres). Esta descrição será exibida aos clientes na visão geral da listagem do marketplace. Inclua a proposta de valor da sua oferta, os principais benefícios, as associações de categorias e/ou do setor, as oportunidades de compra no aplicativo e quaisquer divulgações necessárias.

Algumas dicas para escrever sua descrição:  

- Descreva claramente a proposição de valor da sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens em sua proposta de valor:
  - Descrição da oferta
  - O tipo de usuário que se beneficia da oferta
  - Necessidades do cliente ou dor que a oferta aborda
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados da pesquisa.  
- Não dependa de recursos e em funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você oferece.  
- Use o vocabulário específico do setor ou palavras com base no benefício tanto quanto possível.

Para tornar sua descrição de oferta mais atraente, use o rico editor de texto para formatar sua descrição.

![Usando o rico editor de texto](./media/text-editor2.png)

Use as seguintes instruções para usar o editor de texto rico:

- Para alterar o formato do seu conteúdo, destaque o texto que deseja formatar e selecionar um estilo de texto, conforme mostrado abaixo:

     ![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com uma numeração ou numerada ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para recusar](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Link da política de privacidade

Digite a URL na política de privacidade da sua organização (hospedada em seu site). Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Links úteis

Forneça documentos on-line suplementares opcionais sobre sua solução. Adicione links úteis adicionais clicando **+ Adicione um link**.

### <a name="contact-information"></a>Informações de contato

Nesta seção, você deve fornecer o nome, e-mail e número de telefone para um **contato de suporte** e um contato de **Engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas a parceiros CSP.

### <a name="support-urls"></a>Suporte a URLs

Se você tiver sites de suporte para **clientes azure global** e/ou **azure Government,** forneça esses URLs aqui.

### <a name="marketplace-images"></a>Imagens de mercado

Nesta seção, você pode fornecer logotipos e imagens que serão usadas ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato .png.

#### <a name="marketplace-logos"></a>Logotipos de marketplace

São necessários quatro tamanhos de logotipo: **Pequeno (40x40)**, **Médio (90x90)**, **Grande (115x115)** e **Largo (255x115)**. Siga estas diretrizes para seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
- As cores do tema do portal são branca e preta. Não use essas cores como a cor de fundo de seu logotipo. Use uma cor que destaque seu logotipo no portal. É recomendável usar cores primárias simples.
- Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não estão em branco, preto ou azul.
- A aparência de seu logotipo deve ser simples e evitar gradientes. Não use um fundo gradiente no logotipo.
- Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa.
- Verifique se o logotipo não está esticado.

#### <a name="screenshots"></a>Capturas de tela

Adicione cinco capturas de tela que mostram como sua oferta funciona. Todas as capturas de tela devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Você pode, opcionalmente, adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou Vimeo. Para cada um, digite o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

- [Melhores práticas para listagens de ofertas de marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Visualização

Antes de publicar sua oferta ao vivo para a oferta de mercado mais ampla, você primeiro precisará disponibilizá-la para um público de pré-visualização limitado. Isso permite que você confirme como você oferece aparece no Azure Marketplace antes de disponibilizá-lo aos clientes. As equipes de suporte e engenharia da Microsoft também poderão visualizar sua oferta durante esse período de pré-visualização.

Você pode definir o público de visualização digitando IDs de assinatura do Azure na seção **Visualização audiência.** Você pode inserir até 10 IDs de assinatura manualmente, ou carregar um arquivo .csv com até 100 IDs de assinatura.

Todos os clientes associados a essas assinaturas poderão ver a oferta no Azure Marketplace antes de entrar em operação. Certifique-se de incluir suas próprias assinaturas aqui para que você possa visualizar sua oferta.

## <a name="plan-overview"></a>Visão geral do plano

Cada oferta deve ter um ou mais planos (às vezes chamados de SKUs). Você pode adicionar vários planos para dar suporte a diferentes conjuntos de recursos com preços diferentes ou para personalizar um plano específico para um público seleto de clientes específicos. Os clientes podem ver os planos disponíveis para eles na oferta pai.

Na página **Visão geral do Plano,** selecione **+ Criar novo plano**. Em seguida, digite um **ID do plano** e um **nome do plano**. Ambos os valores só podem conter caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Esses valores podem ser visíveis para os clientes, e eles não podem ser alterados depois que você publicar a oferta.

Selecione **Criar** uma vez que você inseriu esses valores para continuar trabalhando em seu plano. Há três seções a serem concluídas: **Lista de planos,** **precificação e disponibilidade**e **configuração técnica.**

### <a name="plan-listing"></a>Listagem de planos

Primeiro, forneça um **resumo dos resultados da pesquisa** para o plano. Esta é uma breve descrição do seu plano (até 100 caracteres), que pode ser usado em resultados de pesquisa de marketplace.

Em seguida, digite uma **descrição** que forneça uma explicação mais detalhada do plano.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Atualmente, há apenas um modelo de precificação que pode ser usado para oferta de Serviço Gerenciado: **Traga sua própria licença (BYOL)**. Isso significa que você cobrará de seus clientes diretamente os custos relacionados a essa oferta, e a Microsoft não cobrará nenhum valor por você.

A seção **de visibilidade do Plano** permite que você indique se esse plano deve ser [privado](../../marketplace/private-offers.md). Se você deixar o Esta é uma caixa **de plano privado** desmarcada, seu plano não será restrito a clientes específicos (ou a um determinado número de clientes).

Para disponibilizar esse plano somente para clientes específicos, selecione **Sim**. Ao fazer isso, você precisará identificar os clientes fornecendo suas IDs de assinatura. Estes podem ser inseridos um por um (para até 10 assinaturas) ou carregando um arquivo .csv (para um máximo de 10.000 assinaturas em todos os planos). Inclua suas próprias assinaturas aqui para que você possa testar e validar a oferta.

> [!IMPORTANT]
> Uma vez que um plano tenha sido publicado como público, você não pode mudá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, você não pode restringir a disponibilidade a certos clientes ou mesmo a um certo número de clientes (embora você possa parar de vender o plano completamente se você optar por fazê-lo). Você pode [remover o acesso a uma delegação](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) depois que um cliente aceitar uma oferta somente se você incluiu uma **autorização** com a definição **de função** definida como Função [de Exclusão de Atribuição de Registro de Serviços Gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando você publicou a oferta. Você também pode entrar em contato com o cliente e pedir-lhe para [remover seu acesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configuração técnica

Esta seção do seu plano cria um manifesto com informações de autorização para o gerenciamento dos recursos do cliente. Essas informações são necessárias para permitir o [gerenciamento de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Certifique-se de rever [inquilinos, funções e usuários em cenários do Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais funções são suportadas e as melhores práticas para definir suas autorizações.

> [!NOTE]
> Como observado acima, os usuários e as funções em sua entradas **autorização** serão aplicadas a todos os clientes que comprarem o plano. Se você quiser limitar o acesso a um cliente específico, será necessário publicar um plano privado para uso exclusivo.

#### <a name="manifest"></a>Manifest

Primeiro, forneça uma **Versão** para o manifesto. Use o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira sua **ID de Locatário**. Este é um GUID associado ao ID de inquilino do Azure Active Directory (Azure AD) da sua organização; ou seja, o inquilino gerenciador a partir do qual você acessará os recursos de seus clientes. Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da conta no lado superior direito do portal do Azure ou selecionando o **Mudar diretório**.

Se você publicar uma nova versão da sua oferta e precisar criar um manifesto atualizado, selecione **+ Novo manifesto**. Certifique-se de aumentar o número da versão da versão manifesto anterior.

#### <a name="authorization"></a>Autorização

As autorizações definem as entidades em seu inquilino gestor que podem acessar recursos e assinaturas para os clientes que adquirirem o plano. Cada uma dessas entidades tem um papel embutido que concede níveis específicos de acesso.

Você pode criar até vinte autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, você vai querer atribuir funções a um grupo de usuários do Azure AD ou diretor de serviço, em vez de uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados. Ao atribuir funções a grupos AD do Azure, [certifique-se de que o **tipo de Grupo** é **Segurança** e não **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para obter recomendações adicionais, confira [Locatários, funções e usuários em cenários do Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Para cada **Autorização**, você precisará fornecer o seguinte. Em seguida, você pode selecionar **+ Adicionar autorização** quantas vezes necessário para adicionar mais usuários e definições de função.

- **ID do objeto Azure AD**: O identificador Azure AD de um usuário, grupo de usuário ou aplicativo que receberá certas permissões (conforme definido pela Definição de Função) aos recursos de seus clientes.
- **Nome de exibição de objeto AD do Azure :** Um nome amigável para ajudar o cliente a entender o propósito desta autorização. O cliente verá esse nome ao delegar recursos.
- **Definição de função**: Selecione uma das funções incorporadas do Azure AD disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID do Objeto do Azure AD** terá nos recursos dos clientes. Para descrições dessas funções, consulte [Funções incorporadas](../../role-based-access-control/built-in-roles.md) e [suporte de função para gerenciamento de recursos delegados do Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > À medida que novas funções incorporadas forem adicionadas ao Azure, elas estarão disponíveis aqui, embora possa haver algum atraso antes de aparecerem.
- **Funções atribuíveis**: Esta opção só aparecerá se você tiver selecionado administrador de acesso ao usuário na **Definição de Função** para esta autorização. Nesse caso, você deve adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **Azure AD Object ID** poderá atribuir essas funções a [identidades gerenciadas,](../../active-directory/managed-identities-azure-resources/overview.md)o que é necessário para [implantar políticas que possam ser corrigidas](../../lighthouse/how-to/deploy-policy-remediation.md). Observe que nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

> [!TIP]
> Para garantir que você possa [remover o acesso a uma delegação,](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) se necessário, inclua uma **autorização** com a definição **de função** definida como Função [de Exclusão de Atribuição de Registro de Serviços Gerenciados .](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

Depois de concluir todas as seções do seu plano, você pode selecionar **+ Criar um novo plano** quantas vezes precisar para criar planos adicionais. Quando terminar, selecione **Salvar**.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualizar

Depois de ter concluído todas as seções exigidas da oferta, selecione **publicar** no canto superior direito do portal. Você será redirecionado para a **página Revisar e publicar.**

Se é sua primeira vez publicando esta oferta, você pode:

- Consulte o status de conclusão de cada seção da oferta.
  - *Não iniciado* - significa que a seção não foi tocada e precisa ser concluída.
  - *Incompleto* - significa que a seção tem erros que precisam ser corrigidos ou requer mais informações a serem fornecidas. Volte para a seção(s) e atualize-a.
  - *Completo* - significa que a seção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em estado completo antes de você poder enviar a oferta.
- Na seção **Notas para certificação,** forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de quaisquer notas suplementares úteis para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail quando uma versão de pré-visualização da oferta estiver disponível para você revisar e aprovar. Retorne ao Partner Center e selecione **go-live** para a oferta de publicar sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="customer-experience-and-offer-management"></a>Experiência do cliente e gestão de ofertas

Quando um cliente implanta sua oferta, ele será capaz de delegar assinaturas ou grupos de recursos para [o gerenciamento de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Para obter mais informações sobre esse processo, consulte [O processo de onboarding do cliente.](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)

Você pode [publicar uma versão atualizada de sua oferta](update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](../../lighthouse/how-to/view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá examinar as alterações e decidir se querem atualizar para a nova versão.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
- [Saiba mais sobre o Farol azure](../../lighthouse/overview.md)