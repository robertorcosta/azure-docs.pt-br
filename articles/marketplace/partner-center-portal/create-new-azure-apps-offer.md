---
title: Criar uma oferta de aplicativo do Azure-Microsoft Commercial Marketplace
description: Conheça as etapas e considerações para criar uma nova oferta de aplicativo do Azure no portal do Marketplace comercial no Partner Center. Você pode listar ou vender sua oferta de aplicativo do Azure no Azure Marketplace ou por meio do programa CSP (provedor de soluções na nuvem).
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 22d6c37b59488633394d7f3ed5ca5b0c78371e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790680"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicativo do Azure

Este artigo explica as etapas e considerações para a criação de uma nova oferta de aplicativo do Azure no Marketplace comercial. Você deve estar familiarizado com esses conceitos antes de criar uma nova oferta de aplicativo do Azure. 

Antes de publicar uma nova oferta de aplicativo do Azure, [crie uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) e verifique se sua conta está inscrita no programa do Marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Projetar, criar e testar ofertas de aplicativos do Azure requer conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta. A equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/).
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/).
* Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conhecimento de trabalho do [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Examine os seguintes recursos ao preparar sua oferta de aplicativo do Azure para o Marketplace comercial.

* [Compreender os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* Inícios Rápidos:

    * [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modelos de Início Rápido do Azure GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publicar definição do aplicativo](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Implantar aplicativo do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriais:

    * [Criar arquivos de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicar aplicativo do marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Exemplos:

    * [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [PowerShell do Azure](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluções de aplicativo gerenciado](https://docs.microsoft.com/azure/managed-applications/sample-projects)

Os modelos de solução de criação de vídeo [e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) fornecem uma introdução abrangente ao tipo de oferta de aplicativo do Azure:

* Quais tipos de oferta estão disponíveis;
* Quais ativos técnicos são necessários;
* Como criar um modelo de Azure Resource Manager;
* Desenvolvendo e testando a interface do usuário do aplicativo;
* Como publicar a oferta de aplicativo;
* O processo de revisão do aplicativo.

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar seu aplicativo Azure:

* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as extensões a seguir:
    * Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Você pode examinar as ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/) . Além disso, se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicativo do Azure

Há dois tipos de planos de aplicativo do Azure: modelos de solução e aplicativos gerenciados.

* O **modelo de solução** é uma das principais maneiras de publicar uma solução no Marketplace. Use este tipo de plano quando sua solução exigir implantação adicional e automação de configuração além de uma única máquina virtual (VM). Com um modelo de solução, você pode automatizar o fornecimento de mais de um recurso, incluindo VMs, redes e recursos de armazenamento para fornecer soluções complexas de IaaS.  Para obter mais informações sobre como criar modelos de solução, consulte [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* O **aplicativo gerenciado** é semelhante aos modelos de solução, com uma diferença importante. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução. Use aplicativos gerenciados para criar e entregar facilmente aplicativos completos e totalmente gerenciados aos seus clientes.  Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, confira a [Visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="technical-requirements"></a>Requisitos técnicos

Todos os aplicativos do Azure incluem pelo menos dois arquivos na pasta raiz de `.zip` um arquivo morto:

* Um arquivo de modelo do Resource Manager chamado [MainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este modelo define os recursos a serem implantados na assinatura do Azure do cliente.  Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) ou o repositório de [modelos do github: Azure Resource Manager QuickStart](https://github.com/azure/azure-quickstart-templates) correspondente.

* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure chamada [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Na interface do usuário, você especifica os elementos que permitem aos consumidores fornecer valores de parâmetro.

Todas as novas ofertas de aplicativos do Azure devem incluir um [GUID de atribuição de uso do cliente de parceiro do Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution). 

Para saber mais sobre os requisitos de publicação para cada plano de aplicativo, veja requisitos de [publicação de oferta de modelo de solução](../marketplace-solution-templates.md) e requisitos de publicação de oferta de [aplicativo gerenciado](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Criar uma oferta

>[!NOTE]
>Depois que uma oferta for publicada, as edições feitas nela no Partner Center não aparecerão em vitrines até que você Republique a oferta. Certifique-se de sempre republicar sua oferta depois de fazer alterações.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu à esquerda, selecione > **visão geral**do **Marketplace comercial**.

1. Na página Visão geral, selecione **+ nova oferta** > **aplicativo Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-azure-app.png)

1. Na página **nova oferta** , insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

     * Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e os modelos de Azure Resource Manager, se aplicável.
     * Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
     * A ID da oferta não pode ser alterada depois que você seleciona **criar**.

1. Insira um **alias de oferta**. Este é o nome usado para a oferta no Partner Center.

     * Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
     * O alias da oferta não pode ser alterado depois que você seleciona **criar**.

1. Selecione **criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Instalação da oferta

A página **instalação da oferta** é onde você pode configurar um test drive e gerenciamento de leads para sua oferta. 

### <a name="test-drive"></a>Test drive

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção "tentar antes de comprar", resultando em uma maior conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **habilitar um test drive** . Para remover test drive de sua oferta, desmarque essa caixa de seleção. Configure o ambiente de test drive na seção de [configuração técnica do Test Drive](#test-drive-technical-configuration) mais adiante neste tópico.

Para obter informações adicionais, consulte [testar sua oferta no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive). Você também pode ler sobre [Test Drive práticas recomendadas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e baixar a [visão geral do test drives PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (verifique se o bloqueador de pop-ups está desativado)

>[!Note]
>Como todos os aplicativos do Azure são implementados usando um modelo de Azure Resource Manager, o único tipo de test drive disponível para uma Aplicativo Azure é uma [Test Drive baseada em Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

## <a name="lead-management"></a>Gerenciamento de clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [visão geral do gerenciamento de Lead](./commercial-marketplace-get-customer-leads.md).

Selecione **salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

A página de **Propriedades** é onde você define as categorias e os setores usados para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

Selecione pelo menos uma e até três categorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Não se esqueça de descrever como sua oferta dá suporte a essas categorias na descrição da oferta.

### <a name="legal"></a>Legal

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Listagem de ofertas

Esta página é onde você gerencia a cópia e as imagens da oferta do Marketplace comercial. 

### <a name="marketplace-details"></a>Detalhes do Marketplace

> [!NOTE]
> A oferta de conteúdo de listagem (como descrição, documentos, capturas de tela e termos de uso) não precisa estar em inglês, desde que a descrição da oferta comece com a frase ", este aplicativo está disponível somente em [idioma diferente do inglês]". Também é aceitável fornecer uma URL de *Link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

#### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto inserido para o **alias de oferta** quando você criou a oferta, mas pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada nos resultados da pesquisa.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). Essa descrição pode ser usada nos resultados da pesquisa.

#### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta (até 3.000 caracteres). Essa descrição será exibida aos clientes na visão geral da listagem. Inclua a proposta de valor da sua oferta, os principais benefícios, a categoria e/ou as associações do setor, as oportunidades de compra no aplicativo e as divulgações necessárias.

SHere são algumas dicas para escrever sua descrição:

* Descreva claramente a proposição de valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
  * Descrição da sua oferta.
  * O tipo de usuário que se beneficia da sua oferta.
  * As necessidades do cliente ou os problemas que a oferta atendem.
* Lembre-se de que as primeiras frases podem ser exibidas nos resultados do mecanismo de pesquisa.
* Não confie em recursos e funcionalidades para vender sua oferta. Em vez disso, concentre-se no valor que sua oferta oferece.
* Use palavras específicas do setor ou de benefício.
* Considere o uso de marcas HTML para formatar sua descrição e torná-la mais atraente.

#### <a name="search-keywords"></a>Palavras-chave para pesquisa

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no Marketplace. Para obter melhores resultados, use também essas palavras-chave em sua descrição.

#### <a name="privacy-policy-link"></a>Link de política de privacidade

Insira a URL para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Links úteis

Adicione links a documentos opcionais online complementares sobre sua solução selecionando **+ Adicionar um link**.

### <a name="contact-information"></a>Informações de contato

Forneça o nome, o email e o número de telefone para um contato de **suporte**, **contato de engenharia**e **contato do programa CSP**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP. Alguns contatos podem exigir informações adicionais.

### <a name="marketplace-media"></a>Mídia do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

>[!Note]
>Se você tiver um problema ao carregar arquivos, certifique-se de que sua rede local não https://upload.xboxlive.com bloqueie o serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos PNG do logotipo da sua oferta nos seguintes três tamanhos de pixel:

- **Pequeno** (48 x 48)
- **Médio** (90 x 90)
- **Grande** (216 x 216)
- **Largo** (255 x 115)

Todos os três logotipos são necessários e são usados em locais diferentes na lista.

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela deve ter 1280 x 720 pixels de tamanho e no formato PNG. Cada captura de tela deve incluir uma legenda.

#### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstram sua oferta. Eles devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço Web e uma imagem PNG em miniatura do vídeo em 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selecione **salvar rascunho** antes de continuar.

## <a name="preview-audience"></a>Público de visualização

Na guia Visualização, escolha um público- **alvo** limitado para validar sua oferta antes de publicá-lo em tempo real para o público do Marketplace mais amplo.

> [!IMPORTANT]
> Depois de verificar sua oferta em versão prévia, selecione **entrar em tempo real** para publicar sua oferta no público público do Marketplace comercial.

Seu público-alvo de visualização é identificado por GUIDs de ID de assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. Você pode encontrar sua ID de assinatura do Azure na página **assinaturas** no portal do Azure.

Adicione pelo menos uma ID de assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você define quem pode visualizar sua oferta antes que ela seja publicada ao vivo. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público de visualização difere de um público privado. Um público de visualização pode acessar sua oferta _antes_ de ser publicado em tempo real nos Marketplaces. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público privado depois que sua oferta for totalmente publicada no Marketplace. Um público privado (definido na guia **preço e disponibilidade** do plano) tem acesso exclusivo a um plano específico.

Selecione **salvar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Conclua esta seção somente se sua oferta incluir um aplicativo gerenciado que emitirá eventos de medição usando a API do serviço de medição do Marketplace. Insira a **ID de locatário Azure Active Directory** e **Azure Active Directory ID do aplicativo** que seu serviço usará ao emitir eventos de medição.

Selecione **salvar rascunho** antes de continuar.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível de oferta)

>[!Note]
>Os detalhes técnicos de nível de oferta são opcionais.  Você só precisará configurar esses detalhes se estiver publicando um aplicativo gerenciado com cobrança limitada e tiver um serviço que será autenticado com um token de segurança do Azure AD. Para obter mais informações, consulte [estratégias de autenticação](./marketplace-metering-service-authentication.md) nas diferentes opções de autenticação.

A configuração técnica define os detalhes (ID do locatário e ID do aplicativo) usados para identificar seu serviço, o que emitirá eventos de medição para um aplicativo gerenciado usando as [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md).  Insira a identidade que seu serviço usará ao emitir eventos de medição.

* **ID de locatário do Azure ad** (obrigatório): dentro de portal do Azure, exigimos que você [crie um aplicativo de Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para localizar a [ID do locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), vá para o Azure Active Directory e selecione **Propriedades**e procure o número da **ID de diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).
* **ID do aplicativo do Azure ad** (obrigatório): você também precisa da [ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e de uma chave de autenticação. Para obter esses valores, vá para o Azure Active Directory e selecione **registros de aplicativo**, em seguida, procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para localizar a chave de autenticação, vá para **configurações** e selecione **chaves**. Você precisará fornecer uma descrição e uma duração e, em seguida, será fornecido um valor numérico.

>[!Note]
>A ID do aplicativo do Azure será associada à sua ID do editor e só poderá ser usada nesta conta do Publicador.

>[!Note]
>Essa configuração será necessária se você quiser usar o [evento de uso do lote](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event).  Caso queira enviar o evento de [uso](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event), você também pode usar o [serviço de metadados de instância](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para obter o token de [portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app).

## <a name="plan-overview"></a>Visão geral do plano

Esta guia permite que você forneça opções de plano diferentes na mesma oferta. Esses planos (chamados de SKUs no Portal do Cloud Partner) podem diferir em termos de tipo de plano (modelo de solução versus aplicativo gerenciado), monetização ou público.  Configure pelo menos um plano para listar sua oferta no Marketplace.

Depois de criado, você verá seus nomes de plano, IDs, tipo de plano, disponibilidade (pública ou privada), status de publicação atual e quaisquer ações disponíveis nessa guia.

As **ações** disponíveis na **visão geral do plano** variam de acordo com o status atual do seu plano e podem incluir:

* Se o status do plano for **rascunho** – excluir rascunho.
* Se o status do plano for **Live** – parar vender plano ou sincronizar público privado.

### <a name="create-new-plan"></a>Criar novo plano

***ID do plano*** – crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes na URL do produto.  Use apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. São permitidos no máximo 50 caracteres para essa ID de plano. Essa ID não pode ser modificada após a seleção de Create.

***Nome do plano*** -os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome de oferta exclusivo para cada plano nesta oferta. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

Esta guia permite que você defina a configuração de alto nível para o tipo de plano, se ele reutiliza pacotes de outro plano e em quais nuvens o plano deve estar disponível. Suas respostas nessa guia afetarão quais campos são exibidos em outras guias para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano
Selecione o tipo de plano para sua oferta. Um plano de **modelo de solução** é totalmente gerenciado pelo cliente. Um plano de **aplicativo gerenciado** permite que os editores gerenciem o aplicativo em nome do cliente. Para obter detalhes, consulte [tipos de planos de aplicativo do Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Reutilizar configuração técnica

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **este plano reutiliza pacotes de outro plano**.  Ao selecionar essa opção, você poderá selecionar um dos outros planos do mesmo tipo para esta oferta para reutilizar pacotes do. 

>[!Note]
>Quando você reutiliza pacotes de outro plano, a guia de configuração técnica inteira desaparecerá deste plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações feitas no futuro, também serão usados para esse plano.<br><br>Essa configuração não pode ser alterada depois que este plano é publicado.

#### <a name="azure-regions-cloud"></a>Regiões do Azure (nuvem)

Seu plano deve ser disponibilizado em pelo menos uma região do Azure.

Selecione a opção **global do Azure** para disponibilizar seu plano para clientes em todas as regiões globais do Azure que têm integração com o Marketplace comercial. Para obter detalhes, confira [disponibilidade geográfica e suporte a moeda](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecione a opção **Azure governamental** para disponibilizar seu plano na região do [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribal dos EUA, bem como parceiros qualificados para atendê-los. Você, como Publicador, é responsável por qualquer controle de conformidade, medidas de segurança e práticas recomendadas. O Azure governamental usa data centers isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar no [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), teste e valide seu plano no ambiente, pois determinados pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação de [Microsoft Azure governamental avaliação](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Depois que o plano for publicado e disponível em uma região específica do Azure, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure governamental

Essa opção só estará visível se você tiver selecionado **Azure governamental**.

Os serviços do Azure governamental lidam com os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para dar reconhecimento às suas certificações para esses programas, você pode fornecer até 100 links que os descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis somente para clientes do Azure governamental.

Selecione **salvar rascunho** antes de continuar.

### <a name="plan-listing"></a>Lista de planos

É aqui que você configura os detalhes da listagem do plano. Essa guia exibe informações específicas que podem diferir entre os planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isso é preenchido previamente com o nome que você atribuiu ao seu plano quando o criou. Esse nome aparece no Marketplace como o título deste plano e é limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo é limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo, bem como as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **salvar rascunho** antes de continuar.

### <a name="availability-solution-template-plans-only"></a>Disponibilidade (somente planos de modelo de solução)

Você pode tornar o plano visível para todos, somente para clientes específicos (um público privado) e se deseja tornar o plano oculto para uso somente por outros modelos de solução ou aplicativos gerenciados.

#### <a name="plan-visibility"></a>Visibilidade do plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público específico de sua escolha. Você pode atribuir associação a esse público restrito usando as IDs de assinatura do Azure.

Selecione **este é um plano privado** para tornar seu plano privado e visível somente para o público restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos; Ele não pode ser reconfigurado como um plano privado.

Se você tornar a planta privada, insira uma **ID de assinatura do Azure** e sua descrição. Cada um é um público que terá acesso a esse plano privado. O acesso é atribuído usando as IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuída. Adicione até 10 clientes IDs de assinatura individualmente ou 20.000 importando um arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e as letras devem estar em minúsculas.

>[!Note]
>Um público privado ou restrito é diferente do público de visualização que você definiu na guia **Visualização** . Um público de visualização pode acessar sua oferta _antes_ de sua publicação em tempo real no Marketplace. Embora a escolha de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Ocultar plano

Se o seu modelo de solução pretende ser implantado apenas indiretamente quando for referenciado em outro modelo de solução ou aplicativo gerenciado, marque esta caixa para publicar o modelo de solução, mas ocultá-lo de clientes pesquisando e navegando diretamente.

Selecione **salvar rascunho** antes de continuar.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Preços e disponibilidade (somente planos de aplicativo gerenciados)

Use-o para configurar os **mercados** em que esse plano estará disponível, o **preço** por mês do gerenciamento da solução e a visibilidade do **plano** se apenas clientes específicos devem vê-lo (um público privado).

Selecione **salvar rascunho** antes de continuar.

#### <a name="markets"></a>Mercados

Cada plano deve estar disponível em pelo menos um mercado. Marque a caixa de seleção de qualquer local de mercado onde você gostaria de disponibilizar esse plano. Uma caixa de pesquisa e um botão para selecionar os países "impostos remetidos", no qual a Microsoft remete as vendas e o imposto sobre o uso em seu nome, estão incluídas para ajudar.

Se você já tiver definido preços para seu plano em dólares de Estados Unidos (USD) e adicionar outro local de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Os preços podem ser revisados usando o link "exportar preços (xlsx)" depois de salvar as alterações.

#### <a name="pricing"></a>Preços

Forneça o preço por mês para este plano.  Esse preço é além de qualquer infraestrutura do Azure ou custos de software pago conforme o uso incorridos pelos recursos implantados por essa solução.

Além do preço por mês, você também pode definir preços para consumo de unidades não padrão usando [cobrança limitada](./azure-app-metered-billing.md).  Você pode definir o preço por mês como zero e cobrar exclusivamente usando a cobrança limitada, se desejar. 

Os preços definidos em USD (USD = Estados Unidos dólar) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. 

>[!Note]
>Você deve primeiro salvar as alterações de preços para habilitar a exportação de dados de preços.

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado.  

>[!Note]
>Depois que o preço de um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

#### <a name="plan-visibility"></a>Visibilidade do plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público específico de sua escolha. Você pode atribuir associação a esse público restrito usando as IDs de assinatura do Azure.

Selecione **este é um plano privado** para tornar seu plano privado e visível somente para o público restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos; Ele não pode ser reconfigurado como um plano privado.

Se você tornar a planta privada, insira uma **ID de assinatura do Azure** e sua descrição. Cada um é um público que terá acesso a esse plano privado. O acesso é atribuído usando as IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuída. Adicione até 10 clientes IDs de assinatura individualmente ou 20.000 importando um arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e as letras devem estar em minúsculas.

>[!Note]
>Um público privado ou restrito é diferente do público de visualização que você definiu na guia **Visualização** . Um público de visualização pode acessar sua oferta _antes_ de sua publicação em tempo real no Marketplace. Embora a escolha de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privados ou não) para fins de validação.

### <a name="technical-configuration"></a>Configuração técnica 

Esta guia permite que você carregue o pacote de implantação que permitirá que os clientes implantem seu plano.

>[!Note]
>Essa guia não estará visível se você tiver configurado este plano para reutilizar pacotes de outro plano na guia **configuração do plano** .

#### <a name="package-details"></a>Detalhes do pacote

Esta guia permite que você edite a versão de rascunho de sua configuração técnica.

**Versão** – atribua a versão atual da configuração técnica.  Aumente essa versão sempre que publicar uma alteração nesta página. A versão deve estar no formato `{integer}.{integer}.{integer}`.

**Arquivo de pacote** (. zip) – Este pacote contém todos os arquivos de modelo necessários para esse plano, bem como quaisquer recursos adicionais, empacotados como um `.zip` arquivo.

Todos os pacotes de plano de aplicativo do Azure devem incluir esses dois arquivos na pasta `.zip` raiz de um arquivo morto:

* Um arquivo de modelo do Resource Manager chamado [MainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Este modelo automatiza a implantação de recursos na assinatura do Azure dos clientes.  Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou o repositório de [modelos do github: Azure Resource Manager QuickStart](https://github.com/azure/azure-quickstart-templates) correspondente.
* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure chamada [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Os tamanhos máximos de arquivo com suporte são:

* Até 1 GB no tamanho total do `.zip` arquivo compactado
* Até 1 GB para qualquer arquivo não compactado individual dentro do `.zip` arquivo morto  

Todas as novas ofertas de aplicativos do Azure também devem incluir um GUID de [atribuição de uso do cliente de parceiro do Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

Os planos de aplicativos gerenciados exigem informações adicionais nesta guia.

#### <a name="previously-published-packages"></a>Pacotes publicados anteriormente

A subguia **pacotes publicados anteriormente** permite que você exiba todas as versões publicadas de sua configuração técnica.

#### <a name="enable-just-in-time-jit-access"></a>Habilitar o acesso JIT (just-in-time)

Selecione esta opção para habilitar o acesso JIT (just-in-time) para este plano.  O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso.  Para obter mais informações, consulte [habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Para exigir que os consumidores do seu aplicativo gerenciado conceda acesso permanente à sua conta, deixe essa opção desmarcada.

>[!Note]
>Certifique-se de atualizar `createUiDefinition.json` seu arquivo para dar suporte a esse recurso.  

#### <a name="deployment-mode"></a>Modo de implantação

Selecione se deseja configurar o **modo de implantação** **completo** ou incremental ao implantar este plano: 

* No **modo completo**, uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no grupo de recursos gerenciados se os recursos não estiverem definidos no `mainTemplate.json`. 
* No **modo incremental**, uma reimplantação do aplicativo deixa os recursos existentes inalterados.

Para saber mais sobre modos de implantação, consulte [Azure Resource Manager modos de implantação](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

#### <a name="notification-endpoint-url"></a>URL do ponto de extremidade de notificação

Especifique um ponto de extremidade de webhook HTTPS para receber notificações sobre todas as operações CRUD em instâncias de aplicativo gerenciado desta versão do plano.

#### <a name="customize-allowed-customer-actions"></a>Personalizar ações de clientes permitidas

Selecione esta opção para especificar quais ações os clientes podem executar nos recursos gerenciados, além das ações`*/read`"" que estão disponíveis por padrão. 

Liste as ações adicionais que você gostaria de permitir que o cliente execute aqui, separado por ponto-e-vírgula.  Para obter mais informações, consulte [noções básicas sobre atribuições de negação para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Para as ações disponíveis, confira [Operações do provedor de recursos do Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Por exemplo, para permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

#### <a name="global-azure--azure-government-cloud"></a>Nuvem global do Azure/Azure governamental

Indique quem deve ter acesso de gerenciamento a esse aplicativo gerenciado em cada nuvem com suporte. Usuários, grupos ou aplicativos para os quais você deseja receber permissão para o grupo de recursos gerenciados são identificados usando as identidades do AAD (Azure Active Directory).

**Azure Active Directory ID de locatário** – a ID de locatário do AAD (também conhecida como ID de diretório) que contém as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões. Você pode encontrar sua ID de locatário do AAD na portal do Azure, em [Propriedades para Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizações** – adicione a ID de objeto Azure Active Directory do usuário, grupo ou aplicativo para o qual você deseja receber permissão para o grupo de recursos gerenciado. Identifique o usuário por sua ID de entidade de segurança, que pode ser encontrada na [folha Azure Active Directory usuários no portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada entidade de segurança, selecione uma das funções internas do Azure AD na lista (proprietário ou colaborador). A função selecionada descreverá as permissões que a entidade de segurança terá sobre os recursos na assinatura do cliente. Para obter mais informações, consulte [Funções internas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Para obter mais informações sobre o RBAC (controle de acesso baseado em função), consulte Introdução [ao RBAC no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Embora você possa adicionar até 100 autorizações por nuvem, geralmente é mais fácil criar um grupo de usuários Active Directory e especificar sua ID na "ID da entidade de segurança". Isso permitirá que você adicione mais usuários ao grupo de gerenciamento depois que o plano for implantado e reduza a necessidade de atualizar o plano apenas para adicionar mais autorizações.

#### <a name="policy-settings"></a>Configurações de política

Aplique [políticas do Azure](https://docs.microsoft.com/azure/governance/policy/overview) ao seu aplicativo gerenciado para especificar os requisitos de conformidade para a solução implantada. Para definições de política e o formato dos valores de parâmetro, veja [Exemplos de Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index). Você pode configurar um máximo de cinco políticas e apenas uma instância de cada opção de políticas. Algumas políticas exigem parâmetros adicionais. A SKU Standard é necessária para as políticas de auditoria. O nome da política está limitado a 50 caracteres.

Selecione **salvar rascunho** antes de continuar.

## <a name="co-sell-with-microsoft"></a>Vendas conjuntas com a Microsoft

Fornecer informações sobre a guia de venda é totalmente opcional para publicar sua oferta. É necessário atingir o status pronto para venda de covenda pronta e de IP. As informações fornecidas serão usadas pelas equipes de vendas da Microsoft para saber mais sobre sua solução ao avaliar seu ajuste para as necessidades do cliente. Ele não está disponível diretamente aos clientes.

Para obter detalhes sobre essa guia, consulte [a opção de revenda no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa CSP ( [provedores de soluções de nuvem](https://azure.microsoft.com/offers/ms-azr-0145p/) ). Isso permite que os revendedores vendam sua oferta aos clientes e criem soluções agrupadas.

Selecione **salvar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configure uma demonstração (test drive) que permite aos clientes experimentar sua oferta antes de adquiri-la. Para criar um ambiente de demonstração que permita aos clientes experimentar sua oferta por um período de tempo fixo, consulte [testar sua oferta no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para habilitar uma test drive, marque a caixa de seleção **habilitar um test drive** na guia [configuração da oferta](#test-drive) . Para remover test drive de sua oferta, desmarque essa caixa de seleção.

### <a name="test-drive-technical-configuration"></a>Testar a configuração técnica

- **ID do aplicativo do Azure ad** (obrigatório): Insira sua ID do [aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)de Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Detalhes da assinatura da implantação

Para permitir que o Test Drive seja implantado em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva (não é necessária para Power BI unidades de teste).

* **ID da assinatura do Azure** (necessária para Azure Resource Manager e aplicativos lógicos) – Insira a ID da assinatura para conceder acesso aos serviços de conta do Azure para relatório e cobrança de uso de recursos. Recomendamos que você considere a [criação de uma assinatura do Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) a ser usada para unidades de teste, se você ainda não tiver uma. Você pode encontrar sua ID de assinatura do Azure fazendo logon no [portal do Azure](https://portal.azure.com/) e navegando até a guia **assinaturas** do menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **ID de locatário do Azure ad** (obrigatório) – Insira sua ID de [locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)do Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades**e procure o número de **ID de diretório** listado (como 50c464d3-4930-494c-963c-1e951d15360e). Você também pode pesquisar a ID de locatário da sua organização usando sua URL de nome de [https://www.whatismytenantid.com](https://www.whatismytenantid.com)domínio em:.
* **Nome do locatário do Azure ad** (necessário para o Dynamic 365) – Insira seu nome de Azure Active Directory (AD). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário será listado em seu nome de conta.
* **ID do aplicativo do Azure ad** (obrigatório) – Insira sua ID do [aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu do nav esquerdo, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (como 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory segredo do cliente do aplicativo** (obrigatório) – Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)de aplicativo do Azure AD. Para localizar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu do nav esquerdo, selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo Test Drive. Em seguida, **Selecione certificados e segredos**, selecione **novo segredo do cliente**, insira uma descrição, selecione **nunca** em **expirar**e, em seguida, escolha **Adicionar**. Certifique-se de copiar o valor antes de sair desta página.)

Selecione **salvar rascunho** antes de continuar.

### <a name="marketplace-listing-optional"></a>Listagem do Marketplace (opcional)

Descreva a experiência de test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrição** (obrigatório) – descreva sua Test Drive, o que será demonstrado, os objetivos para o usuário experimentar, os recursos a serem explorados e todas as informações relevantes para ajudar o usuário a determinar se deseja adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 
* **Informações de acesso** (necessárias para unidades de teste de Azure Resource Manager e lógica) – explique o que um cliente precisa saber para acessar e usar esse Test Drive. Percorra um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos em todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.
* **Manual do usuário** (obrigatório) – um passo a passo detalhado de sua experiência de Test Drive. O manual do usuário deve abranger exatamente o que você deseja que o cliente tenha de apresentar o test drive e servir como uma referência para quaisquer perguntas que possam ter. O arquivo deve estar no formato PDF e ter o nome (máximo de 255 caracteres) após o carregamento.
* **Vídeos: adicionar vídeos** (opcional) – vídeos podem ser carregados no YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir um passo a passo de informações para ajudá-los a entender melhor os Test Drive, incluindo como usar com êxito os recursos de sua oferta e entender os cenários que destacam seus benefícios.
  * **Nome** (obrigatório)
  * **Endereço** (somente YouTube ou Vimeo; obrigatório)
  * **Miniatura** (o arquivo de imagem deve estar no formato PNG e 533 x 324 px).

Selecione **salvar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

Quando você tiver concluído todas as seções necessárias da oferta, selecione **revisar e publicar** no canto superior direito do Portal.

Examine o status de conclusão de cada seção da oferta.
    - *Não iniciado* -significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* -significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para a (s) seção (ões) e atualize-a.
    - *Concluir* -significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.

Se esta for a primeira vez que você publica essa oferta, você pode fornecer instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.

Selecione **Enviar** para enviar sua oferta de publicação. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para revisão e aprovação.

Retorne ao Partner Center e selecione **Go-Live** para a oferta para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa de **validação manual** no processo de publicação representa uma análise extensiva de sua oferta e seus ativos técnicos associados (especialmente o modelo de Azure Resource Manager), os problemas normalmente são apresentados como links de solicitação de pull (PR). Obter uma explicação de como exibir e responder a essas solicitações de pull, confira [Como lidar com comentários de revisão](./azure-apps-review-feedback.md).

Se você tiver erros em uma ou mais das etapas de publicação, corrija-os antes de republicar sua oferta.

## <a name="next-steps"></a>Próximas etapas

* [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
