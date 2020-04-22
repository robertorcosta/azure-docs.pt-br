---
title: Crie uma nova oferta de Aplicativos Azure no mercado comercial
description: Como criar uma nova oferta de Aplicativos Azure para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) usando o portal Commercial Marketplace no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: a62551b31c51836dfa50534ee6db907c8a4e111a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730610"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicativo do Azure

As etapas para a publicação de uma oferta de aplicativo Azure no mercado comercial estão descritas aqui.

## <a name="azure-application-offer-type"></a>Tipo de oferta de aplicativo do Azure

Este tópico descreve fundamentos sobre as ofertas de aplicativos do Azure.  Você deve estar familiarizado com esses conceitos antes de iniciar o processo de publicação de uma nova oferta de aplicativo Azure no Marketplace.

### <a name="publishing-overview"></a>Visão geral da publicação

Os modelos de solução de construção de vídeo [e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) são uma introdução ao tipo de oferta de aplicativos do Azure:

* Quais tipos de oferta estão disponíveis;
* Quais ativos técnicos são necessários;
* Como criar um modelo de Gerenciador de Recursos do Azure;
* Desenvolvimento e teste do aplicativo UI;
* Como publicar a oferta do aplicativo;
* O processo de revisão do aplicativo.

### <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicativos do Azure

Existem dois tipos de planos de aplicativos Azure, aplicativos gerenciados e modelos de soluções.

* **O modelo** de solução é uma das principais formas de publicar uma solução no Marketplace. Esse tipo de plano é usado quando sua solução requer implantação e automação de configuração adicionais além de uma única máquina virtual (VM).  Com um modelo de solução, você pode automatizar o fornecimento de mais de um recurso, incluindo VMs, redes e recursos de armazenamento para fornecer soluções IaaS complexas.  Para obter mais informações sobre a construção de modelos de soluções, consulte a documentação do [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

* **O aplicativo gerenciado** é semelhante aos modelos de solução, com uma diferença fundamental. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução. Use aplicativos gerenciados para criar e fornecer aplicativos totalmente gerenciados e turnkey para seus clientes.  Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, confira a [Visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Todos os aplicativos do Azure incluem pelo menos `.zip` dois arquivos na pasta raiz de um arquivo:

* Um arquivo de modelo do Gerenciador de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este é o modelo que define os recursos a serem implantados na assinatura do Azure do cliente.  Para obter exemplos de modelos do Gerenciador de recursos, consulte a [galeria Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/) ou o [relo de modelos quickstart do Gerenciador de recursos do Azure.](https://github.com/azure/azure-quickstart-templates)

* Uma definição de interface de usuário para a experiência de criação de aplicativos do Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Na interface do usuário, você especifica os elementos que permitem aos consumidores fornecer valores de parâmetro.

Todas as novas ofertas de aplicativos do Azure devem incluir uma atribuição de [uso do cliente parceiro Do Azure GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Antes de começar

Examine a seguinte documentação do aplicativo do Azure, que fornece guias de início rápido, tutoriais e exemplos.

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

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

Projetar, criar e testar esses recursos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.

A equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

* Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/).
* Como [projetar e arquitetar aplicações do Azure](https://azure.microsoft.com/solutions/architecture/).
* Conhecimento de funcionamento das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [Rede Azure.](https://azure.microsoft.com/services/?filter=networking#networking)
* Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conhecimento de trabalho da [JSON](https://www.json.org/).

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

Você pode rever as ferramentas disponíveis na página [Ferramentas do Desenvolvedor do Azure.](https://azure.microsoft.com/tools/)  Também se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicativo do Azure

Antes de criar uma oferta de aplicativo do Azure, você deve primeiro [criar uma conta do Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) e abrir o painel do mercado [comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a guia **Visão geral** selecionada.

>[!Note]
>Uma vez publicada uma oferta, as edições da oferta feita no Partner Center só serão atualizadas no sistema e nas vitrines após a republicação.  Certifique-se de enviar a oferta para publicação após as alterações feitas.

### <a name="create-a-new-offer"></a>Criar uma oferta

Selecione o botão **+ Novo oferta** e selecione o item do menu do Aplicativo **Azure.** A caixa de diálogo **Nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>Oferecer ID e alias

* **ID de oferta**: Um identificador exclusivo para cada oferta em sua conta. Este ID será visível para os clientes no endereço URL para a oferta de marketplace e modelos do Azure Resource Manager (se aplicável). <br> <br> Seu ID de oferta deve ser caracteres alfanuméricos minúsculos (incluindo hífens e sublinhados, mas sem espaço em branco). Ele é limitado a 50 caracteres e não pode ser alterado depois de selecionar Criar. <br> <br> Por exemplo, se `test-offer-1` você entrar aqui, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o URL da oferta será . 

* **Ofereça alias**: O nome usado para se referir à oferta dentro do Partner Center. Este nome não será usado no mercado e é diferente do nome da oferta e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **Criar**.

Uma vez que você tenha inserido o alias **de Oferta** e **Oferta,** selecione **Criar**. Você poderá trabalhar em todas as outras partes da sua oferta.

## <a name="offer-setup"></a>Configuração da oferta

A página **de configuração Oferta** pede as seguintes informações. Certifique-se de selecionar **Salvar** depois de concluir esses campos.

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando em maior conversão e geração de leads altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar um test drive, verifique **a Caixa Ativar um test drive.** Em seguida, você precisará configurar um ambiente de demonstração na [configuração técnica do test drive](#types-of-azure-application-plans) configurar para permitir que os clientes experimentem sua oferta por um período fixo de tempo. 

>[!Note]
>Como todos os aplicativos do Azure são implementados usando um modelo do Azure Resource Manager, o único tipo de test drive que pode ser configurado para um aplicativo Do Azure é um [test drive baseado no Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Recursos adicionais de test drive

- [Práticas recomendadas técnicas do test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing de test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral da unidade de teste um pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [visão geral do gerenciamento de líderes](./commercial-marketplace-get-customer-leads.md).

Lembre-se **de salvar** antes de passar para a próxima seção!

## <a name="properties"></a>Propriedades

A página **Propriedades** permite definir as categorias e indústrias usadas para agrupar sua oferta no mercado, sua versão do aplicativo e os contratos legais que suportam sua oferta. Selecione **Salvar** depois de concluir esta página.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e no máximo três categorias, que serão usadas para colocar sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de chamar como sua oferta suporta essas categorias na descrição da oferta. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Listagem de ofertas

A página de listagem de ofertaexibe os idiomas nos quais sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Você precisará definir detalhes do mercado (nome de oferta, descrição, imagens, etc.) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> O conteúdo de listagem de ofertas (como a descrição, documentos, capturas de tela, termos de uso, etc.) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]." Também é aceitável fornecer uma *URL de Link útil* para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem de Oferta.

### <a name="name"></a>Nome

O nome que você inserir aqui será mostrado aos clientes como o título da sua lista de ofertas. Este campo está prepovoado com o texto que você inseriu para **oferecer alias** quando você criou a oferta, mas você pode alterar esse valor. Este nome pode ser registrado (e você pode incluir símbolos de marca ou direitos autorais). O nome não pode ser mais de 50 caracteres e não pode incluir nenhum emoji.

### <a name="summary"></a>Resumo

Forneça uma breve descrição de sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa de sua oferta (até 256 caracteres). A descrição pode ser usada em resultados de pesquisa de marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa de sua oferta (até 3.000 caracteres). Esta descrição será exibida aos clientes na visão geral da listagem do marketplace. Inclua a proposta de valor da sua oferta, os principais benefícios, as associações de categorias e/ou do setor, as oportunidades de compra no aplicativo e quaisquer divulgações necessárias. 

Algumas dicas para escrever sua descrição:  

- Descreva claramente a proposição de valor da sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens em sua proposta de valor:
  - Descrição do produto
  - O tipo de usuário que se beneficia do produto
  - Necessidades do cliente ou dor que o produto aborda
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados da pesquisa.  
- Não dependa de recursos e em funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você oferece.  
- Use o vocabulário específico do setor ou palavras com base no benefício tanto quanto possível. 
- Considere usar tags HTML para formatar sua descrição e torná-la mais atraente.

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Você pode, opcionalmente, inserir até três palavras-chave de pesquisa para ajudar os clientes a encontrar sua oferta no mercado. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="support-urls"></a>Suporte a URLs

Esta seção permite que você forneça links para ajudar os clientes a entender mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Digite a URL na política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

#### <a name="useful-links"></a>Links úteis

Forneça documentos on-line suplementares opcionais sobre sua solução.  Adicione links úteis adicionais clicando **+ Adicione um link**.

### <a name="contacts"></a>Contatos

Nesta seção, você deve fornecer o nome, e-mail e número de telefone para um **contato de suporte** e um contato de **Engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas a parceiros CSP.

Na seção **de contato de suporte,** você também deve fornecer a **URL de suporte** onde os parceiros CSP podem encontrar suporte para sua oferta.

### <a name="marketplace-images"></a>Imagens de mercado

Nesta seção, você pode fornecer logotipos e imagens que serão usadas ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato .png.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça o logotipo da sua oferta em três tamanhos: **Pequeno (48 x 48)**, **Médio (90 X 90)** e **Grande (216 x 216)**.

#### <a name="hero"></a>Hero

A imagem de herói é opcional. Se você fornecer um, ele deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de tela

Adicione capturas de tela que mostram como sua oferta funciona. Você pode adicionar até cinco capturas de tela. Todas as capturas de tela devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Você pode, opcionalmente, adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou Vimeo. Para cada um, digite o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

- [Melhores práticas para listagens de ofertas de marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Visualização

A guia **Visualização** permite definir um **Público de Pré-Visualização** limitado para validar sua oferta antes de publicar sua oferta ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Você deve selecionar **Ir ao vivo** antes que sua oferta seja publicada ao vivo para o público do mercado depois de verificar sua oferta no Preview.

Seu público de visualização é identificado por GUIDs de assinatura do Azure, emparelhados com uma descrição opcional para cada um.  Nenhum desses campos é visível para os clientes.

Adicione até 10 IDs de assinatura do Azure manualmente, ou até 100 se carregar um arquivo CSV.  Ao adicionar essas assinaturas, você está definindo um público que terá acesso de pré-visualização à sua oferta antes de ser totalmente publicada.  Se sua oferta já estiver ao vivo, você ainda pode definir uma audiência de pré-visualização para testar quaisquer alterações ou atualizações à sua oferta.

>[!Note]
>O público de pré-visualização difere de uma audiência privada. Uma audiência de pré-visualização pode ter acesso à sua oferta *antes* de ser publicada ao vivo nos marketplaces. Você também pode optar por criar um plano e disponibilizá-lo apenas para um público privado (usando a guia Disponibilidade do plano).  Seu público de pré-visualização poderá ver e validar todos os planos, incluindo planos que estarão disponíveis apenas para um público privado assim que sua oferta for totalmente publicada no mercado.

## <a name="plan-overview"></a>Visão geral do plano

A guia **Visão geral do Plano** permite que você forneça diferentes opções de plano dentro da mesma oferta. Esses planos (chamados de SKUs no Portal de Parceiros em Nuvem) podem diferir em termos de tipo de plano (modelo de solução versus aplicativo gerenciado), monetização ou audiência.  Configure pelo menos um plano para listar sua oferta no mercado.

Uma vez criado, você verá seus nomes de plano, IDs, tipo de plano, disponibilidade (Público ou Privado), status de publicação atual e quaisquer ações disponíveis nesta guia.

**As ações** disponíveis na **visão geral** do Plano variam dependendo do status atual do seu plano e podem incluir:

* Se o status do plano **for Rascunho** – Exclua o rascunho.
* Se o status do plano for **Ao Vivo** – Pare de vender plano ou Sincronia audiência privada.

### <a name="create-new-plan"></a>Criar um novo plano

***ID do plano*** - Crie um iD de plano único para cada plano nesta oferta. Este ID será visível para os clientes na URL do produto.  Use apenas caracteres minúsculos e alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este id plano. Este ID não pode ser modificado após a seleção de criar.

***Nome do plano*** - Os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo. Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

A guia **De configuração plano** permite definir a configuração de alto nível para o tipo de plano, se ele reutiliza pacotes de outro plano e em quais nuvens o plano deve estar disponível.  Suas respostas nesta guia afetarão quais campos são exibidos em outras guias para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano

Como descrito nos [planos de aplicativos do Azure,](#types-of-azure-application-plans)selecione se seu plano conterá um modelo de solução ou um aplicativo gerenciado.

#### <a name="this-plan-reuses-packages"></a>Este plano reutiliza pacotes

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **este plano reutiliza pacotes de outro plano**.  Ao selecionar essa opção, você poderá selecionar um dos outros planos do mesmo tipo para que esta oferta reutilize pacotes. 

>[!Note]
>Quando você reutilizar pacotes de outro plano, toda a guia de configuração técnica desaparecerá deste plano.  Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que você fizer no futuro, também serão usados para este plano. <br> <br> Além disso, essa configuração não pode ser alterada uma vez que este plano seja publicado.

#### <a name="cloud-availability"></a>Disponibilidade de nuvem

Este plano deve ser disponibilizado em pelo menos uma nuvem. 

Selecione a opção **Public Azure** para tornar sua solução implantável aos clientes em todas as regiões públicas do Azure que tenham integração com o Marketplace.  Saiba mais sobre [disponibilidade geográfica.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção **Azure Government Cloud** para tornar sua solução implantável na [Nuvem do Governo do Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), uma nuvem da comunidade governamental com acesso controlado para clientes dos EUA Federal, Estado, local ou tribal e parceiros elegíveis para atender a essas entidades.  Você, como editor, é responsável por quaisquer controles de conformidade, medidas de segurança e práticas recomendadas para servir a essa comunidade de nuvem.  O governo do Azure usa data centers e redes fisicamente isolados (localizados apenas nos EUA).  Antes de publicar para o [Governo Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)a Microsoft recomenda que você teste e valide sua solução no ambiente, pois certos pontos finais podem ser diferentes. Para encenar e testar sua solução, solicite uma conta de teste a partir deste [link](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Uma vez que um plano é publicado como disponível em uma nuvem específica, essa nuvem não pode ser removida.

**Certificações de Nuvem do Governo do Azure**

Essa opção só será visível se **o Azure Government Cloud** for selecionado sob disponibilidade na **Nuvem**.

Os serviços do Governo Azure lidam com dados que estão sujeitos a certas regulamentações e requisitos governamentais, como FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.  Para trazer consciência às suas certificações para esses programas, você pode fornecer até 100 links descrevendo suas certificações.  Esses links podem ser links para sua listagem diretamente no programa, ou um link para descrições de sua conformidade com eles em seus próprios sites.  Esses links serão visíveis apenas para os clientes do Azure Government Cloud.

## <a name="plan-listing"></a>Listagem de planos

A guia **de listagem do plano** exibe as informações de listagem específicas do plano que podem ser diferentes entre diferentes planos para a mesma oferta.

### <a name="name"></a>Nome

Pré-preenchido com base no seu nome que você atribuiu seu plano quando o criou.  Este nome aparecerá como o título deste "Plano de Software" exibido no mercado.  Pode conter até 100 caracteres.

### <a name="summary"></a>Resumo

Forneça um breve resumo do seu plano de software.  Pode conter até 100 caracteres.

### <a name="description"></a>Descrição

Esta descrição é uma oportunidade para explicar o que torna este plano de software único e quaisquer diferenças de outros planos de software dentro de sua oferta. Pode conter até 2.000 caracteres.

Selecione **Salvar** depois de concluir esses campos.

## <a name="availability"></a>Disponibilidade

A guia **Disponibilidade** é visível apenas para planos de modelo de solução.  Você pode tornar o plano visível para todos, apenas para clientes específicos (um público privado), e se tornar o plano oculto para uso por outro modelo de solução ou apenas aplicativos gerenciados.

### <a name="plan-audience"></a>Audiência do Plano

Você tem a opção de configurar cada plano para ser visível para todos ou para apenas um público específico de sua escolha. Você pode atribuir a adesão a este público restrito usando IDs de assinatura do Azure.

**Privacidade / Este é um plano privado** (caixa de seleção opcional) - Marque esta caixa para tornar seu plano privado e visível apenas para o público restrito de sua escolha. Uma vez publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Uma vez que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

**Audiência Restrita (IDs de assinatura do Azure)** - Atribuir ao público que terá acesso a este plano privado. O acesso é atribuído usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuído. Um máximo de 10 IDs de assinatura podem ser adicionados, ou 20.000 iDs de assinatura de clientes se importar um arquivo de planilha .csv.  Os IDs de assinatura do Azure são representados como GUIDs, e as letras devem ser reduzidas.

>[!Note]
>O público privado (ou audiência restrita) difere do público de pré-visualização que você definiu na guia [**Visualização.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Um público de pré-visualização pode ter acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação de público privado só se aplique a um plano específico, o público de pré-visualização pode visualizar todos os planos (privados ou não) para fins de validação.

### <a name="hide-plan"></a>Plano de ocultação

Se o modelo da solução se destina a ser implantado apenas indiretamente quando referenciado por outro modelo de solução ou aplicativo gerenciado, verifique esta caixa para publicar seu modelo de solução, mas esconda-o dos clientes que procuram e navegam diretamente por ele.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

A guia **Preços e disponibilidade** é visível apenas para planos de aplicativos gerenciados.  Você pode configurar os mercados em que este plano estará disponível, o preço por mês da gestão da solução, e se tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de seleção para qualquer local de mercado onde você gostaria de disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países "Remetidos de impostos", nos quais a Microsoft reemite vendas e uso de impostos em seu nome, são incluídos para ajudar.

Se você já definiu os preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço para o novo mercado será calculado de acordo com as taxas de câmbio atuais. Sempre revise o preço de cada mercado antes de publicar. Os preços podem ser revisados usando o link "Preços de exportação (xlsx)" após salvar suas alterações.

### <a name="pricing"></a>Preços

Forneça o preço mensal para este plano.  Este preço é além de qualquer infra-estrutura do Azure ou custos de software pay-as-you-go incorridos pelos recursos implantados por esta solução.

Os preços definidos em moeda local (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais disponíveis durante a configuração. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. 

>[!Note]
>Primeiro, você deve salvar suas alterações de preços para permitir a exportação de dados de preços.

Revise seus preços cuidadosamente antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano.  

>[!Note]
>Uma vez que um preço para um mercado em seu plano é publicado, ele não pode ser alterado mais tarde.

### <a name="plan-audience"></a>Audiência do Plano

Você tem a opção de configurar cada plano para ser visível para todos ou para apenas um público específico de sua escolha. Você pode atribuir a adesão a este público restrito usando IDs de assinatura do Azure.

**Privacidade / Este é um plano privado** (caixa de seleção opcional) - Marque esta caixa para tornar seu plano privado e visível apenas para o público restrito de sua escolha. Uma vez publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Uma vez que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

**Audiência Restrita (IDs de assinatura do Azure)** - Atribuir ao público que terá acesso a este plano privado. O acesso é atribuído usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuído. Um máximo de 10 IDs de assinatura podem ser adicionados, ou 20.000 iDs de assinatura de clientes se importar um arquivo de planilha .csv.  Os IDs de assinatura do Azure são representados como GUIDs, e as letras devem ser reduzidas.

>[!Note]
>O público privado (ou audiência restrita) difere do público de pré-visualização que você definiu na guia [**Visualização.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Um público de pré-visualização pode ter acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação de público privado só se aplique a um plano específico, o público de pré-visualização pode visualizar todos os planos (privados ou não) para fins de validação.

## <a name="technical-configuration"></a>Configuração técnica 

A guia **de configuração técnica** permite que você carregue o pacote de implantação que permitirá que os clientes implantem seu plano.

>[!Note]
>Esta guia não será visível se você configurou esse plano para reutilizar pacotes de outro plano na guia **De configuração Do Plano.**

### <a name="package-details"></a>Detalhes do pacote

A subguia **Detalhes do pacote** permite editar a versão de rascunho da sua configuração técnica.

***Versão*** - Atribuir a versão atual da configuração técnica.  Incremente esta versão cada vez que você publicar uma alteração nesta página. A versão deve `{integer}.{integer}.{integer}`estar no formato .

***Arquivo*** de`.zip`pacote ( ) - Este pacote contém todos os arquivos de modelo necessários para este plano, bem como quaisquer recursos adicionais, embalados como um `.zip` arquivo.

Todos os pacotes de plano de aplicativos do Azure devem incluir esses dois arquivos na pasta raiz de um `.zip` arquivo:

* Um arquivo de modelo do Gerenciador de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este modelo automatiza a implantação de recursos na assinatura do Azure dos clientes.  Para obter exemplos de modelos do Gerenciador de recursos, consulte a [galeria Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/) ou o [relo de modelos quickstart do Gerenciador de recursos do Azure.](https://github.com/azure/azure-quickstart-templates)

* Uma definição de interface de usuário para a experiência de criação de aplicativos do Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Todas as novas ofertas de aplicativos do Azure também devem incluir uma atribuição de uso do [cliente parceiro Do Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID.

### <a name="previously-published-packages"></a>Pacotes publicados anteriormente 

A subguia **pacotes publicadoanteriormente** permite que você visualize todas as versões publicadas de sua configuração técnica.

## <a name="technical-configuration-managed-application-plans-only"></a>Configuração técnica (somente planos de aplicativos gerenciados)

Os planos de aplicativos gerenciados têm complexidade adicional na guia **de configuração técnica** além dos campos de arquivos **Versão** e **Pacote** descritos acima. 

### <a name="enable-just-in-time-jit-access"></a>Habilite o acesso just-in-time (JIT)

Selecione esta opção para habilitar o acesso Just-in-time (JIT) para este plano.  O acesso ao JIT permite que você solicite acesso elevado aos recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente à leitura aos recursos, mas por um período específico de tempo você pode ter um acesso maior.  Para obter mais informações, consulte [Ativar e solicitar acesso just-in-time para aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Para exigir que os consumidores de seu aplicativo gerenciado concedam acesso permanente à sua conta, deixe essa opção sem controle.

>[!Note]
>Certifique-se de `createUiDefinition.json` atualizar seu arquivo para suportar esse recurso.  

### <a name="deployment-mode"></a>Modo de implantação

Selecione se deve configurar o modo de implantação **completa** ou **incremental** ao implantar este plano: 

* No **modo completo,** uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no `mainTemplate.json`grupo de recursos gerenciados se os recursos não forem definidos no . 
* No **modo incremental,** uma reimplantação do aplicativo deixa os recursos existentes inalterados.

Para saber mais sobre os modos de implantação, consulte [os modos de implantação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL de ponto final de notificação

Especifique um ponto final do Webhook HTTPS para receber notificações sobre todas as operações CRUD em instâncias de aplicativos gerenciadas desta versão do plano.

### <a name="customize-allowed-customer-actions"></a>Personalize as ações permitidas do cliente

Selecione esta opção para especificar quais ações os clientes`*/read`podem executar nos recursos gerenciados, além das " ações que estão disponíveis por padrão. 

Liste as ações adicionais que deseja permitir que seu cliente execute aqui, separada por ponto e vírgula.  Para obter mais informações, consulte [Entendendo negar atribuições para os recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Para as ações disponíveis, confira [Operações do provedor de recursos do Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Por exemplo, para permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

### <a name="global-azure--azure-government-cloud"></a>Nuvem do governo Global Azure / Azure

Indique quem deve ter acesso gerencial a esse aplicativo gerenciado em cada nuvem suportada.  Os usuários, grupos ou aplicativos que você deseja obter permissão para o grupo de recursos gerenciados são identificados usando identidades AAD (AAD) do Azure Active Directory (AAD).

***ID do inquilino do azure Active Directory*** - O ID do inquilino AAD (também conhecido como ID de diretório) contendo as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões.  Você pode encontrar seu ID de inquilino AAD no portal Azure, em [Propriedades para o Diretório Ativo do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizações*** - Adicione o ID do objeto do Azure Active Directory do usuário, grupo ou aplicativo que você deseja ter permissão para o grupo de recursos gerenciado. Identifique o usuário pelo seu ID principal, que pode ser encontrado na lâmina de usuários do [Azure Active Directory no portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada principal, selecione uma das funções incorporadas do Azure AD na lista (Proprietário ou Contribuinte). A função selecionada descreverá as permissões que o principal terá sobre os recursos na assinatura do cliente. Para obter mais informações, consulte [Funções internas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Para obter mais informações sobre o Controle de Acesso baseado em função (RBAC), consulte [Iniciar com o RBAC no portal Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Embora você possa adicionar até 100 autorizações por nuvem, geralmente é mais fácil criar um grupo de usuários do Active Directory e especificar seu ID no "Principal ID".  Isso permitirá adicionar mais usuários ao grupo de gerenciamento após a implantação do plano e reduzir a necessidade de atualizar o plano apenas para adicionar mais autorizações.

### <a name="policy-settings"></a>Configurações de política

Aplique [políticas do Azure](https://docs.microsoft.com/azure/governance/policy/overview) ao seu aplicativo gerenciado para especificar os requisitos de conformidade para a solução implantada.  Para definições de política e o formato dos valores de parâmetro, veja [Exemplos de Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Você pode configurar um máximo de cinco políticas e apenas uma instância de cada opção Políticas.  Algumas políticas requerem parâmetros adicionais.  A SKU Standard é necessária para as políticas de auditoria.  Policy Name é limitado a 50 caracteres.

## <a name="co-sell"></a>Venda Conjunta

Fornecer informações sobre a guia Cosell é totalmente opcional para publicar sua oferta. É necessário para alcançar co-vender o status de Co-sell Ready e IP Co-sell Ready. As informações fornecidas serão usadas pelas equipes de vendas da Microsoft para saber mais sobre sua solução ao avaliar seu ajuste às necessidades do cliente. Não está disponível diretamente para os clientes.

Para obter mais informações sobre como concluir esta guia, consulte [A opção co-venda no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Test drive

A guia **Test drive** permite configurar uma demonstração (ou "test drive") que permitirá que os clientes experimentem sua oferta antes de se comprometerem a comprá-la. Saiba mais no artigo [O que é test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Se você não quiser mais fornecer um test drive para sua oferta, retorne à página **de configuração da Oferta** e desmarque Ativar test **drive**.

### <a name="technical-configuration"></a>Configuração técnica

Os aplicativos do Azure usam inerentemente o tipo de test drive do Azure Resource Manager.  Consulte [a configuração técnica do test drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) para obter mais informações.

### <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura azure separada e exclusiva. (Não é necessário para unidades de teste power bi).

- **ID de assinatura do Azure** (necessário para aplicativos Azure Resource Manager e Logic): Digite o ID de assinatura para conceder acesso aos serviços de sua conta do Azure para relatórios e faturamento de uso de recursos. Recomendamos que você considere [criar uma assinatura azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar em test drives se você ainda não tiver uma. Você pode encontrar seu ID de assinatura do Azure fazendo login no portal do [Azure](https://portal.azure.com/) e navegando na guia **Assinaturas** do menu do lado esquerdo. A seleção da guia exibirá seu ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do inquilino Azure AD** (obrigatório): Insira seu ID de inquilino do Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, faça login no [portal Azure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **Propriedades e procure o número **de ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID de inquilino da [https://www.whatismytenantid.com](https://www.whatismytenantid.com)sua organização usando o URL do seu nome de domínio em: .

- **Nome do inquilino Azure AD** (necessário para o Dynamic 365): Digite o nome do Azure Active Directory (AD). Para encontrar esse nome, entre no [portal Azure,](https://portal.azure.com/)no canto superior direito seu nome de inquilino será listado com o nome da sua conta.

- **ID do aplicativo Azure AD** (obrigatório): Insira o ID do aplicativo Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, entre no [portal do Azure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **inscrições do aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD app client secret** (obrigatório): Insira o segredo do [cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)do aplicativo Azure AD . Para encontrar esse valor, faça login no [portal Azure](https://portal.azure.com/). Selecione a guia Diretório ativo do **Azure** no menu à esquerda, selecione **Registros do aplicativo**e selecione o aplicativo test drive. Em seguida, **selecione Certificados e segredos,** selecione **Novo segredo do cliente,** digite uma descrição, **selecione Nunca** em **Expirado**e escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de copiar o valor, ou então você não terá acesso ao valor.)

Lembre-se **de salvar** antes de passar para a próxima seção!

### <a name="test-drive-listings-optional"></a>Listas de test drive (opcional)

A opção **de listagem test drive** encontrada na guia Test **drive** exibe os idiomas (e mercados) onde sua test drive está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página exibe o status da listagem específica do idioma e a data/hora em que foi adicionada. Você precisará definir os detalhes do test drive (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (necessária): Descreva seu test drive, o que será demonstrado, objetivos para o usuário experimentar, recursos a explorar e quaisquer informações relevantes para ajudar o usuário a determinar se deve adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessárias para o Azure Resource Manager e logic test drives): Explique o que um cliente precisa saber para acessar e usar este test drive. Caminhe por um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Usuário** (obrigatório): Um passo a passo de sua experiência de test drive. O Manual do Usuário deve cobrir exatamente o que você deseja que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer perguntas que eles possam ter. O arquivo deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicione vídeos** (opcional): Os vídeos podem ser carregados no YouTube ou Vimeo e referenciados aqui com um link e imagem em miniatura (533 x 324 pixels) para que o cliente possa visualizar um walk-through de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso os recursos de sua oferta e entender cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Miniatura (533 x 324 px)**: O arquivo de imagem deve estar no formato PNG.

Selecione **Salvar** depois de concluir esses campos.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualizar

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do portal. Você será redirecionado para a **página Revisar e publicar.** 

Se é a primeira vez que publica esta oferta, você pode:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não iniciado* - significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* - significa que a seção tem erros que precisam ser corrigidos ou requer mais informações a serem fornecidas. Por favor, volte para a seção(s) e atualize-a.
    - *Completo* - significa que a seção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em estado completo antes de você poder enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de quaisquer notas suplementares úteis para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail para que você saiba quando uma versão de pré-visualização da oferta estiver disponível para você revisar e aprovar. Retorne ao Partner Center e selecione **go-live** para a oferta de publicar sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa **de validação manual** no processo de publicação representa uma ampla revisão de sua oferta e de seus ativos técnicos associados (especialmente o modelo do Azure Resource Manager), problemas são normalmente apresentados como links de solicitação de tração (PR). Obter uma explicação de como exibir e responder a essas solicitações de pull, confira [Como lidar com comentários de revisão](./azure-apps-review-feedback.md).

Se encontrar erros em uma ou mais das etapas de publicação, você precisará corrigi-los e republicar sua oferta.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
