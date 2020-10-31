---
title: Criar uma oferta do Dynamics 365 for Customer Engagement e do PowerApps no marketplace comercial da Microsoft
description: Como criar uma nova oferta do Dynamics 365 for Customer Engagement e do PowerApps para listagem ou venda no Azure Marketplace, no AppSource ou por meio do programa CSP (Provedor de Soluções de Nuvem) no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 96bdfc513023cafb2d94a68c06f1065cf20c3ba8
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130163"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Criar uma oferta do Dynamics 365 for Customer Engagement e PowerApps

Este tópico explica como criar uma nova oferta do Dynamics 365 for Customer Engagement e do PowerApps. Todos os aplicativos para Dynamics 365 for Customer Engagement (PowerApps, Vendas, Serviço, Serviço de Projeto e Serviço de Campo) precisam passar pelo nosso processo de certificação e oferecer uma experiência de avaliação. O processo de certificação verifica sua solução quanto aos requisitos padrão, compatibilidade e práticas adequadas. A experiência de avaliação permite aos usuários implantar sua solução em um ambiente ativo do Dynamics 365.

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral** .
3. Na página Visão geral, selecione **+ Nova oferta** > **Dynamics 365 for Customer Engagement e PowerApps** .

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta** . Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- A ID da oferta combinada com a ID do Publicador deve ter menos de 40 caracteres de comprimento.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços. Por exemplo, se sua ID de editor for testpublisherid e você inserir **Test-offer-1** , o endereço Web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- A ID da oferta não pode ser alterada depois que você seleciona **Criar** .

Insira um **Alias da oferta** . Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Ele não poderá ser alterado depois que você selecionar **Criar** .

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

Siga estas etapas para configurar sua oferta.

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

Selecione **Salvar rascunho** antes de continuar.

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de demonstrar sua oferta para clientes potenciais oferecendo a eles a opção de experimentar antes de comprar, o que resulta no aumento da conversão e na geração de clientes potenciais altamente qualificados. Para saber mais, comece com [o que é Test Drive](../what-is-test-drive.md).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive** . Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, confira [Visão geral de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Essa página permite que você defina as categorias e os setores usados para agrupar sua oferta no Azure Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável** .

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Produtos aplicáveis do Dynamics 365

Selecione todos os produtos do Dynamics 365 a que essa oferta se aplica.

### <a name="app-version"></a>Versão do aplicativo

Insira o número de versão da sua oferta. Os clientes verão essa versão listada na página de detalhes da oferta. Se você estiver atualizando o número de versão apenas devido a alterações de marketing/descritivas, marque a caixa **Somente alterações de marketing** . Essa opção permite que a oferta ignore os estágios de certificação e provisionamento.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus próprios termos e condições legais aqui. Você também pode fornecer o endereço em que os termos e condições podem ser encontrados. Os clientes precisarão aceitar esses termos antes de poderem testar a oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página exibe os idiomas em que a oferta será listada. Atualmente, **inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do Marketplace para cada idioma/mercado aqui, como nome da oferta, descrição e imagens. Selecione o nome do idioma/mercado para fornecer essas informações.

> [!NOTE]
> O conteúdo de listagem de ofertas, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "Este aplicativo está disponível apenas em [idioma que não seja inglês]". Também é aceitável fornecer uma *URL de link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Produtos
3. Categorias
4. Endereço de suporte (link)
5. Endereço Termos de uso (link)
6. Nome da oferta
7. Descrição
8. Capturas de tela/vídeos

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto que você inseriu para o **Alias da oferta** quando você criou a oferta em questão, mas você pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de copyright). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição breve

Forneça uma descrição breve da sua oferta, com até 100 caracteres. Essa descrição pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no marketplace. Para obter melhores resultados, use essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo funciona

Se você quiser permitir que os clientes saibam que seu aplicativo funciona com produtos específicos, insira até três nomes de produto aqui.

### <a name="support-urls"></a>URLs de suporte

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira o endereço em que os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira o endereço da política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e os regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contatos

Forneça o nome, o email e o número de telefone de um **Contato de suporte** e de um **Contato de engenharia** . Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na seção **Contato de suporte** , forneça também a **URL de suporte** em que os parceiros do CSP podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, como white papers, folhetos, listas de verificação ou apresentações, em formato PDF.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para sua oferta. O logotipo precisa estar no formato PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

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

Essa página permite que você defina onde e como tornar sua oferta disponível.

### <a name="markets"></a>Mercados

Especifique os mercados nos quais sua oferta deve estar disponível. Para fazer isso, selecione **Editar mercados** , que exibirá a janela pop-up **Seleção de mercado** .

Nenhum mercado é selecionado por padrão, mas você deve selecionar pelo menos um mercado para publicar sua oferta. Escolha **Selecionar todos** para disponibilizar sua oferta em todos os possíveis mercados ou selecione os mercados específicos que você deseja adicionar.

Suas seleções aqui se aplicam somente a novas aquisições; se alguém já tiver seu aplicativo em um determinado mercado e você remover esse mercado posteriormente, as pessoas que já têm a oferta nesse mercado poderão continuar a usá-la, mas nenhum novo cliente nesse mercado poderá obter sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender aos requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center. Mesmo que você selecione todos os mercados, as leis e restrições locais ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Público-alvo de versão prévia

Antes de publicar sua oferta como ativa para a oferta de Marketplace mais ampla, primeiro você precisará disponibilizá-la para um **público-alvo de versão prévia** limitado. Insira um **Ocultar chave** (qualquer cadeia de caracteres que use apenas letras minúsculas e/ou números) aqui. Os membros do seu público-alvo de versão prévia podem usar esse "ocultar chave" como um token para ver uma versão prévia da sua oferta no Marketplace.

Em seguida, quando estiver pronto para disponibilizar sua oferta e remover a restrição de versão prévia, você precisará remover o **ocultar chave** e publicar novamente.

Selecione **Salvar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configurações técnicas

Esta página define os detalhes técnicos usados para se conectar à sua oferta. Essa conexão nos permitirá provisionar sua oferta para o cliente final se ele optar por adquiri-la.

### <a name="base-license-model"></a>Modelo de licença básica

O modelo de licença base determina como os clientes são atribuídos a seu aplicativo no Centro de Administração de CRM. Selecione **Recurso** para licenciamento baseado em instância ou **Usuário** se as licenças forem atribuídas uma por locatário.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Requer acesso de saída S2S e de Repositório Seguro do CRM

Marque a caixa para habilitar a configuração do Repositório Seguro de CRM ou o acesso S2S (servidor para servidor) de saída. É um recurso que exige uma atenção especializada da equipe do Dynamics 365 durante a fase de certificação. Microsoft entrará em contato com você para concluir as etapas adicionais para dar suporte ao recurso.

### <a name="application-configuration-url"></a>URL de configuração do aplicativo

Forneça a URL para a página da Web de configuração que permite ao cliente configurar seu aplicativo.

### <a name="crm-package"></a>Pacote CRM

No campo **URL do local do pacote** , insira a URL de uma conta do Armazenamento de Blobs do Azure que contenha o arquivo .zip do pacote CRM carregado. Inclua uma chave de SAS somente leitura na URL para que a Microsoft possa selecionar seu pacote para verificação.

> [!IMPORTANT]
> Para evitar a publicação de um bloco, verifique se a data de expiração na URL do seu Armazenamento de Blobs não expirou. Você pode revisar a data acessando sua política. Recomendamos que o **Tempo de validade** seja pelo menos um mês adiante.

Selecione a caixa rotulada **Há mais de um pacote CRM no arquivo de pacote** , se for o caso. Nesse caso, inclua todos os pacotes em seu arquivo .zip.

Para obter informações detalhadas sobre como criar seu pacote e atualizar sua estrutura, consulte [Etapa 3: Criar um pacote AppSource para seu aplicativo](/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Disponibilidade do pacote CRM

Nesta seção, selecione **+ Adicionar região** para especificar as regiões geográficas em que o pacote CRM estará disponível para os clientes. A implantação nas seguintes regiões soberanas exige permissão e validação especiais durante o processo de certificação: [Alemanha](../../germany/index.yml), [Nuvem do Governo dos EUA](../../azure-government/documentation-government-welcome.md) e TIP.

Por padrão, a **URL de configuração do aplicativo** que você inseriu acima será usada para cada região. Se preferir, você pode inserir uma URL de configuração de aplicativo diferente para uma ou mais regiões específicas. 

Selecione **Salvar rascunho** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

Esta página permite que você configure uma demonstração ("test drive") que permite que os clientes experimentem sua oferta antes de adquiri-la. Saiba mais em [o que é Test Drive](../what-is-test-drive.md).

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** na guia [Configuração da oferta](#test-drive). Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

Quando você terminar de configurar sua test drive, selecione **salvar rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo complementar

Esta página permite que você forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não são mostradas aos clientes nem publicadas no Marketplace.

### <a name="key-usage-scenario"></a>Principais cenários de uso

Carregue um arquivo PDF que liste os principais cenários de uso da sua oferta. Todos os cenários poderão ser verificados por nossa equipe de validação antes de aprovarmos sua oferta do Marketplace.

Selecione **Salvar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para versão prévia

Depois de concluir todas as seções necessárias da oferta, selecione **Examinar e publicar** no canto superior direito do portal.

Se for a primeira vez que você publicar essa oferta, você poderá:

- Ver o status de conclusão de cada seção da oferta.
    - **Não iniciada** : a seção não foi tocada e deve ser concluída.
    - **Incompleta** : a seção tem erros que precisam ser corrigidos ou requer que mais informações sejam fornecidas. Volte para as seções e atualize-as.
    - **Concluída** : a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você possa enviar a oferta.
- Na seção **Notas para certificação** , forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de eventuais notas suplementares úteis para compreensão do seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar** . Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para exame e aprovação. Retorne ao Partner Center e selecione **Go-Live** para que a oferta seja publicada no público.

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)