---
title: Criar uma oferta de serviço gerenciado no Microsoft Commercial Marketplace
description: Como criar uma nova oferta de serviço gerenciado para listagem no Azure Marketplace usando o portal do Marketplace comercial no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 34af52b32d9e31ad003c5e95e288f88b157a944a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710610"
---
# <a name="create-a-managed-service-offer"></a>Criar uma oferta de serviço gerenciado

O serviço gerenciado oferece ajuda para habilitar cenários do [Azure Lighthouse](../../lighthouse/overview.md). Quando um cliente aceita uma oferta de serviço gerenciado, ele é capaz de integrar recursos para [gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Antes de começar, [crie uma conta do marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

Você deve ter um [nível de competência de plataforma de nuvem Prata ou Ouro](https://partner.microsoft.com/membership/cloud-platform-competency) ou ser um [MSP Especialista em Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerenciado.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Serviço Gerenciado**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-managed-service.png)

>[!NOTE]
>Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

* Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
* Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
* A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

* Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
* O Alias da oferta não poderá ser alterado depois que você selecionar a opção **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Observe que, de acordo com as [políticas de certificação de Serviços Gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), é necessário um **Destino de Lead**. Isso criará um registro em seu sistema CRM cada vez que um cliente implantar sua oferta.

Para obter mais informações, confira [Visão geral de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite que você defina as categorias usadas para agrupar sua oferta no Marketplace e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione no mínimo uma e no máximo cinco categorias que serão usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Lembre-se de incluir como sua oferta é compatível com essas categorias na descrição da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus termos e condições legais no campo **Termos e condições**. Você também pode fornecer a URL em que os termos e condições podem ser encontrados. Os clientes precisarão aceitar esses termos antes de poderem testar a oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite que você defina detalhes do Marketplace (como nome da oferta, descrição e imagens) para sua oferta.

> [!NOTE]
> O conteúdo de listagem de ofertas, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "Este aplicativo está disponível apenas em [idioma que não seja inglês].” Também é aceitável fornecer uma *URL de link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

Aqui está um exemplo de como as informações de oferta são exibidas no portal do Azure:

:::image type="content" source="media/example-managed-services.png" alt-text="Ilustra como essa oferta aparece na portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Título
2. Descrição
3. Links úteis
4. Capturas de tela

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto que você inseriu para o **Alias da oferta** quando você criou a oferta em questão, mas você pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de copyright). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição breve da sua oferta (até 256 caracteres). Esse resumo longo também pode ser usado nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira a URL da política de privacidade da sua organização (hospedada no seu site). Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e os regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Links úteis

Forneça documentos complementares opcionais on-line sobre sua solução. Adicione links úteis adicionais clicando em **+ Adicionar um link**.

### <a name="contact-information"></a>Informações de contato

Nessa seção, você deve fornecer o nome, o email e o número de telefone de um **Contato de suporte** e de um **Contato de engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

### <a name="support-urls"></a>URLs de suporte

Se você tiver sites de suporte para **Clientes Globais do Azure** e/ou **Clientes do Azure Governamental**, forneça essas URLs aqui.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens precisam estar no formato .png.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pela Central de Parceiros.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Todas as capturas de tela precisam ter 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Opcionalmente, você pode adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, a respectiva URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

- [Melhores práticas para listagens de oferta do marketplace](../gtm-offer-listing-best-practices.md)

Selecione **Salvar rascunho** antes de continuar.

## <a name="preview"></a>Visualização

Antes de publicar sua oferta como ativa para a oferta de Marketplace mais ampla, primeiro você precisará disponibilizá-la para um público-alvo de versão prévia limitado. Isso permite que você confirme como a oferta é exibida no Azure Marketplace antes de disponibilizá-la para os clientes. As equipes de suporte e engenharia da Microsoft também poderão exibir a oferta durante esse período de versão prévia.

Você pode definir o público de visualização inserindo IDs de assinatura do Azure na seção **Pré-visualização de Público-alvo**. Você pode inserir até 10 IDs de assinatura manualmente ou carregar um arquivo. csv com até 100 IDs de assinatura.

Todos os clientes associados a essas assinaturas poderão exibir a oferta no Azure Marketplace antes que ela fique ativa. Certifique-se de incluir suas próprias assinaturas aqui para poder visualizar sua oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Cada oferta deve ter um ou mais planos (anteriormente chamados de SKUs). Você pode adicionar vários planos para dar suporte a diferentes conjuntos de recursos com preços diferentes ou para personalizar um plano específico para um público seleto de clientes específicos. Os clientes podem ver os planos disponíveis para eles na oferta pai.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](../private-offers.md).

Na página **Visão geral do plano**, selecione **+ Criar novo plano**. Em seguida, insira uma **ID de plano** e um **Nome de plano**. Ambos os valores podem conter apenas caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Esses valores podem ser visíveis para os clientes e não podem ser alterados depois que você publicar a oferta.

Selecione **Criar** depois de inserir esses valores para continuar trabalhando em seu plano. Há três seções a serem preenchidas: **Listagem de plano**, **Preços e disponibilidade** e **Configuração técnica**.

### <a name="plan-listing"></a>Listagem de planos

Primeiro, forneça um **Resumo dos resultados da pesquisa** para o plano. Esta é uma breve descrição do seu plano (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

Em seguida, insira uma **Descrição** que forneça uma explicação mais detalhada do plano.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Atualmente, há apenas um modelo de preços que pode ser usado para a oferta de serviço gerenciado: **Traga sua própria licença (BYOL)** . Isso significa que você cobrará de seus clientes diretamente os custos relacionados a essa oferta, e a Microsoft não cobrará nenhum valor por você.

A seção **Visibilidade do plano** permite que você indique se esse plano deve ser [Particular](../../marketplace/private-offers.md). Se você deixar a caixa **Este é um plano particular** desmarcada, seu plano não será restrito a clientes específicos (ou a um determinado número de clientes).

> [!NOTE]
> Não há suporte para planos privados com assinaturas estabelecidas por meio de um revendedor do programa CSP (provedor de soluções na nuvem).

Para disponibilizar esse plano somente para clientes específicos, selecione **Sim**. Ao fazer isso, você precisará identificar os clientes fornecendo suas IDs de assinatura. Eles podem ser inseridos um por um (para até 10 assinaturas) ou carregando um arquivo. csv (para um máximo de 10.000 assinaturas em todos os planos). Inclua suas próprias assinaturas aqui para que você possa testar e validar a oferta.

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Você pode [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) depois que um cliente aceitar uma oferta somente se você tiver incluído uma **Autorização** com a **Definição de função** definida como [Função de exclusão de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao publicar a oferta. Você também pode entrar em contato com o cliente e pedir a eles para [remover seu acesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configuração técnica

Esta seção do seu plano cria um manifesto com informações de autorização para gerenciar recursos do cliente. Essas informações são necessárias para habilitar o [gerenciamento de recursos delegados do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Certifique-se de examinar [Locatários, funções e usuários em cenários de Lighthouse do Azure](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais funções têm suporte e as práticas recomendadas para definir suas autorizações.

> [!NOTE]
> Como observado acima, os usuários e as funções em sua entradas **autorização** serão aplicadas a todos os clientes que comprarem o plano. Se você quiser limitar o acesso a um cliente específico, será necessário publicar um plano privado para uso exclusivo.

#### <a name="manifest"></a>Manifesto

Primeiro, forneça uma **Versão** para o manifesto. Use o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira sua **ID de Locatário**. Este é um GUID associado à ID de locatário do Azure Active Directory (Azure AD) da sua organização; ou seja, o locatário de gerenciamento do qual você vai acessar os recursos de seus clientes. Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da conta no lado superior direito do portal do Azure ou selecionando o **Mudar diretório**.

Se você publicar uma nova versão da sua oferta e precisar criar um manifesto atualizado, selecione **+ Novo manifesto**. Certifique-se de aumentar o número de versão da versão de manifesto anterior.

#### <a name="authorization"></a>Autorização

As autorizações definem as entidades em seu locatário de gerenciamento que podem acessar recursos e assinaturas para clientes que compram o plano. Cada uma dessas entidades é atribuída a uma função interna que concede níveis específicos de acesso.

Você pode criar até vinte autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, você desejará atribuir funções a um grupo de usuários ou entidade de serviço do Azure AD, em vez de uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados. Ao atribuir funções aos grupos do Azure AD, [certifique-se de que o **Tipo de grupo** é **Segurança** e não **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para obter recomendações adicionais, confira [Locatários, funções e usuários em cenários do Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Para cada **Autorização**, você precisará fornecer o seguinte. Em seguida, você pode selecionar **+ Adicionar autorização** quantas vezes forem necessárias para adicionar mais usuários e definições de função.

- **ID do Objeto do Azure AD**: O identificador do Azure AD de um usuário, grupo de usuários ou aplicativo que receberá determinadas permissões (conforme definido pela definição de função) aos recursos de seus clientes.
- **Nome de exibição do objeto do Azure AD**: um nome amigável para ajudar o cliente a entender a finalidade da autorização. O cliente verá esse nome ao delegar recursos.
- **Definição de Função**: Selecione uma das funções internas do Azure AD disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID do Objeto do Azure AD** terá nos recursos dos clientes. Para obter descrições dessas funções, consulte [funções internas](../../role-based-access-control/built-in-roles.md) e [suporte de função para o Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).
  > [!NOTE]
  > Conforme as novas funções internas aplicáveis forem adicionadas ao Azure, elas ficarão disponíveis aqui, embora possa haver algum atraso antes que elas apareçam.
- **Funções atribuíveis**: Essa opção será exibida somente se você tiver selecionado administrador de acesso de usuário na **Definição de função** para essa autorização. Nesse caso, você deve adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **ID de objeto do Azure AD** poderá atribuir essas funções para [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md), o que é necessário para [implantar políticas que podem ser remediadas](../../lighthouse/how-to/deploy-policy-remediation.md). Observe que nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

> [!TIP]
> Para garantir que você possa [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) se necessário, inclua uma **Autorização** com a **Definição de função** definida como [Função Excluir Atribuição de Registro de Serviços Gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

Depois de concluir todas as seções do plano, você poderá selecionar **+ Criar novo plano** quantas vezes precisar para criar planos adicionais. Quando terminar, selecione **Salvar**.

Selecione **Salvar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para versão prévia

Depois de concluir todas as seções necessárias da oferta, selecione **Revisar e publicar** no canto superior direito do Portal.

Se for a primeira vez que você publicar essa oferta, você poderá:

- Ver o status de conclusão de cada seção da oferta.
  - **Não iniciada** –  A seção não foi tocada e precisa ser concluída.
  - **Incompleta** –  A seção tem erros que precisam ser corrigidos ou requer que mais informações sejam fornecidas. Volte para as seções e atualize-as.
  - **Concluída** - A seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você possa enviar a oferta.
- Na seção **Notas para certificação**, forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de eventuais notas suplementares úteis para compreensão do seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Nós lhe enviaremos um e-mail quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Retorne ao Partner Center e selecione **Ativar** a oferta para publicar sua oferta para o público (ou, se uma oferta privada, para o público-alvo privado).

### <a name="customer-experience-and-offer-management"></a>Experiência do cliente e gerenciamento de ofertas

Quando um cliente implanta sua oferta, ele poderá delegar assinaturas ou grupos de recursos para [gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Para saber mais sobre esse processo, confira [O processo de integração do cliente](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

Você pode [publicar uma versão atualizada de sua oferta](update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](../../lighthouse/how-to/view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá revisar as alterações e decidir se deseja atualizar para a nova versão.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
- [Saiba mais sobre o Azure Lighthouse](../../lighthouse/overview.md)
