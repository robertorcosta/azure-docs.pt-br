---
title: Criar uma oferta de aplicativo do Azure - Marketplace comercial da Microsoft
description: Conheça as etapas e considerações para criar uma nova oferta de aplicativo do Azure no portal do Marketplace comercial no Partner Center. Você pode listar ou vender sua oferta de aplicativo do Azure no Azure Marketplace ou por meio do programa CSP (provedor de soluções na nuvem).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: fb3a3ab5339186d8fa4e347d9d13e66940457f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710712"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicativo do Azure

Este artigo explica as etapas e considerações para criar uma nova oferta de aplicativo do Azure no portal do marketplace comercial. Você deve estar familiarizado com esses conceitos antes de criar uma nova oferta de aplicativo do Azure.

Antes de você publicar uma nova oferta de aplicativo do Azure, [crie uma conta do marketplace comercial no Partner Center](create-account.md) e verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Projetar, criar e testar ofertas do aplicativo Azure exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta. A equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/).
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/).
* Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento prático de [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Examine os seguintes recursos ao preparar sua oferta de aplicativo do Azure para o Marketplace comercial.

* [Compreender os modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

* Inícios Rápidos:

    * [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    * [Guia de práticas recomendadas de modelos do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Publicar definição do aplicativo](../../managed-applications/publish-service-catalog-app.md)
    * [Implantar aplicativo do catálogo de serviços](../../managed-applications/deploy-service-catalog-quickstart.md)

* Tutoriais:

    * [Criar arquivos de definição](../../managed-applications/publish-service-catalog-app.md)
    * [Publicar o aplicativo do Marketplace](../../managed-applications/publish-marketplace-app.md)

* Exemplos:

    * [CLI do Azure](../../managed-applications/cli-samples.md)
    * [PowerShell do Azure](../../managed-applications/powershell-samples.md)
    * [Soluções de aplicativo gerenciado](../../managed-applications/sample-projects.md)

O vídeo [Criação de modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) fornece uma introdução abrangente ao tipo de oferta de aplicativo do Azure:

* Quais tipos de oferta estão disponíveis
* Quais ativos técnicos são necessários
* Como criar um modelo de Azure Resource Manager
* Desenvolvendo e testando a interface do usuário do aplicativo
* Como publicar a oferta de aplicativo
* O processo de revisão do aplicativo

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar seu aplicativo Azure:

* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/)
* [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Gerenciador de Armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as extensões a seguir:
    * Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Analise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/). Além disso, se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicativos do Azure

Há dois tipos de aplicativos do Azure: aplicativos gerenciados e modelos de solução.

* Os **modelos de solução** são uma das principais formas de publicar uma solução no Marketplace. Use esse tipo de plano quando sua solução requer implantação adicional e automação de configuração de mais de uma única VM (máquina virtual). Com um modelo de solução, você pode automatizar o fornecimento de mais de um recurso, incluindo VMs, redes e recursos de armazenamento para fornecer soluções complexas de IaaS.  Para obter mais informações sobre como criar modelos de solução, confira [Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md).

* **Aplicativos gerenciados** são semelhantes a modelos de solução do mercado, com uma diferença importante. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução. Use os aplicativos gerenciados do Azure para criar e entregar facilmente aplicativos totalmente gerenciados e prontos para uso aos clientes.  Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, confira a [Visão geral dos aplicativos gerenciados do Azure](../../managed-applications/overview.md).

## <a name="technical-requirements"></a>Requisitos técnicos

Todos os aplicativos do Azure incluem pelo menos dois arquivos na pasta raiz de um arquivo de `.zip`:

* Um arquivo de modelo do Resource Manager chamado [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md).  Este modelo define os recursos a serem implantados na assinatura do Azure do cliente. Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) ou o [GitHub correspondente: Modelos de Início Rápido do Azure Resource Manager](https://github.com/azure/azure-quickstart-templates).

* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure denominada [createUiDefinition.json](../../managed-applications/create-uidefinition-overview.md).  Na interface do usuário, você especifica os elementos que permitem aos consumidores fornecer valores de parâmetro.

Todas as novas ofertas de aplicativos do Azure devem incluir um [GUID de atribuição de uso do cliente de parceiro do Azure](../azure-partner-customer-usage-attribution.md). 

Para saber mais sobre os requisitos de publicação para cada plano de aplicativo, consulte [Requisitos de publicação de oferta de modelo de solução](../marketplace-solution-templates.md) e [Requisitos de publicação de oferta de aplicativo gerenciado](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Criar uma oferta

>[!NOTE]
>Depois que uma oferta for publicada, as edições feitas nela no Partner Center não aparecerão em lojas online até que você Republique a oferta. Sempre republique uma oferta depois de fazer alterações nela.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.

1. Na página Visão geral, selecione **+ Nova oferta** > **Aplicativo do Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-azure-app.png)

1. Na página **Nova oferta**, insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

     * Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
     * Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
     * A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

1. Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

     * Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
     * O Alias da oferta não pode ser alterado depois que você seleciona **Criar**.

1. Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

A página **Configuração de oferta** é onde você pode configurar um test drive e gerenciamento de leads para sua oferta.

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de demonstrar sua oferta para clientes potenciais oferecendo a eles a opção de experimentar antes de comprar, o que resulta no aumento da conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives](../what-is-test-drive.md).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive**. Para remover o test drive de sua oferta, desmarque essa caixa de seleção. Configure o ambiente de test drive na seção [Configuração técnica do test drive](#test-drive-technical-configuration), mais adiante neste tópico.

Para obter informações adicionais, confira [Fazer test drive de sua oferta no marketplace comercial](test-drive.md). Você também pode ler sobre [melhores práticas de test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e baixar o [PDF de visão geral do test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (verifique se o bloqueador de pop-ups está desativado)

>[!Note]
>Como todos os aplicativos do Azure são implementados usando um modelo de Azure Resource Manager, o único tipo de test drive disponível para um Aplicativo Azure é um [test drive baseado no Azure Resource Manager](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, confira [Visão geral de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

A página de **Propriedades** é onde você define as categorias usadas para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="legal"></a>Legal

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Listagem de ofertas

Esta página é onde você gerencia a cópia e as imagens da oferta do marketplace comercial.

### <a name="marketplace-details"></a>Detalhes do marketplace

> [!NOTE]
> O conteúdo de listagem de ofertas, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "Este aplicativo está disponível apenas em [idioma que não seja inglês]". Também é aceitável fornecer uma *URL de link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

Aqui está um exemplo de como as informações de oferta são exibidas no Azure Marketplace (os preços listados são apenas para fins de exemplo e não se destinam a refletir os custos reais):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Categorias
3. Endereço de suporte (link)
4. Termos de uso
5. Endereço de política de privacidade (link)
6. Nome da oferta
7. Resumo
8. Descrição
9. Capturas de tela/vídeos

<br>Aqui está um exemplo de como as informações de oferta são exibidas no portal do Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Título
2. Descrição
3. Links úteis
4. Capturas de tela

#### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto que você inseriu para o **Alias da oferta** quando você criou a oferta em questão, mas você pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de copyright). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma descrição breve da sua oferta, com até 100 caracteres. Essa descrição pode ser usada nos resultados da pesquisa.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição breve da sua oferta, com até 256 caracteres. Essa descrição pode ser usada nos resultados da pesquisa.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Palavras-chave para pesquisa

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no marketplace. Para obter melhores resultados, use essas palavras-chave em sua descrição também.

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira a URL da política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e os regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Links úteis

Adicione links a documentos opcionais online complementares sobre sua solução selecionando **+ Adicionar um link**.

### <a name="contact-information"></a>Informações de contato

Forneça o nome, o email e o número de telefone de um **contato de suporte**, **contato de engenharia** e **contato de programa CSP**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP. Alguns contatos podem exigir informações adicionais.

### <a name="marketplace-media"></a>Mídia do marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela precisa ter 1280 x 720 pixels de tamanho e estar no formato PNG. Cada captura de tela precisa incluir uma legenda.

#### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstrem sua oferta. Os vídeos devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço Web e uma imagem PNG em miniatura do vídeo em 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

- [Melhores práticas para listagens de oferta do marketplace](../gtm-offer-listing-best-practices.md)

Selecione **Salvar rascunho** antes de continuar.

## <a name="preview-audience"></a>Público-alvo de versão prévia

Selecione a guia Versão prévia e, em seguida, selecione um **Público-alvo de versão prévia** para validar sua oferta antes de publicá-la como ativa para o público-alvo comercial em geral.

> [!IMPORTANT]
> Depois de verificar sua oferta no painel Versão prévia, selecione **Ativar** para publicar sua oferta para o público-alvo do marketplace comercial.

Seu público-alvo de versão prévia é identificado por GUIDs de ID da assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. É possível encontrar a ID da assinatura na página **Assinaturas** no portal do Azure.

Adicione pelo menos uma ID da assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você define quem pode ter acesso à versão prévia de sua oferta antes que ela seja publicada como ativa. Se sua oferta já estiver ativa, você ainda poderá definir um público-alvo de versão prévia para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público-alvo de versão prévia difere de um público-alvo privado. Um público-alvo de versão prévia pode acessar sua oferta *antes* de ela ser publicada como ativa nos marketplaces. O público-alvo de versão prévia pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público-alvo privado depois que sua oferta for publicada integralmente no marketplace. Um público-alvo privado (definido na guia do plano **Preços e Disponibilidade**) tem acesso exclusivo a um plano específico.

Selecione **Salvar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Conclua esta seção somente se sua oferta incluir um aplicativo gerenciado que emitirá eventos de medição usando a API do serviço de medição do Marketplace. Insira a **ID do locatário do Azure Active Directory** e a **ID do aplicativo do Azure Active Directory** que seu serviço usará ao emitir eventos de medição.

Selecione **Salvar rascunho** antes de continuar.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível da oferta)

>[!Note]
>Os detalhes técnicos de nível de oferta são opcionais.  Você só precisará configurar esses detalhes se estiver publicando um aplicativo gerenciado com cobrança limitada e tiver um serviço que será autenticado com um token de segurança do Azure AD. Para obter mais informações, consulte [estratégias de autenticação](./marketplace-metering-service-authentication.md) nas diferentes opções de autenticação.

A configuração técnica define os detalhes (ID do locatário e ID do aplicativo) usados para identificar seu serviço, o que emitirá eventos de medição para um aplicativo gerenciado usando as [APIs do serviço de medição Marketplace](./marketplace-metering-service-apis.md).  Insira a identidade que seu serviço usará ao emitir eventos de medição.

* **ID do locatário do Azure ad** (obrigatório): dentro do portal do Azure, você deve [criar um aplicativo Azure Active Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) para que possamos validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para localizar a [ID de locatário](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)), vá para o Azure Active Directory e selecione **Propriedades**e procure o número de **ID de diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).
* **ID do aplicativo do Azure ad** (obrigatório): você também precisa da [ID do aplicativo](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) e de uma chave de autenticação. Para obter esses valores, vá até o Azure Active Directory e selecione **Registros de aplicativo**,depois procure o número da **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para localizar a chave de autenticação, acesse **Configurações** e selecione **Chaves**. Você precisará fornecer uma descrição e uma duração e, em seguida, um valor numérico será fornecido.

>[!Note]
>A ID do aplicativo do Azure será associada à sua ID do editor e só poderá ser usada nesta conta do editor.

>[!Note]
>Essa configuração será necessária se você quiser usar o [evento de uso do lote](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)).  Caso queira enviar o evento de [uso](marketplace-metering-service-apis.md#metered-billing-single-usage-event)), você também pode usar o [serviço de metadados de instância](../../active-directory/managed-identities-azure-resources/overview.md) para obter o token de [portador JWT (token Web JSON)](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)).

## <a name="plan-overview"></a>Visão geral do plano

Esta guia permite que você forneça opções de plano diferentes na mesma oferta. Esses planos (anteriormente chamados de SKUs) podem diferir em termos de tipo de plano (modelo de solução versus aplicativo gerenciado), monetização ou público. Configure pelo menos um plano para poder vender sua oferta no Marketplace.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](../private-offers.md).

Depois de criado, você verá nomes, IDs, modelos de preços, disponibilidade (pública ou privada), status de publicação atual e ações disponíveis do seu plano.

As **ações** disponíveis no painel **Visão geral do plano** variam de acordo com o status atual do plano e podem incluir:

* Se o status do plano for **Rascunho** – Excluir rascunho.
* Se o status do plano for **Ativo** – Interromper o plano de venda ou Sincronizar público-alvo privado.

### <a name="create-new-plan"></a>Criar novo plano

***ID do plano*** - Crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes na URL do produto.  Use apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. Essa ID de plano pode conter no máximo 50 caracteres. A ID não pode ser modificada após a seleção de "criar".

***Nome do plano*** – os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome da oferta exclusivo para cada plano nessa oferta. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

Esta guia permite que você defina a configuração de alto nível para o tipo de plano, se ele reutiliza pacotes de outro plano e em quais nuvens o plano deve estar disponível. Suas respostas nessa guia afetarão quais campos serão exibidos em outras guias para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano
Selecione o tipo de plano para sua oferta. Um plano de **Modelo de solução** é gerenciado inteiramente pelo cliente. Um **Aplicativo gerenciado** permite que o publicador gerencie o aplicativo em nome do cliente. Para obter mais detalhes, consulte os [Tipos de planos de aplicativos do Azure](#types-of-azure-application-plans)

#### <a name="re-use-technical-configuration"></a>Reutilizar configurações técnicas

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **Este plano reutiliza pacotes de outro plano**.  Ao selecionar essa opção, você poderá selecionar um dos outros planos do mesmo tipo dos quais esta oferta para reutilizará pacotes.

>[!Note]
>Quando você reutiliza pacotes outro plano, toda a guia de Configuração técnica desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações que você fizer no futuro, também serão usadas para esse plano.<br><br>Essa configuração não pode ser alterada após a publicação do plano.

#### <a name="azure-regions-cloud"></a>Regiões do Azure (nuvem)

Seu plano precisa ser disponibilizado em pelo menos uma região do Azure.

Selecione a opção **Azure Global** para disponibilizar seu plano para clientes em todas as regiões do Azure Global que têm integração com o Marketplace comercial. Para obter mais informações, confira [Disponibilidade geográfica e suporte a moeda](../marketplace-geo-availability-currencies.md).

Selecione a opção **Azure Government** para disponibilizar seu plano na região [Azure Government](../../azure-government/documentation-government-welcome.md). Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como para parceiros qualificados para atendê-los. Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar para o [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação do [Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Depois que o plano for publicado e estiver disponível em uma região específica do Azure, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só estará visível se você tiver selecionado **Azure governamental**.

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis somente para clientes do Azure Government.

Selecione **Salvar rascunho** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

Nesta seção, você configurará os detalhes da listagem do plano. Essa guia exibe informações específicas, que podem ser diferentes de outros planos na mesma oferta.

#### <a name="plan-name"></a>Nome do Plano

Esse campo é preenchido com o nome que você deu ao seu plano quando o criou. Esse nome aparece no Marketplace como o título deste plano e é limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não da oferta. Este resumo é limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo e descreva as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **Salvar rascunho** antes de continuar.

### <a name="availability-solution-template-plans-only"></a>Disponibilidade (somente planos de modelo de solução)

Você pode tornar o plano visível para todos, somente para clientes específicos (um público privado) e decidir se deseja tornar o plano oculto para uso somente por outros modelos de solução ou aplicativos gerenciados.

#### <a name="plan-visibility"></a>Visibilidade do plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público-alvo específico de sua escolha. Você pode atribuir uma associação a esse público restrito usando IDs de locatário do Azure AD.

Selecione **Este é um plano privado** para tornar seu plano privado e visível somente para o público-alvo restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público-alvo ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele precisa permanecer visível para todos e não pode ser configurado como um plano privado novamente.

Se você tornar o plano privado, insira um **IDs de assinatura do Azure** e sua descrição. Cada um deles é um público que terá acesso a esse plano privado. O acesso é atribuído usando IDs de locatário com a opção de incluir uma descrição de cada ID do locatário atribuída. Adicione até 10 clientes IDs de assinatura individualmente ou 20.000 importando um arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

>[!Note]
>Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta _antes_ de ela ser publicada como ativa nos marketplaces. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

#### <a name="hide-plan"></a>Ocultar plano

Se o seu modelo de solução pretende ser implantado apenas indiretamente quando for referenciado em outro modelo de solução ou aplicativo gerenciado, marque esta caixa para publicar o modelo de solução, mas ocultá-lo de clientes pesquisando e navegando diretamente.

Selecione **Salvar rascunho** antes de continuar.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Preços e disponibilidade (somente planos de aplicativo gerenciados)

Use-o para configurar os **Mercados** em que esse plano estará disponível, os **Preços** por mês do gerenciamento da solução e a **Visibilidade do plano** se apenas clientes específicos devem vê-lo (um público privado).

Selecione **Salvar rascunho** antes de continuar.

#### <a name="markets"></a>Mercados

Cada plano precisa estar disponível em pelo menos um mercado. Marque a caixa de seleção para qualquer local de mercado onde você gostaria de disponibilizar esse plano. Para seu auxílio, estão incluídos uma caixa de pesquisa e um botão para selecionar países/regiões de "imposto remetido", nos quais a Microsoft remete imposto sobre vendas e uso em seu nome.

Se você já tiver definido preços para seu plano em moeda de dólar americano (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Os preços podem ser revisados usando o link "Preços de exportação (xlsx)" depois de salvar as alterações.

#### <a name="pricing"></a>Preços

Forneça o preço por mês para este plano.  Esse preço é além de qualquer infraestrutura do Azure ou custos de software pago conforme o uso incorridos pelos recursos implantados por essa solução.

Além do preço por mês, você também pode definir preços para consumo de unidades não padrão usando [cobrança limitada](./azure-app-metered-billing.md).  Você pode definir o preço por mês como zero e cobrar exclusivamente usando a cobrança limitada, se desejar.

Os preços definidos em US$ (US$ = dólares americanos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar, exportando a planilha de preços e examinando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços.

>[!Note]
>É necessário primeiro salvar as alterações de preços para habilitar a exportação de dados sobre preços.

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado.  

>[!Note]
>Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

#### <a name="plan-visibility"></a>Visibilidade do plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público-alvo específico de sua escolha. Você pode atribuir uma associação a esse público restrito usando IDs de locatário do Azure AD.

Selecione **Este é um plano privado** para tornar seu plano privado e visível somente para o público-alvo restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público-alvo ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele precisa permanecer visível para todos e não pode ser configurado como um plano privado novamente.

>[!Note]
>Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta _antes_ de ela ser publicada como ativa nos marketplaces. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

Se você tornar o plano privado, insira um **IDs de assinatura do Azure** e sua descrição. Cada um deles é um público que terá acesso a esse plano privado. O acesso é atribuído usando IDs de locatário com a opção de incluir uma descrição de cada ID do locatário atribuída. Adicione até 10 clientes IDs de assinatura individualmente ou 20.000 importando um arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

>[!Note]
>Não há suporte para ofertas privadas com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).

### <a name="technical-configuration"></a>Configuração técnica

Esta guia permite que você carregue o pacote de implantação que permitirá que os clientes implantem seu plano.

>[!Note]
>Essa guia não estará visível se você tiver configurado este plano para reutilizar pacotes de outro plano na guia **Configuração do plano**.

#### <a name="package-details"></a>Detalhes do pacote

Esta guia permite que você edite a versão de rascunho de sua configuração técnica.

**Versão** – Atribua a versão atual da configuração técnica.  Aumente essa versão sempre que publicar uma alteração nesta página. A versão deve estar no formato `{integer}.{integer}.{integer}`.

**Arquivo de pacote** (.zip) – Este pacote contém todos os arquivos de modelo necessários para esse plano, bem como quaisquer recursos adicionais, empacotados como um arquivo de `.zip`.

Todos os pacotes de plano de aplicativos do Azure devem incluir pelo menos dois arquivos na pasta raiz de um arquivo de `.zip`:

* Um arquivo de modelo do Resource Manager chamado [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md). Este modelo automatiza a implantação de recursos na assinatura do Azure dos clientes.  Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou o [GitHub correspondente: Modelos de Início Rápido do Azure Resource Manager](https://github.com/azure/azure-quickstart-templates).
* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure denominada [createUiDefinition.json](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

Os tamanhos máximos de arquivo com suporte são:

* Até 1 GB no tamanho total de arquivo compactado `.zip`
* Até 1 GB para qualquer arquivo não compactado individual dentro do arquivo de `.zip`  

Todas as novas ofertas de aplicativos do Azure devem incluir um GUID [atribuição de uso do cliente de parceiro do Azure](../azure-partner-customer-usage-attribution.md).

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

Os planos de aplicativos gerenciados exigem informações adicionais nesta guia.

#### <a name="previously-published-packages"></a>Pacotes publicados anteriormente

A subguia **Pacotes publicados anteriormente** permite exibir todas as versões publicadas de sua configuração técnica.

#### <a name="enable-just-in-time-jit-access"></a>Habilitar acesso JIT (Just-In-Time)

Selecione esta opção para habilitar o acesso JIT (just-in-time) para este plano.  O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso.  Para obter mais informações, consulte [Habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure](../../managed-applications/request-just-in-time-access.md).  Para exigir que consumidores do seu aplicativo gerenciado permitam à sua conta acesso permanente, deixe essa opção desmarcada.

>[!Note]
>Certifique-se de atualizar o arquivo de `createUiDefinition.json` para dar suporte a esse recurso.  

#### <a name="deployment-mode"></a>Modo de implantação

Selecione se deseja configurar o modo de implantação **Completo** ou **Incremental** ao implantar este plano: 

* Em **modo completo**, uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no grupo de recursos gerenciados se os recursos não estiverem definidos no `mainTemplate.json`. 
* Em **modo incremental**, uma reimplantação do aplicativo deixa os recursos existentes inalterados.

Para saber mais sobre modos de implantação, confira [Modos de implantação do Azure Resource Manager](../../azure-resource-manager/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>URL do ponto de extremidade de notificação

Especifique um ponto de extremidade de Webhook HTTPS opcional para receber notificações sobre todas as operações de CRUD nas instâncias de aplicativo gerenciadas dessa versão do plano.

#### <a name="customize-allowed-customer-actions"></a>Personalizar ações de clientes permitidas

Selecione esta opção para especificar quais ações os clientes podem executar nos recursos gerenciados, além das ações "`*/read`" que estão disponíveis por padrão.

Liste as ações adicionais que você gostaria de permitir que o cliente execute aqui, separado por ponto-e-vírgula.  Para obter mais informações, confira [Compreender atribuições de negação de recursos do Azure](../../role-based-access-control/deny-assignments.md). Para as ações disponíveis, confira [Operações do provedor de recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Por exemplo, para permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

#### <a name="global-azure--azure-government-cloud"></a>Nuvem do Azure Governamental / Azure Global

Indique quem deve ter acesso de gerenciamento a esse aplicativo gerenciado em cada nuvem com suporte. Usuários, grupos ou aplicativos para os quais você deseja receber permissão para o grupo de recursos gerenciados são identificados usando identidades de Azure Active Directory (AD).

**Azure Active Directory ID de locatário** – a ID de locatário do Azure AD (também conhecida como ID de diretório) que contém as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões. Você pode encontrar sua ID de locatário do Azure AD na portal do Azure, em [Propriedades para Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizações** - Adicione a ID do objeto do Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. Identifique o usuário por sua ID de entidade de segurança, que pode ser encontrada na [folha de usuários do Azure Active Directory no portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada entidade de segurança, selecione uma das funções internas do Azure AD na lista (proprietário ou colaborador). A função selecionada descreverá as permissões que a entidade de segurança terá sobre os recursos na assinatura do cliente. Para obter mais informações, veja [Funções internas do Azure](../../role-based-access-control/built-in-roles.md). Para obter mais informações sobre controle de acesso baseado em função (RBAC), consulte [Introdução ao RBAC no portal do Azure](../../role-based-access-control/overview.md).

>[!Note]
>Embora você possa adicionar até 100 autorizações por nuvem, geralmente é mais fácil criar um grupo de usuários do Active Directory e especificar sua ID na "ID da entidade de segurança". Isso permitirá que você adicione mais usuários ao grupo de gerenciamento depois que o plano for implantado e reduza a necessidade de atualizar o plano apenas para adicionar mais autorizações.

#### <a name="policy-settings"></a>Configurações de política

Aplique uma [Política do Azure](../../governance/policy/overview.md) ao aplicativo gerenciado para especificar os requisitos de conformidade para as soluções implantadas. Para definições de política e o formato dos valores de parâmetro, veja [Exemplos de Azure Policy](../../governance/policy/samples/index.md). Você pode configurar um máximo de cinco políticas e apenas uma instância de cada opção de políticas. Algumas políticas não requerem parâmetros adicionais. A SKU Standard é necessária para as políticas de auditoria. O Nome da Política é limitado a 50 caracteres.

Selecione **Salvar rascunho** antes de continuar.

## <a name="co-sell-with-microsoft"></a>Vendas conjuntas com a Microsoft

Fornecer informações sobre a guia de venda conjunta é totalmente opcional para publicar sua oferta. É necessário atingir o status Pronto para venda conjunta e Pronto para venda conjunta IP. As informações fornecidas serão usadas pelas equipes de vendas da Microsoft para saber mais sobre sua solução ao avaliar seu ajuste para as necessidades do cliente. Ele não está disponível diretamente aos clientes.

Para obter detalhes sobre essa guia, consulte [Opção de revenda no Partner Center](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa de [Provedor de soluções na nuvem (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/). Isso permite que os revendedores vendam sua oferta aos clientes e criem soluções agrupadas.

Selecione **Salvar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configure uma demonstração ("test drive") que permite que os clientes experimentem sua oferta antes de adquiri-la. Para criar um ambiente de demonstração que permita aos clientes experimentar sua oferta por um período fixo, consulte [Testar sua oferta no Marketplace comercial.](test-drive.md)

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** na guia [Configuração da oferta](#test-drive). Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

### <a name="test-drive-technical-configuration"></a>Configuração de técnicas de Test Drive

- **ID do aplicativo do Azure ad** (obrigatório): Insira sua ID do [aplicativo](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)de Azure Active Directory (AD)). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (como 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para permitir que o Test Drive seja implantado em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva (não é necessária para unidades de teste do Power BI).

* **ID da assinatura do Azure** (necessário para Azure Resource Manager e Aplicativos Lógicos) – insira a ID da assinatura para permitir acesso aos serviços de conta do Azure para cobrança e relatório de uso de recursos. Recomendamos que você considere [criar uma assinatura separada do Azure](../../billing/billing-create-subscription.md) para usar em test drives, caso ainda não tenha uma. Você pode encontrar sua ID da assinatura do Azure entrando no [portal do Azure](https://portal.azure.com/) e navegando até a guia **Assinaturas** no menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura (como "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **ID de locatário do Azure ad** (obrigatório) – Insira sua ID de [locatário](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)do Azure Active Directory (AD)). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades** e, em seguida, procure o número da **ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar a ID do locatário da sua organização usando uma URL de nome de domínio em:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com).
* **Nome do locatário do Azure AD** (necessário para o Dynamics 365) – insira seu nome do Azure AD (Active Directory). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário estará listado abaixo do seu nome de conta.
* **ID do aplicativo do Azure ad** (obrigatório) – Insira sua ID do [aplicativo](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD)). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Registros de aplicativo** e, em seguida, procure o número da **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory segredo do cliente do aplicativo** (obrigatório) – Insira o [segredo do cliente](../../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)do aplicativo do Azure AD). Para encontrar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **Registros de aplicativo** e, em seguida, selecione seu aplicativo de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente**, insira uma descrição, selecione **Nunca** em **Expira em** e, em seguida, escolha **Adicionar**. Certifique-se de copiar o valor antes de sair desta página.)

Selecione **Salvar rascunho** antes de continuar.

### <a name="marketplace-listing-optional"></a>Listagem do Marketplace (opcional)

Descreva a experiência de test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrição** (obrigatório) – descreva seu test drive, o que será demonstrado, os objetivos do usuário com os quais experimentar, os recursos a serem explorados e as informações relevantes para ajudar o usuário a determinar se sua oferta deve ou não ser adquirida. Até 3.000 caracteres de texto podem ser inseridos neste campo. 
* **Informações de acesso** (necessário para test drives de Aplicativos Lógicos e do Azure Resource Manager) – explique o que um cliente precisa saber para acessar e usar esse test drive. Faça um passo a passo para um cenário de uso de sua oferta e exatamente o que o cliente deve saber para acessar recursos durante o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.
* **Manual do Usuário** (obrigatório) – um passo a passo detalhado da sua experiência de test drive. O manual do usuário precisa abranger exatamente o que você deseja que os clientes ganhem com a experiência de fazer o test drive e deve servir como uma referência para quaisquer perguntas que eles possam ter. O arquivo precisa estar no formato PDF e ser nomeado (máximo de 255 caracteres) após o upload.
* **Vídeos: Adicionar vídeos** (opcional) – Os vídeos podem ser carregados no YouTube ou no Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir um passo a passo de informações para ajudá-los a entender melhor o test drive, incluindo como usar com êxito os recursos de sua oferta e entender cenários que destacam os benefícios desses recursos.
  * **Nome** (obrigatório)
  * **Endereço** (Apenas YouTube ou Vimeo; obrigatório)
  * **Miniatura** (o arquivo de imagem deve estar no formato PNG e 533 x 324 pixels).

Selecione **Salvar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

Depois de concluir todas as seções necessárias da oferta, selecione **Examinar e publicar** no canto superior direito do portal.

Exibir o status de conclusão de cada seção da oferta.
    - *Não iniciado* – significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* – significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para as seções e atualize-as.
    - *Concluído* – significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você possa enviar a oferta.

Se essa é sua primeira vez publicando essa oferta, forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de eventuais notas suplementares úteis para compreensão do seu aplicativo.

Selecione **Enviar** para enviar sua oferta de publicação. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para exame e aprovação.

Retorne ao Partner Center e selecione **Ativar** a oferta para publicar sua oferta para o público (ou, se uma oferta privada, para o público-alvo privado).

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa **Validação da Microsoft** no processo de publicação representa uma revisão abrangente de sua oferta e seus ativos técnicos associados (especialmente o modelo do Azure Resource Manager), os problemas normalmente são apresentados como links de solicitações de pull. Obter uma explicação de como exibir e responder a essas solicitações de pull, confira [Como lidar com comentários de revisão](./azure-apps-review-feedback.md).

Se encontrar erros em uma ou mais das etapas de publicação, você precisará corrigi-los e republicar sua oferta.

## <a name="next-steps"></a>Próximas etapas

* [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
