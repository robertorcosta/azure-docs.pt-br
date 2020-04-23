---
title: Crie uma nova oferta SaaS para o mercado comercial da Microsoft
description: Como criar uma nova oferta de Software as a Service (SaaS) para listagem ou venda no Microsoft AppSource, Azure Marketplace ou através do programa Cloud Solution Provider (CSP) usando o programa de mercado comercial da Microsoft no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869770"
---
# <a name="create-a-new-saas-offer"></a>Criar uma oferta de SaaS

Para começar a criar ofertas de Software as a Service (SaaS), certifique-se de criar primeiro [uma conta do Partner Center](./create-account.md) e abrir o painel do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a guia **Visão Geral** selecionada.

![Painel de mercado comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Uma vez publicada uma oferta, as edições da oferta feita no Partner Center só serão atualizadas no sistema e nas fachadas das lojas após a republicação. Certifique-se de enviar a oferta para publicação depois de fazer alterações.

Selecione o + **Nova oferta...** e, em seguida, selecione o **software como item do** menu Serviço.

Se você selecionar outro tipo de oferta, você pode ser redirecionado para o portal de [parceiros em nuvem](https://cloudpartner.azure.com/)mais antigo . Apenas as ofertas SaaS e Dynamics 365 estão disponíveis no portal Commercial Marketplace no Partner Center neste momento.

![Criar janela de oferta no Partner Center](./media/new-offer-click.png)

A caixa de diálogo **Nova oferta** é exibida.

![Nova caixa de diálogo de oferta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Oferecer ID e alias

- **ID de oferta**: Identificador exclusivo para cada oferta em sua conta. Este ID será visível para os clientes no endereço URL para a oferta de marketplace e modelos do Azure Resource Manager (se aplicável). O ID de oferta deve ser minúsculo, alfanumérico (incluindo hífens e sublinhados, mas sem espaço em branco). O **ID de oferta** é limitado a 50 caracteres e não pode ser alterado depois de selecionar *Criar*.  
Exemplo: test-offer-1
<br>Resultando na URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ofereça alias**: O nome usado para se referir à oferta dentro do portal do Partner Center. Este nome não será usado no mercado e é diferente do Nome da *Oferta* e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona *Criar*.

<br>Exemplo: Oferta de teste 1&#8482;

Selecione **Criar**.  Uma página **de visão geral da Oferta** foi criada para esta oferta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da oferta** inclui:

- O **status de Publicação** exibe uma representação visual das etapas necessárias para publicar esta oferta e quanto tempo cada passo levará para ser concluído. Os ícones da etapa de publicação incompleta serão acinzentados.

- O menu **'Visão geral' da Oferta** contém uma lista de links para a execução de operações nesta oferta. Esta lista de operações mudará com base na seleção que você fizer para sua oferta.  
    - Se a oferta for um rascunho - Exclua o rascunho
    - Se a oferta for ao vivo - Pare de vender oferta
    - Se a oferta está em pré-visualização - Go-live
    - Se você ainda não completou a assinatura do editor - Cancele a publicação

## <a name="offer-setup"></a>Configuração da oferta

A guia **De configuração Oferta** solicita as seguintes informações. Selecione **Salvar** depois de concluir esses campos.

- **Você gostaria de vender através da Microsoft?** (Sim/Não)
    - **Sim,** você gostaria de vender sua oferta através da Microsoft, com as transações de marketplace de hospedagem da Microsoft em seu nome; Ou 
    - **Não,** você prefere apenas listar sua oferta através dos marketplaces, processando quaisquer transações monetárias independentemente da Microsoft.

### <a name="sell-through-microsoft"></a>Vender por meio da Microsoft

Vender através da Microsoft fornece melhor descoberta e aquisição de clientes, permite que a Microsoft hospede transações de marketplace em seu nome e aproveita os recursos de comércio globalmente disponíveis da Microsoft.

#### <a name="saas-offer-requirements"></a>Requisitos de oferta do SaaS

Para listar as ofertas de Software as a Service (SaaS) com o Commercial Marketplace no Partner Center, os seguintes critérios devem ser atendidos:

- Sua oferta deve usar [o Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para gerenciamento de identidade e autenticação.
- Sua oferta deve usar [APIs de preenchimento SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para se integrar ao Azure Marketplace.
- Para obter requisitos mais extensos, consulte o [Guia de Publicação de Ofertas do SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opções de preços e faturamento do SaaS

Com as soluções SaaS em execução na assinatura do Azure do editor, as taxas de licença pagas pelos clientes incluem o custo da infra-estrutura sobre a qual o software é implantado. O uso da infra-estrutura do Azure é gerenciado e cobrado diretamente para você, o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Os editores devem agrupar as taxas de uso da infra-estrutura do Azure em seus preços de licença de software. 

O SaaS oferece suporte ao faturamento mensal ou anual com base em uma taxa fixa, por usuário ou taxas de consumo usando o serviço de cobrança medido. O mercado comercial da Microsoft opera em um modelo de agência, pelo qual os editores definem preços, a Microsoft fatura clientes e a Microsoft paga receita ao editor enquanto retenho uma taxa de agência.

A tabela a seguir mostra um exemplo de divisão de custos e pagamentos para demonstrar o modelo da agência.

|**Custo de sua licença**|**$100 por mês**|
|:---|:---|
|Custo de uso do Azure (D1/1-Núcleo)|Cobrados diretamente para o publicador, não o cliente|
|O cliente é cobrado pela Microsoft|US$ 100,00 por mês (o Publisher deve responder por quaisquer custos incorridos ou de infra-estrutura de passagem na taxa de licença)|

|**Faturas da Microsoft**|**$100 por mês**|
|:---|:---|
|A Microsoft paga para você 80% do seu custo de licença <br>**Para aplicativos SaaS qualificados, a Microsoft paga 90% do custo da sua licença*|US $80,00 por mês <br>*$* 90,00 por mês*|

- Neste exemplo, a Microsoft cobra US$ 100,00 ao cliente pela sua licença de software e paga US$ 80,00 ao editor.
- Os parceiros que se qualificaram para a **Taxa de Serviço de Marketplace Reduzida** verão uma taxa de transação reduzida nas ofertas do SaaS de maio de 2019 até junho de 2020. Neste cenário, a Microsoft cobra US$ 100,00 pela sua licença de software e paga US$ 90,00 ao editor.

> [!NOTE]
> **Taxa de serviço de marketplace reduzida**: Para certas ofertas SaaS que você publicou em nosso Mercado Comercial, a Microsoft reduzirá sua taxa de serviço de marketplace de 20% (conforme descrito no Acordo Microsoft Publisher) para 10%. Para que sua oferta se qualifique, pelo menos uma de suas ofertas deve ter sido designada pela Microsoft como sendo pronta para co-venda ip ou co-venda IP priorizada.  A elegibilidade deve ser cumprida pelo menos cinco (5) dias úteis antes do final de cada mês calendário, a fim de receber esta taxa de serviço de marketplace reduzida para o mês.  A Taxa de Serviço de Marketplace Reduzida não se aplica a VMs, Aplicativos Gerenciados ou quaisquer outros produtos disponibilizados através de nosso Marketplace Comercial.  A Taxa de Serviço de Marketplace Reduzida só estará disponível para ofertas qualificadas para taxas de licença cobradas pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a Taxa de Serviço do Marketplace voltará ao seu valor normal.

### <a name="list-through-microsoft"></a>Liste através da Microsoft

Promova seus negócios com a Microsoft criando uma listagem de marketplace. Selecionar apenas listar sua oferta e não transacionar através da Microsoft significa que a Microsoft não participa diretamente de transações de licença de software. Não há taxa de transação associada e o editor mantém 100% de quaisquer taxas de licenciamento de software cobradas do cliente. No entanto, o editor é responsável por apoiar todos os aspectos da transação de licença de software, incluindo, mas não se limitando a: cumprimento de pedidos, medição, faturamento, faturamento, pagamento e cobrança.

- **Como você quer que os clientes em potencial interajam com essa oferta de listagem?**

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Liste sua oferta aos clientes gratuitamente fornecendo uma URL válida (começando com *http* ou *https)* onde eles podem obter uma avaliação através [de autenticação com um clique usando o Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Por exemplo: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita, fornecendo uma URL válida (começando com *http* ou *https),* onde eles podem obter uma avaliação através [de autenticação com um clique usando o Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Por exemplo: `https://contoso.com/trial/saas-app`. Os testes gratuitos de listagem de ofertas são criados, gerenciados e configurados pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá através do seu link de teste só podem ser usados para obter informações do usuário através do Azure AD para automatizar a criação de conta em seu aplicativo. As contas Microsoft (MSA) não são suportadas para autenticação usando este token.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato com o cliente conectando seu sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). O cliente receberá permissão para compartilhar suas informações. Esses detalhes do cliente, juntamente com o nome da oferta, ID e fonte de marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre a configuração do seu CRM, consulte [O gerenciamento de chumbo connect](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Exemplo de listagem de oferta do Marketplace

![Lista de ofertas de marketplace de exemplo com notas](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Habilitar um test drive

Um test drive é uma ótima maneira de mostrar sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando em maior conversão e geração de leads altamente qualificados. Para saber mais, consulte [Permitir que seus clientes testem sua oferta](./test-drive.md).

- **Habilite um test drive** (caixa de seleção)

Ao ativar o test drive, você será solicitado a configurar um ambiente de demonstração para que os clientes experimentem sua oferta por um período fixo de tempo. 

### <a name="test-drive-resources"></a>Recursos de test drive

- [Práticas recomendadas](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- de test drive de marketing[Test Drive test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral do Test Drive (download em PDF)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Recursos adicionais de gerenciamento de chumbo
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de líderes um pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Lembre-se **de salvar** antes de passar para a próxima seção.

## <a name="properties"></a>Propriedades

A guia **Propriedades** pede que você defina as categorias e indústrias usadas para agrupar sua oferta nos marketplaces, os contratos legais que suportam sua oferta e sua versão do aplicativo.

Selecione **Salvar** depois de concluir esses campos.

### <a name="category"></a>Categoria

Selecione um mínimo de uma (1) e um máximo de três (3) categorias usadas para agrupar sua oferta nas áreas de pesquisa de mercado apropriadas. Descubra como sua oferta suporta essas categorias na descrição da oferta.

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão do aplicativo

Este campo é opcional e usado no mercado AppSource para identificar o número da versão da sua oferta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

A Microsoft fornece um modelo de Contrato Padrão.

- **Usar o Contrato Padrão para o mercado comercial da Microsoft?**

Para simplificar o processo de aquisição para os clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft para ajudar a facilitar as transações no mercado. Em vez de criar termos e condições personalizados, os editores de marketplace comercial podem optar por oferecer seu software sob o Contrato Padrão, que os clientes só precisam vetar e aceitar uma vez. O Contrato Padrão pode https://go.microsoft.com/fwlink/?linkid=2041178ser encontrado aqui: .

Você pode selecionar para usar o Contrato Padrão em vez de fornecer seus próprios termos e condições personalizados selecionando a caixa de seleção "Use o Contrato Padrão para o mercado comercial".

![Usando a caixa de seleção contrato padrão](./media/use-standard-contract.png)

> [!NOTE]
> Uma vez que você publica uma oferta usando o contrato Standard para o mercado comercial da Microsoft, você não pode usar seus próprios termos e condições personalizados. É um cenário "ou". Ou você oferece sua solução sob o Contrato Padrão **ou** seus próprios termos e condições. Se você quiser modificar os termos do Contrato Padrão, você pode fazê-lo através de Alterações de Contrato Padrão.

#### <a name="standard-contract-amendments"></a>Alterações de contrato padrão

As Alterações de Contrato Padrão permitem que os editores selecionem os termos do Contrato Padrão para simplificar e personalizar os termos de seu produto ou negócio. Os clientes só precisam rever as alterações do contrato, se já revisaram e aceitaram o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para editores de mercado comercial:

- Emendas Universais: Essas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. Alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e da alteração antes de poderem usar sua oferta.
- Alterações Personalizadas: Essas alterações são alterações especiais no Contrato Padrão que são direcionadas a clientes específicos apenas através de IDs de inquilinos do Azure. Os editores podem escolher o inquilino que querem atingir. Somente os clientes do inquilino serão apresentados com os termos de alteração aduaneira no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem usar sua oferta.

>[!NOTE]
> Esses dois tipos de alterações se acumulam em cima um do outro. Os clientes alvo de alterações personalizadas também receberão a alteração universal do Contrato Padrão durante a compra.

**Termos de alteração universais ao Contrato Padrão para o mercado comercial da Microsoft**: Insira os termos de alteração universal nesta caixa. Você pode fornecer uma única emenda universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos aos clientes no Portal AppSource, Azure Marketplace e/ou Azure durante o fluxo de descoberta e compra.

**Termos de alteração personalizados para o Contrato Padrão para o mercado comercial da Microsoft**: Inicie selecionando adicionar termos de alteração **personalizados**. Você pode fornecer até 10 termos de alteração personalizados por oferta.

- **Termos de alteração personalizados**: Insira seus termos de alteração personalizados na caixa de termos de alteração personalizada. Você pode inserir um número ilimitado de caracteres nesta caixa. Somente os clientes das IDs de inquilino especificadas para estes termos personalizados serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta no portal Azure.  
- **IDs de inquilino (obrigatório):** Cada alteração personalizada pode ser direcionada para até 20 IDs de inquilinos. Se você adicionar uma alteração personalizada, você deve fornecer pelo menos um id inquilino. A identificação do inquilino identifica seu cliente no Azure. Você pode pedir ao seu cliente este ID e eles podem encontrá-lo navegando para portal.azure.com > Azure Active Directory > Properties. O valor do ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID de inquilino da organização do seu cliente usando o url do nome de domínio no [qual é o meu ID de inquilino do Microsoft Azure e do Office 365?](https://www.whatismytenantid.com)
- **Descrição** (opcional): Opcionalmente, forneça uma descrição amigável para o ID do inquilino que ajuda você a identificar o cliente que você está mirando com a alteração.

#### <a name="terms-and-conditions"></a>Termos e condições

Se você quiser fornecer seus próprios termos e condições personalizados, você pode optar por inseri-los no campo termos e condições. Você pode inserir até 10.000 caracteres de texto neste campo. Se seus termos e condições exigirem uma descrição mais longa, digite um único link de URL neste campo onde seus termos e condições podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes são obrigados a aceitar estes termos antes de tentar sua oferta.

Lembre-se **de salvar** antes de passar para a próxima seção.

## <a name="offer-listing"></a>Listagem de ofertas

A guia de listagem oferta exibe os idiomas (e mercados) onde sua oferta está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página exibe o status da listagem específica do idioma e a data/hora em que foi adicionada. Você precisará definir os detalhes do marketplace (nome de oferta, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

> [!NOTE]
> O conteúdo de listagem de ofertas (como descrição de oferta, documentos, capturas de tela, termos de uso e política de privacidade) não é necessário para estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]." Também é aceitável fornecer uma *URL de Link útil* para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem de Oferta.

### <a name="offer-listings"></a>Listagens de oferta

Forneça detalhes a serem exibidos no mercado, incluindo descrições de sua oferta e ativos de marketing.

- **Nome** (obrigatório): O nome definido aqui aparecerá como o título da sua listagem de oferta no mercado(s) que você escolheu. O nome é pré-preenchido com base na sua entrada anterior **da nova oferta.** O nome pode ser registrado. Ele não pode conter emojis (a menos que sejam símbolos de marca e direitos autorais) e deve ser limitado a 50 caracteres.
- **Resumo** (obrigatório): Forneça uma breve descrição de sua oferta a ser usada nos resultados de pesquisa de listagem de mercado. Até 100 caracteres de texto podem ser inseridos neste campo.
- **Descrição** (necessária): Forneça uma descrição de sua oferta a ser exibida na visão geral da listagem de marketplace. Considere incluir uma proposta de valor, benefícios-chave, qualquer categoria ou associações do setor, oportunidades de compra no aplicativo, quaisquer divulgações necessárias e um link para saber mais.
Até 3.000 caracteres de texto podem ser inseridos neste campo, incluindo marcação. Para obter mais dicas, consulte o artigo [Escreva uma ótima descrição do aplicativo](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Pesquisar palavras-chave**: Digite até três palavras-chave de pesquisa que os clientes podem usar para encontrar sua oferta no marketplace(s).
- **Recebendo instruções** de início (necessário): Explique como configurar e comece a usar seu aplicativo para clientes em potencial.  Este quickstart pode conter links para documentação on-line mais detalhada. Até 3.000 caracteres de texto podem ser inseridos neste campo.

#### <a name="description"></a>**Descrição**

Este campo é obrigatório. Itens a serem **instidados**na Descrição:

* Descreva claramente a proposição de valor da sua oferta nas primeiras frases de sua descrição.  
* Tenha em mente que as primeiras frases podem ser exibidas nos resultados da pesquisa.  
* Não dependa de recursos e em funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você oferece.  
* Use o vocabulário específico do setor ou palavras com base no benefício tanto quanto possível.

Os componentes principais da sua proposta de valor devem incluir informações sobre:

* Descrição do produto.
* Tipo de usuário que se beneficia do produto.
* As necessidades do cliente ou dor que o produto aborda.

Para tornar sua oferta **Descrição** mais atraente, use o rico editor de texto para formatar sua descrição.

![Usando o rico editor de texto](./media/text-editor2.png)

Use as seguintes instruções para usar o editor de texto rico:

- Para alterar o formato do seu conteúdo, destaque o texto que deseja formatar e selecionar um estilo de texto, conforme mostrado abaixo:

     ![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com uma numeração ou numerada ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo ao texto, use as opções abaixo:

     ![Usando o rico editor de texto para recusar](./media/text-editor5.png)

#### <a name="links"></a>Links

- **Política de Privacidade** (necessária): Link para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida
- **Materiais de marketing do programa CSP** (opcional): Forneça um link para materiais de marketing se você optar por estender sua oferta ao programa [Cloud Solution Provider (CSP).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) A CSP estende sua oferta a uma gama mais ampla de clientes qualificados, permitindo que os parceiros CSP empacotem, comercializem e revendam sua oferta. Esses revendedores precisarão ter acesso a materiais para comercializar sua oferta. Para obter mais informações, consulte [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Links úteis** (opcional): Documentos on-line complementares opcionais sobre seu aplicativo ou serviços relacionados listados fornecendo **um Título** e **URL**. Adicione links úteis adicionais clicando **+ Adicione uma URL**.

#### <a name="contact-information"></a>Informações de contato

- **Contatos**: Para cada contato com o cliente, forneça um **nome de**funcionário, número **de telefone**e endereço **de e-mail.**  (Estes *não serão* exibidos publicamente). Uma **URL de suporte** também é necessária para o grupo de contato de **suporte.**  (Essas informações *serão* exibidas publicamente).

**Contato de suporte** (necessário): Para perguntas gerais de suporte.

**Contato de engenharia** (obrigatório): Para questões técnicas.

**Contato do Gerenciador de Canais** (obrigatório): Para perguntas de revendedor relacionadas ao programa CSP.

#### <a name="files-and-images"></a>Arquivos e Imagens

- **Documentos** (obrigatório): Adicione documentos de marketing relacionados à sua oferta, em formato PDF, fornecendo um mínimo (1) e máximo de três (3) documentos por oferta.
- **Imagens** (opcional): Existem vários lugares onde as imagens do logotipo da sua oferta podem aparecer em todo o mercado(s), exigindo os seguintes tamanhos -- Pequeno: 48 x 48 pixels _(obrigatório),_ Médio: 90 x 90 pixels _(obrigatório)_, Grande: 216 x 216 pixels _(necessário),_ Wide: 255 x 115 pixels, e Hero: 815 x 290 pixels. Todas as imagens devem estar dentro. Formato PNG.
- **Capturas de tela** (necessárias): Adicione capturas de tela demonstrando sua oferta. Um máximo de cinco (5) capturas de tela podem ser adicionados e devem ser dimensionados em 1280 x 720 pixels. Todas as imagens devem estar dentro. Formato PNG.
- **Vídeos** (opcional): Adicione links para vídeos que demonstrem sua oferta. É possível usar links para vídeos do YouTube e/ou do Vimeo, que são mostrados juntamente com sua oferta aos clientes. Você também precisará inserir uma imagem em miniatura do vídeo, dimensionada para 1280 x 720 pixels no formato PNG. Você pode exibir um máximo de quatro vídeos por oferta.

Lembre-se **de salvar** antes de passar para a próxima seção.

>[!Note]
>Se você tiver um problema de upload de arquivos, https://upload.xboxlive.com certifique-se de que sua rede local não bloqueie o serviço usado pelo Partner Center.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

- [Melhores práticas para listagens de ofertas de marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Visualização

A guia **Visualização** permite definir uma audiência de **visualização** limitada para lançar sua oferta antes de publicar sua oferta ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar sua oferta no Preview, selecione **Ir ao vivo** para que sua oferta possa ser publicada ao vivo para o público do mercado.

- **Defina uma audiência de visualização: adicione um único e-mail de conta AAD/MSA por linha, juntamente com uma descrição opcional.**

Adicione até 10 endereços de e-mail manualmente, ou 20 se carregar um arquivo CSV, para contas existentes da Microsoft Account (MSA) ou do Azure Active Directory para ajudar a validar sua oferta antes de publicar ao vivo. Ao adicionar essas contas, você está definindo um público que terá acesso de pré-visualização à sua oferta antes de ser publicada no mercado(s). Se sua oferta já estiver ao vivo, você ainda pode definir uma audiência de pré-visualização para testar quaisquer alterações ou atualizações à sua oferta.

> [!NOTE]
> O público de pré-visualização difere de uma audiência privada. Uma audiência de pré-visualização pode ter acesso à sua oferta _antes_ de ser publicada ao vivo nos marketplaces. Você também pode optar por criar um plano e disponibilizá-lo apenas para um público privado. Na guia de listagem de **planos,** você pode definir um público privado com a caixa de seleção **de plano privado.** Em seguida, você pode definir um público privado de até 20.000 clientes usando IDs de inquilino szure.

## <a name="technical-configuration"></a>Configuração técnica

A guia **de configuração técnica** define os detalhes técnicos (caminho de URL, webhook, ID do inquilino e ID do aplicativo) usados para se conectar à sua oferta. Essa conexão nos permite prover sua oferta para o cliente final se eles optarem por adquiri-la. Os diagramas que descrevem o uso dos campos coletados estão disponíveis na documentação para [APIs de cumprimento do SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL de página de deinicialização** (obrigatório): Defina a URL do site em que os clientes pousarão após adquirir sua oferta no mercado. Esta URL será o ponto final que recebe um token quando um cliente é roteado para a página. Esse token pode ser trocado por detalhes de provisionamento usando resolve nas APIs de cumprimento. Esses detalhes e quaisquer outros que você coletar podem ser usados como parte de uma página web interativa para o cliente construída em sua experiência para concluir o registro e ativar sua compra.

- **Webhook de conexão** (obrigatório): Para todos os eventos assíncronos que a Microsoft precisa enviar para você em nome do cliente (exemplo: A assinatura SaaS ficou inválida), exigimos que você forneça um webhook de conexão. Se você ainda não tem um sistema de webhook em vigor, a configuração mais simples é ter um aplicativo HTTP Endpoint Logic que\/irá ouvir quaisquer eventos que estão sendo postados nele e, em seguida, manuseá-los adequadamente (por exemplo, https: /prod-1westus.logic.azure.com:443/work). Para saber mais, confira [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID de inquilino Azure AD** (obrigatório): Dentro do portal Azure, exigimos que você [crie um aplicativo Azure Active Directory (AD) para](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) que possamos validar a conexão entre nossos dois serviços por trás de uma comunicação autenticada. Para encontrar o ID do [inquilino,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)vá para o seu Diretório Ativo do Azure e selecione **Propriedades,** em seguida, procure o número **de ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID do aplicativo Azure AD** (obrigatório): Você também precisa do seu [ID de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e de uma chave de autenticação. Para obter esses valores, vá para o seu Diretório Ativo do Azure e selecione **inscrições de aplicativos**e procure o número de **ID** do aplicativo listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para encontrar a chave de autenticação, vá para **Configurações** e selecione **Chaves**. Você precisará fornecer uma descrição e duração e, em seguida, será fornecido um valor numér.

>[!Note]
>O ID do aplicativo Azure está associado ao Seu ID do editor, portanto, certifique-se de que o mesmo ID de aplicativo seja usado em todas as suas ofertas.

## <a name="plan-overview"></a>Visão geral do plano

A guia **Visão geral do Plano** permite que você forneça uma variedade de opções de plano dentro da mesma oferta. Esses planos (às vezes chamados de SKUs) podem diferir em termos de versão, monetização ou níveis de serviço. Você deve montar pelo menos um plano para vender sua oferta no mercado.

Uma vez criado, você verá seus nomes de planos, IDs, modelos de preços, disponibilidade (Público ou Privado), status atual de publicação e quaisquer ações disponíveis.

**As ações** disponíveis na **visão geral** do Plano variam dependendo do status atual do seu plano e podem incluir:

- Se o status do plano for **Rascunho** - Exclua o rascunho
- Se o status do plano for **Ao Vivo** - Pare de vender plano ou Sincronie audiência privada

**Criar um novo plano** (mínimo de um plano para quem optar por vender através da Microsoft)

- **ID do plano:** Crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes nos modelos de URL do produto e do Azure Resource Manager (se aplicável). Use apenas caracteres minúsculos e alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este id plano. O ID não pode ser modificado após a seleção de criar.
- **Nome do plano:** Os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, Nome de oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Listagem de planos

A guia **de listagem de planos** exibe os idiomas (e mercados) onde seu plano está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página exibe o status da listagem específica do idioma e a data/hora em que foi adicionada. Você precisará definir os detalhes do marketplace (nome de oferta, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

#### <a name="plan-listing-details"></a>Detalhes da listagem do plano

A seleção de um dos idiomas do plano exibirá as informações de listagem do **plano,** incluindo **Nome** e **Descrição.**

- **Nome**: Pré-preenchido com base na sua pré-visualização Nova entrada **do plano** e aparecerá como o título do "plano de software" da sua oferta exibido no mercado.
- **Descrição:** Esta descrição é uma oportunidade para explicar o que torna este plano de software único e quaisquer diferenças de outros planos de software dentro de sua oferta. Pode conter até 500 caracteres.

Selecione **Salvar** depois de concluir esses campos.

#### <a name="plan-pricing-and-availability"></a>Preços e disponibilidade de planos

A guia **De Preços e Disponibilidade** permite configurar os mercados em que este plano estará disponível, o modelo de monetização desejado, preço e prazo de faturamento. Além disso, você pode indicar se tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

##### <a name="enabling-free-trials"></a>Habilitando testes gratuitos

As ofertas da SaaS através do mercado comercial permitem que você forneça uma avaliação gratuita de um mês ao vender através da Microsoft. Para todos os modelos e termos de faturamento, exceto planos medidos, os testes gratuitos são suportados. Essa opção permite que os clientes tenham uma baixa barreira para a entrada através de um mês de acesso gratuito.  Se você optar por habilitar uma avaliação gratuita para planos dentro de sua oferta, o cliente não poderá converter-se para uma assinatura paga antes do final do período inicial de um mês.  Durante esse tempo, os clientes que comprarem sua oferta podem experimentar qualquer um dos planos suportados que tenham a avaliação gratuita ativada e converter entre eles.  A conversão para uma assinatura paga é feita automaticamente no final do prazo.

>[!Note]
>Se o cliente optar por converter para um plano sem testes gratuitos, a conversão acontecerá, mas a avaliação gratuita será perdida imediatamente.  Além disso, uma vez que um cliente começa a pagar por um plano, ele não pode mais obter avaliação gratuita na mesma assinatura novamente, mesmo se ele se converter para um SKU que suporta testes gratuitos.

A capacidade de configurar uma avaliação gratuita está disponível para cada plano em sua oferta. Navegue até os preços e disponibilidade para cada oferta e marque a caixa para permitir um teste de um mês.

![Caixa de seleção de avaliação gratuita de um mês](./media/free-trial-enable.png)

>[!Note]
>Uma vez que sua oferta transacível tenha sido publicada com um teste gratuito, ela não pode ser desativada para esse plano. Certifique-se de que esta configuração está correta para a primeira publicação para evitar ter que recriar o plano.

Para obter informações sobre assinaturas de clientes que atualmente participam `isFreeTrial`de uma avaliação gratuita, use a nova propriedade da API, que será marcada como verdadeira ou falsa. Para obter mais informações, consulte a [API saas get subscription](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Testes gratuitos não são suportados para planos que aproveitam o serviço de medição de marketplace.

#### <a name="markets"></a>Mercados

- **Editar mercados** (opcional)

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de seleção para qualquer local de mercado onde você gostaria de disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países "Remetidos de impostos", nos quais a Microsoft reemite vendas e uso de impostos em seu nome, são incluídos para ajudar.

Se você já definiu os preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço para o novo mercado será calculado de acordo com as taxas de câmbio atuais. Revise o preço de cada mercado antes de publicar. Os preços podem ser revisados usando o link "Preços de exportação (xlsx)" após salvar suas alterações.

#### <a name="pricing"></a>Preços

- **Modelo de preços**: Taxa fixa ou assento baseado

**Taxa fixa:** Habilite o acesso à sua oferta com um único preço fixo mensal ou anual. Isso às vezes é referido como preço baseado no site. Com este modelo de preços, você pode definir opcionalmente planos medidos que usam a API de serviço de medição de marketplace para cobrar dos clientes de acordo com unidades fora do padrão.  Para obter mais informações sobre faturamento medido, consulte [o faturamento medido usando o serviço de medição de marketplace](./saas-metered-billing.md).

**Por usuário:** Habilite o acesso à sua oferta com o preço baseado no número de usuários que acessam a oferta ou ocupam assentos. Este modelo baseado no usuário permite definir o número mínimo e máximo de usuários permitido sustais com base no preço. Dessa forma, diferentes pontos de preço podem ser configurados com base no número de usuários, configurando vários planos.  Esses campos são opcionais. Se não for selecionado, o número de usuários será interpretado como não tendo um limite (min de 1 e máximo de tantos quanto o sistema pode suportar). Esses campos podem ser editados como parte de uma atualização do seu plano.

Uma vez publicado, a escolha do modelo de preços de faturamento não pode ser alterada. Além disso, todos os planos para a mesma oferta devem compartilhar o mesmo modelo de preços.

- **Termo de faturamento**: Mensal ou Anual

Selecione a frequência que os clientes devem pagar o preço listado. Pelo menos um preço mensal ou anual deve ser fornecido, ou ambas as opções podem ser disponibilizadas aos clientes.

- **Preço**: USD por mês ou USD por ano

Os preços definidos em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. Você é responsável por validar este preço e possuir essas configurações.
*\*Primeiro, você deve salvar suas alterações de preços para permitir a exportação de dados de preços.*

Revise seus preços cuidadosamente antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano:

- Uma vez que um plano é publicado, o modelo de preços não pode ser alterado.
- Uma vez que um termo de faturamento é publicado para um plano, ele não pode ser removido mais tarde.
- Uma vez que um preço para um mercado em seu plano é publicado, ele não pode ser alterado mais tarde.

### <a name="plan-audience"></a>Audiência do Plano

Você tem a opção de configurar cada plano para ser visível para todos ou para apenas um público específico de sua escolha. Você pode atribuir a adesão a este público restrito usando IDs de inquilino azure AD.

#### <a name="privacy"></a>Privacidade

- **Este é um plano privado** (caixa de seleção opcional)

Verifique esta caixa para tornar seu plano privado e visível apenas para o público restrito de sua escolha. Uma vez publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Uma vez que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

- **Audiência restrita (IDs de inquilino)**

Atribua ao público que terá acesso a este plano privado. O acesso é atribuído usando IDs de inquilino com a opção de incluir uma descrição de cada ID de inquilino atribuído. Um máximo de 10 IDs de inquilino podem ser adicionados, ou 20.000 iDs de inquilino de clientes se importar um arquivo de planilha .csv.

Um inquilino é uma representação de uma organização, com um ID representado como um GUID (Globally Unique Identifier, um número inteiro de 128 bits usado para identificar recursos). É uma instância dedicada do Azure AD que uma organização ou desenvolvedor de aplicativos recebe quando a organização ou desenvolvedor de aplicativos cria um relacionamento com a Microsoft, por exemplo, ao se inscrever no Azure, Microsoft Intune ou Microsoft 365. Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure. Para verificar o locatário, entre no portal do Azure com a conta que você deseja usar para gerenciar seu aplicativo. Se houver um locatário, você será conectado a ele automaticamente e verá o nome do locatário diretamente abaixo do nome da conta. Passe o mouse sobre o nome da conta no canto superior direito do portal do Azure para ver seu nome, email, diretório e ID do locatário (um GUID) e seu domínio. Se sua conta estiver associada a vários locatários, você pode selecionar o nome da sua conta para abrir um menu no qual você pode alternar entre locatários. Cada locatário tem sua própria ID exclusiva. Você também pode procurar o ID de inquilino da [https://www.whatismytenantid.com](https://www.whatismytenantid.com)sua organização usando uma URL de nome de domínio em: .

Embora o SaaS ofereça ids de inquilino para definir um público privado, outros tipos de oferta podem usar IDs de assinatura do Azure (que também são representados como GUIDs).

> [!NOTE]
> O público privado (ou público restrito) difere de uma audiência de pré-visualização. Na guia **[Visualização,](#preview)** você pode definir uma audiência de visualização. Um público de pré-visualização pode ter acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação de público privado só se aplique a um plano específico, o público de pré-visualização pode visualizar todos os planos (privados ou não), mas apenas durante o período de pré-visualização limitado enquanto o plano é testado e validado.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Lista de exemplos de planos dentro de uma oferta de marketplace

![Exemplo de listagem de plano de mercado com notas](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Audiência de revendedor do Provedor de Soluções em Nuvem (CSP)

A eleição para disponibilizar sua oferta no programa CSP permite que os provedores de soluções em nuvem vendam seu produto como parte de uma solução empacotada para seus clientes. Para obter mais informações, consulte [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publicar

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do portal. Você será redirecionado para a **página Revisar e publicar.**

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualizar

Se esta é a sua primeira vez publicando esta oferta, você pode:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não iniciado* - significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* - significa que a seção tem erros que precisam ser corrigidos ou requer mais informações a serem fornecidas. Você precisará voltar para a seção e atualizá-la.
    - *Completo* - significa que a seção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em estado completo antes de você poder enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de quaisquer notas suplementares úteis para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail para que você saiba quando uma versão de pré-visualização da oferta estiver disponível para você revisar e aprovar. Você deve retornar ao Partner Center e selecionar **go-live** para a oferta de publicar sua oferta ao público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
