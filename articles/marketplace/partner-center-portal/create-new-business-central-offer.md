---
title: Crie uma nova oferta da Dynamics 365 Business Central no mercado comercial da Microsoft
description: Como criar uma nova oferta da Dynamics 365 Business Central para listagem ou venda no Azure Marketplace, Microsoft AppSource ou através do programa Cloud Solution Provider (CSP) usando o portal do mercado comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 10fa16767061506eccf212c182e738246dc9fe7b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867221"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Crie uma nova oferta da Dynamics 365 Business Central

Este artigo explica como criar uma nova oferta da Dynamics 365 Business Central. [O Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planejamento de recursos corporativos (ERP) que lida com uma ampla gama de processos de negócios, incluindo finanças, operações, cadeia de suprimentos, CRM e gerenciamento de projetos e comércio eletrônico. Os pacotes premium também suportam modelo de implantação clássico e fabricação. Todas as ofertas da Dynamics 365 Business Central devem passar pelo nosso processo de certificação.

Para começar a criar ofertas da Dynamics 365 Business Central, certifique-se de criar primeiro [uma conta do Partner Center](./create-account.md) e abrir o painel do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a página **Visão Geral** selecionada.

![Painel de mercado comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Uma vez publicada uma oferta, as edições da oferta feita no Partner Center só serão atualizadas no sistema e nas fachadas das lojas após a republicação. Por favor, certifique-se de enviar a oferta para publicação depois de fazer alterações.

## <a name="create-a-new-offer"></a>Criar uma oferta

Selecione o botão **+ Nova oferta** e selecione o item do menu **Dynamics 365 Business Central.** A caixa de diálogo **Nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>Oferecer ID e alias

- **ID de oferta**: Identificador exclusivo para cada oferta em sua conta. Este ID será visível para os clientes no endereço URL para a oferta de marketplace e modelos do Azure Resource Manager (se aplicável). O ID de oferta deve ser de caracteres alfanuméricos minúsculos (incluindo hífens e sublinhados, mas sem espaço em branco), limitado a 50 caracteres, e não pode ser alterado depois de selecionar **Criar**.  Por exemplo, se você inserir *test-offer-1* aqui, o URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Ofereça alias**: O nome usado para se referir à oferta dentro do Partner Center. Este nome não será usado no mercado, e é diferente do nome da oferta e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **Criar**.

Depois de inserir seu **alias de ID de oferta** e **oferta,** selecione **Criar**. Você poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Configuração da oferta

A página **de configuração Oferta** pede as seguintes informações. Certifique-se de selecionar **Salvar** depois de concluir esses campos.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como você quer que os clientes em potencial interajam com essa oferta de listagem?

Selecione a opção que você gostaria de usar para esta oferta.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Liste sua oferta aos clientes gratuitamente fornecendo uma URL válida (começando com *http* ou *https)* onde eles podem acessar seu aplicativo.  Por exemplo: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita, fornecendo uma URL válida (começando com *http* ou *https)* onde eles podem obter uma avaliação.  Por exemplo: `https://contoso.com/trial/my-app`. Os testes gratuitos de listagem de ofertas são criados, gerenciados e configurados pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá através do seu link de teste só podem ser usados para obter informações do usuário através do Azure Active Directory (Azure AD) para automatizar a criação de conta em seu aplicativo. As contas microsoft não são suportadas para autenticação usando este token.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato com o cliente conectando seu sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). O cliente receberá permissão para compartilhar suas informações. Esses detalhes do cliente, juntamente com o nome da oferta, ID e fonte de marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre a configuração do seu CRM, consulte [O gerenciamento de chumbo connect](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando em maior conversão e geração de leads altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar um test drive, verifique **a Caixa Ativar um test drive.** Em seguida, você precisará configurar um ambiente de demonstração na [configuração técnica do test drive](#test-drive-technical-configuration) configurar para permitir que os clientes experimentem sua oferta por um período fixo de tempo. 

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione entre as seguintes opções:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se encaixam neste cenário usam apenas recursos do Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos corporativos da Business Central (finanças, operações, cadeia de suprimentos, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de Engajamento do Cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dinâmica 365 para Operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos corporativos de finanças e operações (finanças, operações, manufatura, cadeia de suprimentos, etc.). 
- **[App lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Um modelo de implantação que abrange todas as arquiteturas de soluções complexas. Qualquer produto personalizado deve usar este tipo de Test Drive.
- **[POWER BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Um link incorporado a um painel personalizado. Os produtos que desejam demonstrar um visual interativo de Power BI devem usar este tipo de Test Drive. Aqui, basta fazer upload da URL do Power BI Embedded.

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

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão do aplicativo

Digite o número da versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça seus próprios termos e condições legais no campo **Termos e condições.** Você também pode fornecer a URL onde seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de tentar sua oferta.

## <a name="offer-listing"></a>Listagem de ofertas

A página de listagem de ofertas é onde você define detalhes para sua oferta (como nome, descrição, imagens, etc.).

> [!NOTE]
> Você pode fornecer detalhes de listagem de oferta em apenas um idioma. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]". Também é aceitável fornecer uma *URL de link de ajuda* para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem de Oferta.

### <a name="name"></a>Nome

O nome que você inserir aqui será mostrado aos clientes como o título da sua lista de ofertas. Este campo está pré-preenchido com o texto que você inseriu para **oferecer alias** quando você criou a oferta, mas você pode alterar esse valor. Este nome pode ser registrado (e você pode incluir símbolos de marca ou direitos autorais). O nome não pode ser mais de 50 caracteres e não pode incluir nenhum emoji.

### <a name="short-description"></a>Descrição breve

Forneça uma breve descrição de sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de marketplace.

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

Para tornar sua descrição de oferta mais atraente, use o rico editor de texto para formatar sua descrição.

![Usando o rico editor de texto](./media/text-editor2.png)

Use as seguintes instruções para usar o editor de texto rico:

- Para alterar o formato do seu conteúdo, destaque o texto que deseja formatar e selecionar um estilo de texto, conforme mostrado abaixo:

     ![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com uma numeração ou numerada ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para recusar](./media/text-editor5.png)

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Você pode, opcionalmente, inserir até três palavras-chave de pesquisa para ajudar os clientes a encontrar sua oferta no mercado. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo funciona

Se você quiser informar os clientes que seu aplicativo funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="helpprivacy-urls"></a>URLs de ajuda/privacidade

Esta seção permite que você forneça links para ajudar os clientes a entender mais sobre sua oferta.

#### <a name="help-link"></a>Link de ajuda

Digite a URL onde os clientes podem saber mais sobre sua oferta. Seu **link de ajuda** não pode ser o mesmo que sua URL de **suporte** (explicada abaixo).

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Digite a URL na política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contact-information"></a>Informações de contato

Nesta seção, você deve fornecer o nome, e-mail e número de telefone para um **contato de suporte** e um contato de **Engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas a parceiros CSP.

Na seção **de contato de suporte,** você também deve fornecer a **URL de suporte** onde os parceiros CSP podem encontrar suporte para sua oferta. Sua URL de suporte não pode ser a mesma do link **de ajuda**.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, como white papers, folhetos, checklists ou apresentações. Esses documentos devem estar no formato .pdf.

### <a name="marketplace-images"></a>Imagens de mercado

Nesta seção, você pode fornecer logotipos e imagens que serão usadas ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato .png. Carregue seu logotipo de oferta em dois tamanhos: **Pequeno (48 x 48)** e **Grande (216 x 216)**.

>[!Note]
>Se você tiver um problema de upload de arquivos, https://upload.xboxlive.com certifique-se de que sua rede local não bloqueie o serviço usado pelo Partner Center.

#### <a name="screenshots"></a>Capturas de tela

Adicione capturas de tela que mostram como sua oferta funciona. Pelo menos três capturas de tela são necessárias, e você pode somar até cinco. Todas as capturas de tela devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>vídeos

Você pode, opcionalmente, adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou Vimeo. Para cada um, digite o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

- [Melhores práticas para listagens de ofertas de marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilidade

A página **Disponibilidade** oferece opções sobre onde e como disponibilizar sua oferta.

### <a name="markets"></a>Mercados

Esta seção permite especificar os mercados em que sua oferta deve estar disponível. Para isso, selecione **Editar mercados**, que exibirá a janela popup **de seleção de mercado.**

Por padrão, nenhum mercado é selecionado, mas você deve selecionar pelo menos um mercado para publicar sua oferta. Clique **em Selecionar tudo** para disponibilizar sua oferta em todos os mercados possíveis ou selecionar os mercados específicos que deseja adicionar. Depois de terminar, selecione **Salvar**.

Suas seleções aqui se aplicam apenas a novas aquisições; se alguém já tem seu aplicativo em um determinado mercado, e você mais tarde remove esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-lo, mas nenhum novo cliente nesse mercado será capaz de obter a sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender a quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que mesmo se você selecionar todos os mercados, leis locais e restrições ou outros fatores podem impedir que certas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Audiência de pré-visualização

Antes de publicar sua oferta ao vivo para a oferta de mercado mais ampla, você primeiro precisará disponibilizá-la para um público limitado **de Visualização**. Digite uma **tecla Ocultar** (qualquer string usando apenas letras minúsculas e/ou números) aqui. Os membros do seu público de visualização podem usar essa chave de ocultação como um token para visualizar uma visualização de sua oferta no mercado.

Então, quando você estiver pronto para disponibilizar sua oferta e remover a restrição de visualização, você precisará remover a **tecla Ocultar** e publicar novamente.

## <a name="technical-configuration"></a>Configuração técnica

A página **de configuração técnica** define os detalhes técnicos usados para se conectar à sua oferta. Essa conexão nos permite prover sua oferta para o cliente final se eles optarem por adquiri-la.

### <a name="package-type"></a>Tipo de pacote

Selecione a opção que se aplica à sua oferta:

- **Adicionar**: Um aplicativo add-on amplia a experiência e a funcionalidade existente do Dynamics 365 Business Central. Para obter mais informações, consulte [aplicativos adicionais](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Conectar**: Um aplicativo Connect pode ser usado no cenário em que deve ser estabelecida uma conexão ponto a ponto entre a Dynamics 365 Business Central e uma solução ou serviço de terceiros. Para obter mais informações, consulte [Conectar](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Upload de arquivos

Se você selecionou **Adicionar acima,** onde você carregará o arquivo de pacote da sua oferta, juntamente com os arquivos do pacote para qualquer extensão na qual ele tenha dependências.

#### <a name="extensions-package-file"></a>Arquivo de pacote de extensões

Faça upload do arquivo do pacote de extensão (.app) para sua oferta.

#### <a name="library-package-file"></a>Arquivo de pacote de biblioteca

Obrigatório se sua oferta deve ser instalada juntamente com outra extensão que não será publicada no mercado. Se assim for, carregue seu arquivo .app aqui.

#### <a name="dependency-package-file"></a>Arquivo do pacote de dependência

Obrigatório se sua oferta deve ser instalada juntamente com outra extensão que já foi publicada para o mercado. Se sim, carregue `.app` `.zip` seu ou arquivo aqui.

### <a name="url-to-app-installation"></a>URL para instalação de aplicativos

Se você selecionou **Conectar** acima, forneça a URL para a instalação do aplicativo aqui. Para serviços conectados que não requerem instalação, forneça a URL para sua página de aterrissagem do serviço ou página de inscrição.

## <a name="test-drive-technical-configuration"></a>Configuração técnica do test drive

Se você selecionou **Habilitar um test drive** na página [de configuração oferta,](#offer-setup) você precisará fornecer detalhes aqui para permitir que os clientes experimentem um test drive de sua oferta.

A página **test drive** permite configurar uma demonstração (ou "test drive") que permitirá que os clientes experimentem sua oferta antes de se comprometerem a comprá-la. Saiba mais no artigo [O que é test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se você não quiser mais fornecer um test drive para sua oferta, retorne à página **[de configuração da Oferta](#offer-setup)** e desmarque Ativar test **drive**.

Os seguintes tipos de test drives estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [BI de](#technical-configuration-not-required-for-power-bi-test-drives) potência (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para test drive do Azure Resource Manager

Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se encaixam neste cenário usam apenas recursos do Azure. Saiba mais sobre como configurar [um test drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões (necessárias):** Atualmente existem 26 regiões suportadas pelo Azure onde seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para obter o melhor desempenho. Você precisará ter certeza de que sua assinatura está autorizada a implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicados pelo número de regiões onde sua oferta está disponível.

**Hot**: Este tipo de instância é implantada e aguarda acesso por região selecionada. Os clientes podem acessar instantaneamente *instâncias quentes* de um test drive, em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. É altamente recomendável ter pelo menos uma instância *Hot,* já que a maioria dos clientes não quer esperar por implantações completas, resultando em uma queda no uso do cliente se nenhuma instância *Hot* estiver disponível.

**Frio**: Este tipo de instância representa o número total de instâncias que podem ser implantadas por região. As instâncias frias exigem que todo o modelo do Test Drive Resource Manager seja implantado quando um cliente solicita o test drive, de modo que as instâncias *frias* são muito mais lentas de carregar do que *as instâncias hot.* A compensação é que você só tem que pagar pela duração do test drive, *nem* sempre está sendo executado na sua assinatura do Azure como com uma instância *Hot.*

- **Test drive Modelo do Gerenciador de recursos do Azure**: Carregue o .zip contendo o modelo do Azure Resource Manager.  Saiba mais sobre a criação de um modelo do Azure Resource Manager no artigo quickstart [Criar e implantar modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo, em # de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para test drive Dynamics 365

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e implantação do serviço usando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar mirando um público da Business Central, Customer Engagement ou Operations.

- **Máximo de test drives simultâneos** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive de uma só vez. Cada usuário simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, então você precisará garantir que você tenha licenças suficientes disponíveis para suportar o limite máximo definido. O valor recomendado é de 3 a 5.

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo definindo o número de horas. Depois de tantas horas, a sessão terminará e não consumirá mais uma de suas licenças. Recomendamos um valor de 2-24 horas, dependendo da complexidade de sua oferta. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).  O usuário pode solicitar uma nova sessão se ficar sem tempo e quiser acessar o test drive novamente.

- **URL de instância** (obrigatório): A URL onde o cliente iniciará seu test drive. Normalmente, a URL da sua instância Dynamics 365 executando seu `https://testdrive.crm.dynamics.com`aplicativo com dados de amostra instalados (por exemplo, ).

- **Url de API da Web (obrigatório):** Recupere a URL da API da Web para sua instância Dynamics 365 fazendo login em sua conta Microsoft 365 e navegando para **Configurações** \&gt; **Personalização** \&gt; **Recursos do** \&desenvolvedor gt; **API da Web de instância (Service Root URL)** `https://testdrive.crm.dynamics.com/api/data/v9.0`, copie a URL encontrada aqui (por exemplo, ).

- **Nome da função** (obrigatório): Forneça o nome da função de segurança que você definiu em seu test drive personalizado Dynamics 365, que será atribuído ao usuário durante sua test drive (por exemplo, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para test drive do aplicativo Logic

Qualquer produto personalizado deve usar esse tipo de modelo de implantação de test drive que abrange uma variedade de arquiteturas de soluções complexas. Para obter mais informações sobre como configurar os test drives do Logic App, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Engajamento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista suspensa de seleção única necessária): Atualmente existem 26 regiões suportadas pelo Azure onde seu test drive pode ser disponibilizado. Os recursos para o seu aplicativo Logic serão implantados na região selecionada. Se o seu App Logic tiver algum recurso personalizado armazenado em uma região específica, certifique-se de que a região esteja selecionada aqui. A melhor maneira é implantar totalmente seu App Logic localmente em sua assinatura do Azure no portal e verificar se ele funciona corretamente antes de fazer essa seleção.

- **Máximo de test drives simultâneos** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive de uma só vez. Esses test drives já estão implantados, permitindo que os clientes os acessem instantaneamente sem esperar por uma implantação.

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo, em # de horas. O test drive termina automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório): Digite o nome do [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde sua unidade de teste logic app é salva.

- **Nome do aplicativo lógico do Azure** (obrigatório): Digite o nome do aplicativo Logic que atribui a unidade de teste ao usuário. Este aplicativo Logic deve ser salvo no grupo de recursos do Azure acima.

- **Nome do aplicativo lógico de desprovisionamento** (obrigatório): Digite o nome do aplicativo Logic que desprovisiona o test drive assim que o cliente estiver concluído. Este aplicativo Logic deve ser salvo no grupo de recursos do Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste Power BI

Os produtos que desejam demonstrar um visual interativo do Power BI podem usar um link incorporado para compartilhar um painel personalizado como seu test drive, sem necessidade de configuração técnica adicional. Saiba mais sobre como configurar aplicativos de modelo[power bi.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura azure separada e exclusiva. (Não é necessário para unidades de teste power bi).

- **ID de assinatura do Azure** (necessário para aplicativos Azure Resource Manager e Logic): Digite o ID de assinatura para conceder acesso aos serviços de sua conta do Azure para relatórios e faturamento de uso de recursos. Recomendamos que você considere [criar uma assinatura azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar em test drives se você ainda não tiver uma. Você pode encontrar seu ID de assinatura do Azure fazendo login no portal do [Azure](https://portal.azure.com/) e navegando na guia **Assinaturas** do menu do lado esquerdo. A seleção da guia exibirá seu ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do inquilino Azure AD** (obrigatório): Insira seu ID de inquilino do Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, faça login no [portal Azure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **Propriedades e procure o número **de ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID de inquilino da [https://www.whatismytenantid.com](https://www.whatismytenantid.com)sua organização usando o URL do seu nome de domínio em: .

- **Nome do inquilino Azure AD** (necessário para o Dynamic 365): Digite o nome do Azure Active Directory (AD). Para encontrar esse nome, entre no [portal Azure,](https://portal.azure.com/)no canto superior direito seu nome de inquilino será listado com o nome da sua conta.

- **ID do aplicativo Azure AD** (obrigatório): Insira o ID do aplicativo Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, entre no [portal do Azure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **registros do aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD app client secret** (obrigatório): Insira o segredo do [cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)do aplicativo Azure AD . Para encontrar esse valor, faça login no [portal Azure](https://portal.azure.com/). Selecione a guia Diretório ativo do **Azure** no menu à esquerda, selecione **Registros do aplicativo**e selecione o aplicativo test drive. Em seguida, **selecione Certificados e segredos,** selecione **Novo segredo do cliente,** digite uma descrição, **selecione Nunca** em **Expirado**e escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de fazer isso, ou então você não terá acesso ao valor.)

Lembre-se **de salvar** antes de passar para a próxima seção!

### <a name="test-drive-marketplace-listings"></a>Listas de mercado de test drive

A opção **de listagem do Marketplace** encontrada na guia Test **drive** é onde você define detalhes para a experiência de test drive.

> [!NOTE]
> Você pode fornecer detalhes de listagem de test drive em apenas um idioma. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]". Também é aceitável fornecer uma *URL de link de ajuda* para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem do test drive.


- **Descrição** (necessária): Descreva seu test drive, o que será demonstrado, objetivos para o usuário experimentar, recursos a explorar e quaisquer informações relevantes para ajudar o usuário a determinar se deve adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessárias para o Azure Resource Manager e logic test drives): Explique o que um cliente precisa saber para acessar e usar este test drive. Caminhe por um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Usuário** (obrigatório): Um passo a passo de sua experiência de test drive. O Manual do Usuário deve cobrir exatamente o que você deseja que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer perguntas que eles possam ter. O arquivo deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicione vídeos** (opcional): Os vídeos podem ser carregados no YouTube ou Vimeo e referenciados aqui com um link e imagem em miniatura (533 x 324 pixels) para que o cliente possa visualizar um walk-through de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso os recursos de sua oferta e entender cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Miniatura (533 x 324 px)**: O arquivo de imagem deve estar no formato PNG.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite que você forneça informações adicionais sobre sua oferta para nos ajudar a validar sua oferta. Essas informações não são mostradas aos clientes ou publicadas no mercado.

### <a name="target-release"></a>Liberação de alvo

Indique qual versão do Microsoft Dynamics Business Central sua solução tem como alvo: **Atual**, **Next major**ou Next **minor**. Essas informações nos permitem testar sua solução adequadamente.

### <a name="supported-editions"></a>Edições com suporte

Se sua oferta exigir a edição Premium do Microsoft Dynamics 365 Business Central, selecione somente **Premium.** Caso contrário, selecione **essencial** e **premium**.

### <a name="key-usage-scenario"></a>Cenário de uso chave

Você deve `.pdf` carregar um arquivo que lista os principais cenários de uso da sua oferta listados em um documento (formato .pdf). Todos os cenários listados aqui podem ser verificados por nossa equipe de validação antes de aprovarmos sua oferta para o mercado.

### <a name="app-tests-automation"></a>App testa automação

Você pode, opcionalmente, carregar um **arquivo de automação de testes de aplicativos** aqui (.app).

### <a name="test-accounts"></a>Contas de teste

Se uma conta de teste for necessária para que nossa equipe de certificação revise adequadamente sua oferta, faça upload de um arquivo .pdf, .doc ou .docx com as informações de suas **contas de teste.**

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

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
