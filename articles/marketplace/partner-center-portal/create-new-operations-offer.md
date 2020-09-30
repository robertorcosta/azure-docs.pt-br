---
title: Criar uma oferta do Dynamics 365 for Operations no marketplace comercial
description: Como criar uma oferta do Dynamics 365 for Operations para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa de CSP (provedor de soluções na nuvem) usando o portal do marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 10b5553bea1e275a3a3cc77380ed9e16898e903a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533206"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Criar uma oferta do Dynamics 365 for Operations

Este tópico explica como criar uma oferta do Dynamics 365 for Operations. O Microsoft [Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) é um serviço de ERP (planejamento de recursos empresariais) que dá suporte a finanças avançadas, operações, manufatura e gerenciamento da cadeia de suprimentos. Todas as ofertas do Dynamics 365 for Operations devem passar pelo nosso processo de certificação.

Antes de começar, [crie uma conta do marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

>[!NOTE]
> Depois que uma oferta for publicada, as edições na oferta só serão atualizadas no Partner Center e na loja online depois que você enviar novamente a oferta para publicação.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Dynamics 365 for Operations**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- A ID da oferta combinada com a ID do Publicador deve ter menos de 40 caracteres de comprimento.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços. Por exemplo, se sua ID de editor for testpublisherid e você inserir **Test-offer-1** aqui, o endereço Web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como você deseja que clientes potenciais interajam com essa oferta da listagem?

Selecione a opção que você deseja usar para essa oferta.

#### <a name="get-it-now-free"></a>Obtenha agora (gratuitamente)

Liste sua oferta aos clientes gratuitamente.

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure AD (Azure Active Directory) para automatizar a criação da conta em seu aplicativo. A autenticação com contas Microsoft usando esse token não é um procedimento compatível.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato do cliente conectando seu sistema de CRM (gerenciamento de relacionamento com o cliente). Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do Marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre como configurar o CRM, confira [Clientes potenciais](#customer-leads).

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de demonstrar sua oferta para clientes potenciais oferecendo a eles a opção de experimentar antes de comprar, o que resulta no aumento da conversão e na geração de clientes potenciais altamente qualificados. Para saber mais, comece com [o que é Test Drive](../what-is-test-drive.md).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive**. Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, confira [Visão geral de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Essa página permite que você defina as categorias e os setores usados para agrupar sua oferta no Azure Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão do aplicativo

Insira o número de versão da sua oferta. Os clientes verão essa versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus termos e condições legais no campo **Termos e condições**. Você também pode fornecer a URL na qual os termos e condições podem ser encontrados. Os clientes precisarão aceitar esses termos antes de poderem testar a oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página exibe os idiomas em que a oferta será listada. Atualmente, **Inglês (Estados Unidos)** é a única opção disponível.

Você precisará definir detalhes do Marketplace (nome da oferta, descrição, imagens etc.) para cada idioma/mercado. Selecione o nome do idioma/mercado para fornecer essas informações.

> [!NOTE]
> O conteúdo de listagem de ofertas, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "Este aplicativo está disponível apenas em [idioma que não seja inglês]". Também é aceitável fornecer uma *URL de link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

Aqui está um exemplo de como as informações de oferta são exibidas no Microsoft AppSource:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Produtos
3. Categorias
4. Setores
5. Endereço de suporte (link)
6. Termos de uso
7. Política de privacidade
8. Nome da oferta
9. Capturas de tela/vídeos
10. Descrição

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto que você inseriu para o **Alias da oferta** quando você criou a oferta em questão, mas você pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de copyright). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição breve

Forneça uma descrição breve da sua oferta, com até 100 caracteres. Essa descrição pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no marketplace. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo funciona

Se você quiser permitir que os clientes saibam que seu aplicativo funciona com produtos específicos, insira até três nomes de produto aqui.

### <a name="support-urls"></a>URLs de suporte

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira a URL em que os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira a URL da política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e os regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contatos

Nessa seção, forneça o nome, o email e o número de telefone de um **contato de suporte** e de um **contato de engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na seção **Contato de suporte**, forneça a **URL de suporte** em que os parceiros do CSP podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, como white papers, folhetos, listas de verificação ou apresentações. Esses documentos precisam estar no formato PDF.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens precisam estar no formato .png.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pela Central de Parceiros.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center o usará para criar um logotipo **pequeno** . Você pode, opcionalmente, substituir isso por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de tela

Adicione capturas de tela que mostram como sua oferta funciona. Pelo menos uma captura de tela é necessária e você pode adicionar até cinco. Todas as capturas de tela precisam ter 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Opcionalmente, você pode adicionar até quatro vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, a respectiva URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

[Melhores práticas para listagens de oferta do marketplace](../gtm-offer-listing-best-practices.md)

Selecione **Salvar rascunho** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página fornece opções sobre onde e como tornar sua oferta disponível.

### <a name="markets"></a>Mercados

Esta seção permite que você especifique os mercados nos quais sua oferta deve estar disponível. Para fazer isso, selecione **Editar mercados**, que exibirá a janela pop-up **Seleção de mercado**.

Por padrão, nenhum mercado é selecionado. Selecione pelo menos um mercado para publicar sua oferta. Clique em **Selecionar todos** para disponibilizar sua oferta em todos os possíveis mercados ou selecione os mercados específicos que você deseja adicionar. Quando terminar, selecione **Salvar**.

Suas seleções aqui se aplicam somente a novas aquisições; se alguém já tiver seu aplicativo em um determinado mercado e você remover esse mercado posteriormente, as pessoas que já têm a oferta nesse mercado poderão continuar a usá-la, mas nenhum novo cliente nesse mercado poderá obter sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender aos requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que, mesmo que você selecione todos os mercados, as leis e restrições locais ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Público-alvo de versão prévia

Antes de publicar sua oferta como ativa para a oferta de Marketplace mais ampla, primeiro você precisará disponibilizá-la para um **público-alvo de versão prévia** limitado. Insira um **Ocultar chave** (qualquer cadeia de caracteres que use apenas letras minúsculas e/ou números) aqui. Os membros do seu público-alvo de versão prévia podem usar esse "ocultar chave" como um token para ver uma versão prévia da sua oferta no Marketplace.

Em seguida, quando estiver pronto para disponibilizar sua oferta e remover a restrição de versão prévia, você precisará remover o **ocultar chave** e publicar novamente.

Selecione **Salvar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configurações técnicas

Esta página define os detalhes técnicos usados para se conectar à sua oferta. Essa conexão nos permitirá provisionar sua oferta para o cliente final se ele optar por adquiri-la.

### <a name="solution-identifier"></a>Identificador da solução

Forneça o GUID (identificador de solução) para sua solução.

Para encontrar o identificador da solução:

1. No Microsoft Dynamics LCS (Lifecycle Services), selecione **Gerenciamento de Soluções**.
2. Selecione sua solução e procure o **Identificador da Solução** na **Visão geral do pacote**. Se o identificador estiver em branco, selecione **Editar** e republique seu pacote, depois tente novamente.

### <a name="release-version"></a>Versão de lançamento

Selecione a versão do Dynamics 365 for Finance and Operations com as quais essa solução funciona.

Selecione **Salvar rascunho** para continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

Esta página permite que você configure uma demonstração ("test drive") que permite que os clientes experimentem sua oferta antes de adquiri-la. Saiba mais em [o que é Test Drive](../what-is-test-drive.md).

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** na guia [Configuração da oferta](#test-drive). Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

Quando você terminar de configurar sua test drive, selecione **salvar rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo complementar

Esta página permite que você forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não são mostradas aos clientes nem publicadas no Marketplace.

### <a name="validation-assets"></a>Ativos de validação

Carregue um [CAR (relatório de análise de personalização)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) nesta seção. Esse relatório é gerado analisando seus modelos de personalização e extensão, com base em um conjunto predefinido de regras de melhores práticas.

Esse arquivo deve estar no formato .xls ou .xlsx. Se você tiver mais de um relatório, poderá carregar um arquivo .zip contendo todos os relatórios.

### <a name="does-solution-include-localizations"></a>A solução inclui localizações?

Selecione **Sim** se a solução permitir o uso de políticas e padrões locais (por exemplo, se ele acomoda as regras de folha de pagamento diferentes exigidas por países/regiões diferentes). Caso contrário, selecione **Não**.

### <a name="does-solution-enable-translations"></a>A solução permite traduções?

Responda **Sim** se o texto em sua solução puder ser traduzido para outros idiomas. Caso contrário, selecione **Não**.

Selecione **Salvar rascunho** para continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para versão prévia

Depois de concluir todas as seções necessárias da oferta, selecione **Examinar e publicar** no canto superior direito do portal.

Se for a primeira vez que você publicar essa oferta, você poderá:

- Ver o status de conclusão de cada seção da oferta.
    - **Não iniciada** – a seção não foi tocada e deve ser concluída.
    - **Incompleta** – a seção tem erros que precisam ser corrigidos ou requer que mais informações sejam fornecidas. Volte para as seções e atualize-as.
    - **Concluída** – a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você envie a oferta.
- Na seção **Notas para certificação**, forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de eventuais notas suplementares úteis para compreensão do seu aplicativo. Para obter mais informações sobre instruções de teste e como concluir uma primeira publicação, consulte [AppSource Dynamics 365 Finance and Operations funcional Validation](https://docs.microsoft.com/azure/marketplace/dynamics-365-finance-operations-functional-validation).
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para exame e aprovação. Retorne ao Partner Center e selecione **Go-Live** para a oferta para publicar sua oferta no público.

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
