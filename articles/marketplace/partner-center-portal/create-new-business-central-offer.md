---
title: Criar uma oferta do Dynamics 365 Business Central – marketplace comercial da Microsoft
description: Conheça as etapas e considerações para criar uma nova oferta do Dynamics 365 Business Central no portal do marketplace comercial no Partner Center. Você pode listar ou vender sua oferta no Azure Marketplace ou por meio do programa CSP (Provedor de Soluções na Nuvem).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 456764ae2b08b9852e56b2ab3f7cd9238b540b58
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121938"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Criar uma oferta do Dynamics 365 Business Central

Este artigo explica como criar uma oferta do Dynamics 365 Business Central. O [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planejamento de recursos empresariais (ERP) que lida com uma ampla gama de processos de negócios, incluindo finanças, operações, cadeia de suprimentos, CRM e gerenciamento de projetos e comércio eletrônico. Os pacotes Premium também têm suporte para a fabricação e o modelo de implantação clássico. Todas as ofertas do Dynamics 365 Business Central devem passar pelo nosso processo de certificação.

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Dynamics 365 Business Central**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em frentes de loja após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não poderá ser alterada depois que você selecionar a opção **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O Alias da oferta não poderá ser alterado depois que você selecionar a opção **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

Siga estas etapas para configurar sua oferta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como você deseja que clientes potenciais interajam com essa oferta da listagem?

Selecione a opção que você deseja usar para essa oferta.

#### <a name="get-it-now-free"></a>Obtenha agora (gratuitamente)

Liste sua oferta aos clientes gratuitamente fornecendo uma URL válida (começando com *http* ou *https*) em que eles podem acessar seu aplicativo.  Por exemplo, `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita fornecendo uma URL válida (a partir do `http` ou `https` ) onde eles podem obter uma avaliação.  Por exemplo, `https://contoso.com/trial/my-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure AD (Azure Active Directory) para automatizar a criação da conta em seu aplicativo. A autenticação com contas Microsoft usando esse token não é um procedimento compatível.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato do cliente conectando seu sistema de CRM (gerenciamento de relacionamento com o cliente). Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre como configurar o CRM, confira [Clientes potenciais](#customer-leads).

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

É aqui que você define detalhes da sua oferta, como nome, descrição e imagens.

> [!NOTE]
> Você pode fornecer detalhes da listagem da oferta em apenas um idioma. Eles não precisam estar em inglês, desde que a descrição da oferta inicie com a frase: “Este aplicativo só está disponível em [idioma, exceto inglês].” Também é aceitável fornecer uma *URL de link de ajuda* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Produtos
3. Categorias
4. Endereço de suporte (link)
5. Termos de uso
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Descrição
10. Capturas de tela/vídeos

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto que você inseriu para o **Alias da oferta** quando você criou a oferta em questão, mas você pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de copyright). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição breve

Forneça uma descrição breve da sua oferta, com até 100 caracteres. Essa descrição pode ser usada nos resultados da pesquisa do marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no marketplace. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo funciona

Se você quiser permitir que os clientes saibam que seu aplicativo funciona com produtos específicos, insira até três nomes de produto aqui.

### <a name="helpprivacy-urls"></a>URLs de ajuda/privacidade

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira a URL em que os clientes podem saber mais sobre sua oferta. O **Link de ajuda** não pode ser o mesmo que seu **URL de suporte** (explicado abaixo).

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira a URL da política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e os regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contact-information"></a>Informações de contato

Nessa seção, você deve fornecer o nome, o email e o número de telefone de um **Contato de suporte** e de um **Contato de engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na seção **Contato de suporte**, forneça também a **URL de suporte** em que os parceiros do CSP podem encontrar suporte para sua oferta. Sua URL de suporte não pode ser a mesma do seu **Link de ajuda**.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, como white papers, folhetos, listas de verificação ou apresentações. Esses documentos precisam estar no formato .pdf.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para sua oferta. O logotipo precisa estar no formato PNG. Carregue o logotipo da oferta em dois tamanhos:

* **Pequeno** (48 x 48 pixels)
* **Grande** (216 x 216 pixels)

>[!NOTE]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço `https://upload.xboxlive.com` que é usado pelo Partner Center.

#### <a name="screenshots"></a>Capturas de tela

Adicione capturas de tela que mostram como sua oferta funciona. Pelo menos três capturas de tela são necessárias e você pode adicionar até cinco. Todas as capturas de tela precisam ter 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Opcionalmente, você pode adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, a respectiva URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

[Melhores práticas para listagens de oferta do marketplace](../gtm-offer-listing-best-practices.md)

Selecione **Salvar rascunho** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página fornece opções sobre onde e como tornar sua oferta disponível.

### <a name="markets"></a>Mercados

Essa seção permite que você especifique os mercados nos quais sua oferta deve estar disponível. Para fazer isso, selecione **Editar mercados**, que exibirá a janela pop-up **Seleção de mercado**.

Selecione pelo menos um mercado para publicar sua oferta. Escolha **Selecionar todos** para disponibilizar sua oferta em todos os possíveis mercados ou selecione os mercados específicos que você deseja adicionar.

Suas seleções aqui se aplicam somente a novas aquisições; se alguém já tiver seu aplicativo em um determinado mercado e você remover esse mercado posteriormente, as pessoas que já têm a oferta nesse mercado poderão continuar a usá-la, mas nenhum novo cliente nesse mercado poderá obter sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender aos requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que, mesmo que você selecione todos os mercados, leis, restrições locais ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Público-alvo de versão prévia

Antes de publicar sua oferta como ativa para a oferta de marketplace mais ampla, primeiro você precisará disponibilizá-la para um **público-alvo de versão prévia** limitado. Insira um **Ocultar chave** (qualquer cadeia de caracteres que use apenas letras minúsculas e/ou números) aqui. Os membros do seu público-alvo de versão prévia podem usar essa opção de ocultar chave como um token para ver uma versão prévia da sua oferta no marketplace.

Em seguida, quando estiver pronto para disponibilizar sua oferta e remover a restrição de versão prévia, você precisará remover a opção **Ocultar chave** e publicar novamente.

Selecione **Salvar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configurações técnicas

Esta página define os detalhes técnicos usados para se conectar à sua oferta. Essa conexão nos permitirá provisionar sua oferta para o cliente final se ele optar por adquiri-la.

### <a name="package-type"></a>Tipo de pacote

Selecione a opção que se aplica à sua oferta:

* **Complemento** – um aplicativo de complemento estende a experiência e a funcionalidade existente do Dynamics 365 Business Central. Para obter detalhes, consulte [Aplicativos de complemento](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Conexão** – um aplicativo de conexão pode ser usado no cenário em que deve ser estabelecida uma conexão ponto a ponto entre o Dynamics 365 Business Central e uma solução ou um serviço de terceiros. Para obter detalhes, consulte [Aplicativos de conexão](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Upload de arquivos

Caso tenha selecionado a opção **Complemento** acima, veja em que local você carregará o arquivo de pacote da sua oferta, juntamente com os arquivos de pacote para qualquer extensão na qual ele tenha dependências.

#### <a name="extensions-package-file"></a>Arquivo de pacote de extensões

Carregue o arquivo de pacote de extensão (.app) da sua oferta.

#### <a name="library-package-file"></a>Arquivo de pacote da biblioteca

Será necessário caso sua oferta deva ser instalada junto com outra extensão que não será publicada no marketplace. Nesse caso, carregue seu arquivo .app aqui.

#### <a name="dependency-package-file"></a>Arquivo de pacote da dependência

Será necessário caso sua oferta deva ser instalada junto com outra extensão que já foi publicada no marketplace. Nesse caso, carregue seu arquivo `.app` ou `.zip` aqui.

### <a name="url-to-app-installation"></a>URL para a instalação do aplicativo

Caso tenha selecionado **Conexão** acima, forneça o endereço para a instalação do aplicativo aqui. Para serviços conectados que não requeiram instalação, forneça o endereço da página de aterrissagem ou da página de inscrição do serviço.

Selecione **Salvar rascunho** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

Esta página permite que você configure uma demonstração ("test drive") que permite que os clientes experimentem sua oferta antes de adquiri-la. Saiba mais em [o que é Test Drive](../what-is-test-drive.md).

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** na guia [Configuração da oferta](#test-drive). Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

Quando você terminar de configurar sua test drive, selecione **salvar rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo complementar

Esta página permite que você forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não são mostradas aos clientes nem publicadas no marketplace.

### <a name="target-release"></a>Versão de destino

Indique à qual versão do Microsoft Dynamics Business Central sua solução se destina: **Atual**, **Próxima importante** ou **Próxima secundária**. Essas informações permitem que testemos sua solução adequadamente.

### <a name="supported-editions"></a>Edições com suporte

Caso sua oferta exija a edição Premium do Microsoft Dynamics 365 Business Central, selecione apenas **Premium**. Caso contrário, selecione **Essentials** e **Premium**.

### <a name="key-usage-scenario"></a>Principais cenários de uso

Você deve carregar um arquivo PDF que lista os principais cenários de uso de sua oferta listados em um documento (formato .pdf). Todos os cenários listados aqui poderão ser verificados por nossa equipe de validação antes de aprovarmos sua oferta do marketplace.

### <a name="app-tests-automation"></a>Automação de testes do aplicativo

Caso sua oferta seja um aplicativo de complemento, você deverá carregar um arquivo de **Automação de testes de aplicativo** (.app). Esse arquivo não é aplicável para aplicativos de conexão.

### <a name="test-accounts"></a>Contas de teste

Caso uma conta de teste seja necessária para que nossa equipe de certificação examine sua oferta corretamente, carregue um arquivo .pdf, .doc ou .docx com suas **Contas de teste** informações.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para versão prévia

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do Portal. Você será redirecionado para a página **Revisar e publicar**. 

Se for a primeira vez que você publicar essa oferta, você poderá:

- Ver o status de conclusão de cada seção da oferta.
    - *Não iniciada* – significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleta* – significa que a seção tem erros que precisam ser corrigidos ou requer que mais informações sejam fornecidas. Volte para as seções e atualize-as.
    - *Concluída* – significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você possa enviar a oferta.
- Na seção **Notas para certificação**, forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de eventuais notas suplementares úteis para compreensão do seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Nós enviaremos um e-mail a você quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Retorne ao Partner Center e selecione **Go-Live** para a oferta para publicar sua oferta no público.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
