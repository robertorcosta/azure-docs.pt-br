---
title: Criar uma oferta do Dynamics 365 Business Central – marketplace comercial da Microsoft
description: Conheça as etapas e considerações para criar uma nova oferta do Dynamics 365 Business Central no portal do marketplace comercial no Partner Center. Você pode listar ou vender sua oferta no Azure Marketplace ou por meio do programa CSP (Provedor de Soluções na Nuvem).
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 04de89624dd0e6857e96327bb408cf8700a1f6a2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848872"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Criar uma oferta do Dynamics 365 Business Central

Este artigo explica como criar uma oferta do Dynamics 365 Business Central. O [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planejamento de recursos empresariais (ERP) que lida com uma ampla gama de processos de negócios, incluindo finanças, operações, cadeia de suprimentos, CRM e gerenciamento de projetos e comércio eletrônico. Os pacotes Premium também têm suporte para a fabricação e o modelo de implantação clássico. Todas as ofertas do Dynamics 365 Business Central devem passar pelo nosso processo de certificação.

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Dynamics 365 Business Central**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-bc.png)

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

Liste sua oferta aos clientes com um link para uma avaliação gratuita fornecendo uma URL válida (começando com *http* ou *https*) em que eles possam obter uma avaliação.  Por exemplo, `https://contoso.com/trial/my-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure AD (Azure Active Directory) para automatizar a criação da conta em seu aplicativo. A autenticação com contas Microsoft usando esse token não é um procedimento compatível.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato do cliente conectando seu sistema de CRM (gerenciamento de relacionamento com o cliente). Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre como configurar o CRM, confira [Clientes potenciais](#customer-leads).

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de demonstrar sua oferta para clientes potenciais oferecendo a eles a opção de experimentar antes de comprar, o que resulta no aumento da conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive**. Para remover o test drive de sua oferta, desmarque essa caixa de seleção. Configure o ambiente de test drive na seção [Configuração técnica do test drive](#test-drive-technical-configuration), mais adiante neste tópico.

Para obter informações adicionais, confira [Faça o test drive de sua oferta no marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione entre as seguintes opções:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos adequados a esse cenário usam somente os recursos do Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – a Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos empresariais do Business Central (finanças, operações, cadeia de fornecedores, CRM etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – a Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de Customer Engagement (vendas, serviço, serviço de projeto, serviço de campo etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – a Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos empresariais do Finance and Operations (finanças, operações, manufatura, cadeia de fornecedores etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – um modelo de implantação que abrange todas as arquiteturas de solução complexas. Todos os aplicativos ou produtos personalizados do Dynamics devem usar esse tipo de test drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – consiste em um link incorporado a um dashboard personalizado. Qualquer produto que deseja demonstrar um objeto visual interativo do Power BI deve usar esse tipo de test drive. Aqui, basta fazer upload da URL do Power BI Embedded.

#### <a name="additional-test-drive-resources"></a>Recursos adicionais de test drive

- [Melhores práticas técnicas de test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Melhores práticas de marketing de test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- PDF [Visão geral dos test drives](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (verifique se o bloqueador de pop-ups está desativado)

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, confira [Visão geral de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Essa página permite que você defina as categorias e os setores usados para agrupar sua oferta no Azure Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione no mínimo uma e no máximo cinco categorias, as quais serão usadas para posicionar sua oferta nas áreas de pesquisa apropriadas do marketplace. Lembre-se de incluir como sua oferta é compatível com essas categorias na descrição da oferta. 

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

[Melhores práticas para listagens de oferta do marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

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

Esta página permite que você configure uma demonstração ("test drive") que permite que os clientes experimentem sua oferta antes de adquiri-la. Saiba mais no artigo [o que é um test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** na guia [Configuração da oferta](#test-drive). Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

Os tipos de test drive a seguir estão disponíveis, cada um com os próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

Recursos adicionais de test drive:

- [Práticas recomendadas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Verifique se o bloqueador de pop-ups está desativado)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para test drive do Azure Resource Manager

Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos adequados a esse cenário usam somente os recursos do Azure. Saiba mais sobre como configurar um [test drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (obrigatório) – atualmente, há 26 regiões compatíveis com o Azure onde seu test drive pode ser disponibilizado. Normalmente, você desejará disponibilizar seu test drive nas regiões em que você prevê o maior número de clientes, para que eles possam selecionar a região mais próxima a fim de obter o melhor desempenho. Você precisará certificar-se de que sua assinatura tem permissão para implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias** – selecione o tipo (frequente ou ocasional) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões em que sua oferta está disponível.

    **Frequente** – esse tipo de instância está implantada e aguardando acesso pela região selecionada. Os clientes podem acessar essas instâncias *frequentes* de um test drive instantaneamente em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. É altamente recomendável ter pelo menos uma instância *frequente*, pois a maioria dos seus clientes não quer esperar pelo término de implantações completas e ocorre uma diminuição no uso pelos clientes caso nenhuma instância *frequente* esteja disponível.

    **Ocasional** – esse tipo de instância representa o número total de instâncias que é possível implantar por região. As instâncias *ocasionais* exigem que o modelo do Resource Manager para test drive inteiro passe por uma implantação no momento em que um cliente solicita o test drive; portanto, carregam muito mais lentamente que as instâncias *frequentes*. A compensação é que você só precisa pagar pela duração do test drive, ela *não* está sempre em execução em sua assinatura do Azure, como ocorre com uma instância *frequente*.

- **Modelo do Azure Resource Manager para test drive** – carregue o .zip que contém o modelo do Azure Resource Manager.  Saiba mais sobre como criar um modelo de Azure Resource Manager no artigo de início rápido [Criar e implantar modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do test drive** (obrigatório) – insira o período, em número de horas, durante o qual o test drive permanecerá ativo. O Test Drive é encerrado automaticamente após o término desse período de tempo. Use apenas números inteiros (por exemplo, "2" horas é válido, "1,5" não é).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica do test drive do Dynamics 365

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e a implantação de serviços usando esse tipo de test drive. A configuração para esse tipo de test drive hospedado é a mesma, independentemente de o test drive ser direcionado a um público-alvo de Business Central, Customer Engagement ou Operations.

- **Máximo de unidades de test drive simultâneas** (obrigatório) – defina o número máximo de clientes que podem usar seu test drive simultaneamente. Cada usuário simultâneo consumirá uma licença do Dynamics 365 enquanto o test drive estiver ativo, portanto, será necessário garantir que você tenha licenças suficientes disponíveis para dar suporte ao limite máximo definido. O valor recomendado é de 3 a 5.

- **Duração do test drive** (obrigatório) – insira o período durante o qual o test drive permanecerá ativo, definindo o número de horas. Depois desse número de horas, a sessão será encerrada e deixará de consumir uma de suas licenças. Recomendamos um valor de 2 a 24 horas, dependendo da complexidade da oferta. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas; "1,5" não é válido).  O usuário poderá solicitar uma nova sessão se atingir o tempo limite e desejar acessar o test drive novamente.

- **URL da instância** (obrigatório) – a URL em que o cliente começará o test drive dele. Normalmente, a URL da instância do Dynamics 365 executando o aplicativo com os dados de exemplo instalados (por exemplo, `https://testdrive.crm.dynamics.com`).

- **URL da API Web da instância** (obrigatório) – recupere a URL da API Web para sua instância do Dynamics 365 fazendo logon em sua conta do Microsoft 365 e navegando até **Configurações** \&gt; **Personalização** \&gt; **Recursos de Desenvolvedor** \&gt; **API Web da Instância (URL da Raiz do Serviço)** , copie a URL encontrada aqui (por exemplo, `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nome da função** (obrigatório) – forneça o nome da função de segurança que você definiu em seu test drive personalizado do Dynamics 365, o qual será atribuído ao usuário durante o test drive (por exemplo, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica do test drive do aplicativo lógico

Todos os produtos personalizados devem usar esse tipo de modelo de implantação de test drive que abrange uma variedade de arquiteturas de solução complexas. Para obter mais informações sobre como configurar test drives de Aplicativo Lógico, visite [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (obrigatório, lista suspensa de seleção única) – atualmente, há 26 regiões compatíveis com o Azure onde seu test drive pode ser disponibilizado. Os recursos para seu aplicativo lógico serão implantados na região que você selecionar. Se o Aplicativo Lógico tiver recursos personalizados armazenados em uma região específica, verifique se essa região está selecionada aqui. A melhor maneira de implantar de forma integral o Aplicativo Lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona antes de gravá-lo aqui.

- **Máximo de unidades de test drive simultâneas** (obrigatório) – defina o número máximo de clientes que podem usar seu test drive simultaneamente. Essas unidades de teste já estão implantadas, permitindo que os clientes as acessem instantaneamente sem esperar por uma implantação.

- **Duração do test drive** (obrigatório) – insira o período durante o qual o test drive permanecerá ativo, em número de horas. O test drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório) ‒ insira o nome do [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) em que o test drive do Aplicativo Lógico está salvo.

- **Nome do Aplicativo Lógico do Azure** (obrigatório) – insira o nome do Aplicativo Lógico que atribui o test drive ao usuário. Esse Aplicativo Lógico precisa ser salvo no grupo de recursos do Azure mencionado acima.

- **Desprovisionar o nome do aplicativo lógico** (obrigatório) – insira o nome do aplicativo lógico que desprovisiona o test drive depois que o cliente é concluído. Esse Aplicativo Lógico precisa ser salvo no grupo de recursos do Azure mencionado acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para test drives do Power BI

Os produtos que desejam demonstrar um visual interativo do Power BI podem usar um link incorporado para compartilhar um dashboard personalizado como o test drive deles, sem necessidade de nenhuma configuração técnica adicional. Saiba mais sobre como configurar modelos de aplicativo do [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para implantar o test drive em seu nome, crie e forneça uma assinatura separada e exclusiva do Azure. (Não é necessário para test drives do Power BI).

- **ID da assinatura do Azure** (necessário para Azure Resource Manager e Aplicativos Lógicos) – insira a ID da assinatura para permitir acesso aos serviços de conta do Azure para cobrança e relatório de uso de recursos. Recomendamos que você considere [criar uma assinatura separada do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar em test drives, caso ainda não tenha uma. Você pode encontrar sua ID da assinatura do Azure entrando no [portal do Azure](https://portal.azure.com/) e navegando até a guia **Assinaturas** no menu do lado esquerdo. A seleção dessa guia exibirá sua ID da assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do locatário do Azure AD** (obrigatório) – insira sua [ID do locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) do Azure AD (Active Directory). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades e, em seguida, procure o número listado da **ID do diretório** (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar a ID do locatário da sua organização usando seu endereço de nome de domínio em [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nome do locatário do Azure AD** (necessário para o Dynamics 365) – insira seu nome do Azure AD (Active Directory). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário estará listado abaixo do seu nome de conta.

- **ID do aplicativo do Azure AD** (obrigatório) – insira sua [ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) do Azure AD (Active Directory). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Registros de aplicativo** e, em seguida, procure o número da **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Segredo do cliente do aplicativo do Azure AD** (obrigatório) – insira seu [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) do aplicativo do Azure AD. Para encontrar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **Registros de aplicativo** e, em seguida, selecione seu aplicativo de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente**, insira uma descrição, selecione **Nunca** em **Expira em** e, em seguida, escolha **Adicionar**. Certifique-se de anotar o valor. (Não saia da página antes de fazer isso ou você não terá mais acesso ao valor.)

### <a name="test-drive-marketplace-listings"></a>Listagens do marketplace de test drive

Você define os detalhes da experiência de test drive na opção **Listagem do marketplace** na guia **Test drive**.

> [!NOTE]
> Você pode fornecer detalhes da listagem de test drive em apenas um idioma. Eles não precisam estar em inglês, desde que a descrição da oferta inicie com a frase: “Este aplicativo só está disponível em [idioma, exceto inglês].” Também é aceitável fornecer uma *URL de link de ajuda* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de test drive.

- **Descrição** (obrigatório) – descreva seu test drive, o que será demonstrado, os objetivos do usuário com os quais experimentar, os recursos a serem explorados e as informações relevantes para ajudar o usuário a determinar se sua oferta deve ou não ser adquirida. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessário para test drives de Aplicativos Lógicos e do Azure Resource Manager) – explique o que um cliente precisa saber para acessar e usar esse test drive. Faça um passo a passo para um cenário de uso de sua oferta e exatamente o que o cliente deve saber para acessar recursos durante o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do usuário** (obrigatório) – um passo a passo detalhado da sua experiência de test drive. O Manual do usuário precisa abranger exatamente o que você deseja que os clientes ganhem com a experiência de fazer o test drive e deve servir como uma referência para quaisquer perguntas que eles possam ter. O arquivo precisa estar no formato PDF e ser nomeado (máximo de 255 caracteres) após o upload.

- **Vídeos** (opcional) – os vídeos podem ser carregados no YouTube ou no Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um passo a passo de informações para ajudá-los a entender melhor o test drive, incluindo como usar com êxito os recursos de sua oferta e entender cenários que realçam os benefícios desses recursos.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Imagem em miniatura** (a imagem precisa estar no formato PNG e ter 533 x 324 pixels)

Selecione **Salvar rascunho** antes de continuar.

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
- Envie a oferta para publicação selecionando **Enviar**. Nós enviaremos um e-mail a você quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Retorne ao Partner Center e selecione **Ativar** a oferta para publicar sua oferta para o público (ou, se uma oferta privada, para o público-alvo privado).

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
