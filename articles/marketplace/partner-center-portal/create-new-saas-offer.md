---
title: Criar uma oferta de SaaS, Azure Marketplace e Microsoft AppSource
description: Como criar uma oferta de SaaS (software como serviço) para listagem ou venda no Microsoft AppSource, no Azure Marketplace ou no programa CSP (provedor de soluções na nuvem) usando o programa Microsoft Commercial Marketplace no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 2c5394dce503a6fa00e2a3e6ff73a683d3d2e76f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012086"
---
# <a name="create-a-saas-offer"></a>Criar uma oferta de SaaS

Para começar a criar ofertas de SaaS (software como serviço) no marketplace comercial, você precisa primeiro [criar uma conta do Partner Center](./create-account.md) e abra o [painel Marketplace Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com a guia **Visão geral** selecionada.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Software como um Serviço**.

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-saas.png)

> [!NOTE]
> Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em frentes de loja após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1** aqui, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O Alias da oferta não pode ser alterado depois que você seleciona **Criar**.

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Visão geral da oferta

O **status da publicação** exibe uma representação visual das etapas necessárias para publicar essa oferta e quanto tempo cada etapa levará para ser concluída. Os ícones de etapa de publicação incompletos ficarão esmaecidos.

O menu **Visão geral da oferta** contém uma lista de links para executar operações nessa oferta. Essa lista de operações será alterada com base na seleção que você fizer para sua oferta.  

- Se a oferta for um rascunho – Excluir rascunho
- Se a oferta estiver ativa – Parar oferta de venda
- Se a oferta estiver em versão prévia – Ativar
- Se você ainda não concluiu a saída do Publicador, cancele a publicação

## <a name="offer-setup"></a>Configuração da oferta

Esta página solicita as informações a seguir.

- **Você gostaria de vender pela Microsoft?** (Sim/Não)
    - **Sim**, eu gostaria de vender pela Microsoft e ter transações de host da Microsoft em meu nome
    - **Não**, prefiro apenas listar minha oferta por meio dos Marketplaces e processar as transações de maneira independente.

### <a name="sell-through-microsoft"></a>Vender por meio da Microsoft

A venda pela Microsoft fornece melhor descoberta de clientes e aquisição, permite que a Microsoft hospede transações de Marketplace em seu nome e aproveita as funcionalidades de comércio da Microsoft disponíveis globalmente.

#### <a name="saas-offer-requirements"></a>Requisitos da oferta de SaaS

Para listar ofertas de SaaS (software como serviço) com o Marketplace Comercial no Partner Center, os seguintes critérios precisam ser atendidos:

- Sua oferta precisa usar o [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) para gerenciamento de identidades e autenticação.
- Sua oferta precisa usar [APIs de cumprimento de SaaS](pc-saas-fulfillment-api-v2.md) para se integrar ao Azure Marketplace.

#### <a name="saas-pricing-and-billing-options"></a>Opções de preços e cobrança de SaaS

Com as soluções de SaaS em execução na assinatura do Azure do editor, os valores de licença pagos pelos clientes incluem o custo da infraestrutura na qual o software é implantado. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para você, o parceiro. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Os editores devem incluir os valores de uso da infraestrutura do Azure em nos preços de licença de software praticados por eles.

O SaaS dá suporte à cobrança mensal ou anual com base em valores fixos por usuário ou cobranças de consumo usando o serviço de cobrança limitado. O marketplace comercial da Microsoft opera em um modelo de agência, no qual os editores definem preços, a Microsoft cobra os clientes e paga uma receita ao editor, retendo um valor de agenciamento.

Esta é uma análise de exemplo de custos e pagamentos para demonstrar o modelo de agência (todos os preços listados são apenas para fins de exemplo e não se destinam a refletir os custos reais):

|**Custo de sua licença**|**US$ 100 por mês**|
|:---|:---|
|Custo de uso do Azure (D1/1-Núcleo)|Cobrados diretamente para o publicador, não o cliente|
|O cliente é cobrado pela Microsoft|US$ 100,00 por mês (o editor precisa contabilizar os custos incorridos ou de infraestrutura de passagem no valor da licença)|

|**Faturas da Microsoft**|**US$ 100 por mês**|
|:---|:---|
|A Microsoft paga para você 80% do seu custo de licença <br>**Para aplicativos SaaS qualificados, a Microsoft paga 90% do seu custo de licença*|US $80,00 por mês <br>*$US* 90,00 por mês*|

- Neste exemplo, a Microsoft cobra US$ 100,00 do cliente pela sua licença de software e paga US$ 80,00 ao editor.

> [!NOTE]
> **Redução da taxa de serviço do Marketplace** – para determinadas ofertas de SaaS que você publicou no mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%. Para que suas ofertas sejam qualificadas, suas ofertas devem ter sido designadas pela Microsoft como o IP do Azure co-vender incentivados. A qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do fim de cada mês civil para receber a taxa de serviço do Marketplace reduzida para o mês. A taxa reduzida de serviço do Marketplace também se aplica ao IP do Azure covenda de VMs incentivados, aplicativos gerenciados e qualquer outra oferta de IaaS paga e qualificada disponibilizada por meio do Marketplace comercial.

### <a name="list-through-microsoft"></a>Listar por meio da Microsoft

Promova sua empresa com a Microsoft criando uma listagem do Marketplace. Selecionar a opção de somente listar sua oferta e não realizar transações por meio da Microsoft significa que a Microsoft não participará diretamente das transações de licença de software. Não há nenhuma taxa de transação associada e o editor mantém 100% dos valores de licenciamento de software coletados do cliente. No entanto, os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: cumprimento de pedidos, medição, faturamento, faturamento, pagamento e cobrança.

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>Obtenha agora (gratuitamente)

Liste sua oferta aos clientes gratuitamente fornecendo um endereço válido (começando com *http* ou *https*) em que eles podem obter uma avaliação por meio da [autenticação com um clique usando Azure Active Directory (AD do Azure)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Por exemplo, `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita fornecendo um endereço válido (começando com *http* ou *https*), em que eles podem obter uma avaliação por meio de [autenticação com um clique usando Azure Active Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Por exemplo, `https://contoso.com/trial/saas-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure AD para automatizar a criação da conta em seu aplicativo. A MSA (autenticação com contas Microsoft) usando esse token não é um procedimento compatível.

#### <a name="contact-me"></a>Entrar em contato comigo

Colete informações de contato do cliente conectando seu sistema de CRM (gerenciamento de relacionamento com o cliente). Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do Marketplace onde encontraram sua oferta, serão enviados para o sistema de CRM que você configurou. Para obter mais informações sobre como configurar o CRM, confira [Clientes potenciais](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Exemplo de listagem de oferta do Marketplace

<!-- ![Example marketplace offer listing with notes](./media/marketplace-offer.svg) -->

Aqui está um exemplo de como as informações de oferta são exibidas no Microsoft AppSource:

:::image type="content" source="media/example-appsource-saas.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo grande
2. Categorias
3. Setores
4. Endereço de suporte (link)
5. Termos de uso
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Descrição
10. Capturas de tela/vídeos
11. Documentos

<br>Aqui está um exemplo de como as informações de oferta são exibidas no portal do Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ilustra como essa oferta aparece na portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Título
2. Descrição
3. Links úteis
4. Capturas de tela

## <a name="enable-a-test-drive"></a>Habilitar um test drive

Um test drive é uma ótima maneira de demonstrar sua oferta para clientes potenciais oferecendo a eles a opção de experimentar antes de comprar, o que resulta no aumento da conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives](../what-is-test-drive.md).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive**. Para remover o test drive de sua oferta, desmarque essa caixa de seleção.

Para obter informações adicionais, confira [Faça o test drive de sua oferta no marketplace comercial](test-drive.md).

### <a name="test-drive-resources"></a>Recursos de test drive

- [O que é um test drive?](../what-is-test-drive.md)
- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Verifique se o bloqueador de pop-ups está desativado)

### <a name="customer-leads"></a>Clientes potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Recursos adicionais de gerenciamento de clientes potenciais
- [Perguntas frequentes sobre gerenciamento de leads](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Erros comuns de configuração de leads](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [One Pager Visão geral do gerenciamento de leads](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Selecione **Salvar rascunho** para continuar.

## <a name="properties"></a>Propriedades

Essa página solicita que você defina as categorias e os setores usados para agrupar sua oferta nos Marketplaces, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Sua oferta será publicada no AppSource ou no Azure Marketplace, dependendo dos recursos de transação associados à sua oferta e à seleção de categoria. Consulte comparando [Microsoft AppSource e o Azure Marketplace](../comparing-appsource-azure-marketplace.md) para obter detalhes. Selecione categorias e subcategorias que melhor se alinhem com sua oferta e seu público-alvo. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias aplicáveis a cada vitrine nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Setores

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

A seleção do setor aplica-se apenas às ofertas publicadas no AppSource.

### <a name="app-version"></a>Versão do aplicativo

Esse campo é opcional e usado no Marketplace do AppSource para identificar o número de versão da sua oferta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Standard para o marketplace comercial da Microsoft

A Microsoft fornece um modelo de Contrato Standard.

- **Usar o Contrato Standard para o marketplace comercial da Microsoft?**

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um Contrato Standard para o marketplace comercial a fim de facilitar as transações no Marketplace. Em vez de criar termos e condições personalizados, os editores do marketplace comercial podem optar por oferecer o software deles sob o Contrato Standard, que os clientes precisam analisar e aceitar apenas uma vez. O Contrato Standard pode ser encontrado em https://go.microsoft.com/fwlink/?linkid=2041178.

Você pode optar por usar o Contrato Standard em vez de fornecer seus termos e condições personalizados selecionando a caixa de seleção "Usar o Contrato Standard para o marketplace comercial".

![Usar a caixa de seleção de Contrato Standard](./media/use-standard-contract.png)

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Standard para o marketplace comercial da Microsoft, você não pode usar seus termos e condições personalizados. Esse é um cenário com duas possibilidades mutuamente exclusivas. Você pode oferecer uma solução sob o Contrato Standard **ou** os seus termos e condições. Se você quiser modificar os termos do Contrato Standard, poderá fazer isso por meio de aditamento ao Contrato Standard.

#### <a name="standard-contract-amendments"></a>Aditamentos do Contrato Standard

Os aditamentos do Contrato Standard permitem que os editores selecionem os termos do Contrato Standard para simplificá-los e personalizem os termos para o produto ou negócio deles. Os clientes precisarão examinar os aditamentos do contrato somente se já tiverem examinado e aceitado o Contrato Standard da Microsoft.

Há dois tipos de aditamentos disponíveis para editores do marketplace comercial:

- Aditamentos universais: Esses aditamentos são aplicados universalmente ao Contrato Standard para todos os clientes. Os aditamentos universais são mostrados para todos os clientes da oferta no fluxo de compra. Os clientes precisam aceitar os termos do Contrato Standard e o aditamento para usar sua oferta.
- Aditamentos personalizados: São aditamentos especiais ao Contrato Standard destinados apenas a clientes específicos por meio de IDs de locatário do Azure. Os editores podem escolher o locatário que desejam usar como destino. Somente os clientes do locatário serão apresentados com os termos de aditamento personalizados no fluxo de compra da oferta.  Os clientes precisam aceitar os termos do Contrato Standard e os aditamentos para usar sua oferta.

>[!NOTE]
> Esses dois tipos de aditamentos se sobrepõem. Os clientes aos quais os aditamentos personalizados são direcionados também terão o aditamento universal para o Contrato Standard durante a compra.

**Termos de aditamento universal para o Contrato Standard para o marketplace comercial da Microsoft** – insira os termos de aditamento universal nesta caixa. Você pode fornecer um aditamento universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para os clientes no AppSource, no Azure Marketplace e/ou no portal do Azure durante o fluxo de compra e de descoberta.

**Termos de aditamento personalizados para o Contrato Standard do marketplace comercial da Microsoft** – comece selecionando **Adicionar termos de aditamento personalizados**. Você pode fornecer até dez termos de aditamento personalizados por oferta.

- **Termos de aditamento personalizados** – insira seus termos de aditamento personalizados na caixa de termos de aditamento personalizados. Você pode inserir um número ilimitado de caracteres nesta caixa. Somente os clientes das IDs de locatário que você especificar para esses termos personalizados poderão ver os termos de aditamento personalizados no fluxo de compra da oferta no portal do Azure.  
- **IDs do locatário** (obrigatório) – cada aditamento personalizado pode ser direcionado para até 20 IDs de locatário. Se você adicionar um aditamento personalizado, precisará fornecer pelo menos uma ID de locatário. A ID de locatário identifica seu cliente no Azure. Você pode solicitar que o cliente tenha essa ID e ele pode encontrá-la navegando até portal.azure.com > Azure Active Directory > Propriedades. O valor da ID de diretório é a ID do locatário (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode pesquisar a ID de locatário da organização de seu cliente usando a URL do nome de domínio em [qual é a ID do locatário do Office 365 e do Microsoft Azure?](https://www.whatismytenantid.com)
- **Descrição** (opcional) – forneça opcionalmente uma descrição amigável para a ID do locatário que ajude a identificar o cliente ao qual você está direcionando o aditamento.

#### <a name="terms-and-conditions"></a>Termos e condições

Se você quiser fornecer seus termos e condições personalizados, poderá optar por inseri-los no campo de termos e condições. Você pode inserir até 10.000 caracteres de texto neste campo. Se os termos e condições exigirem uma descrição mais longa, insira nesse campo um link de URL em que os termos e condições possam ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes precisam aceitar esses termos antes de poderem testar a oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Essa página exibe os idiomas (e os mercados) em que sua oferta está disponível, atualmente Inglês (Estados Unidos) é a única localização disponível. Além disso, essa página exibe o status da listagem específica a um idioma e a data/hora em que ela foi adicionada. Você precisará definir detalhes do Marketplace (nome da oferta, descrição, imagens de pesquisa etc.) para cada idioma/mercado.

> [!NOTE]
> O conteúdo de listagem da ofertas (como descrição, documentos, capturas de tela, termos de uso e políticas de privacidade) não precisa estar em inglês, desde que a descrição da oferta comece com a frase "Este aplicativo está disponível apenas em [idioma que não seja inglês]". Também é aceitável fornecer uma *URL de link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem de ofertas.

### <a name="offer-listings"></a>Listagens de oferta

Forneça detalhes a serem exibidos no Marketplace, incluindo descrições de sua oferta e ativos de marketing.

- **Nome** (obrigatório) – o nome definido aqui será exibido como o título da sua listagem de ofertas nos marketplaces que você escolheu. O nome é populado previamente com base na entrada **Nova oferta** anterior. Uma marca comercial pode ser atribuída ao nome. Ele não pode conter emojis (a menos que sejam os símbolos de marca registrada e direitos autorais) e precisam ser limitados a 50 caracteres.
- **Resumo** (obrigatório) – forneça uma descrição breve de sua oferta a ser usada nos resultados da pesquisa de listagens do Marketplace. Até 100 caracteres de texto podem ser inseridos neste campo.
- **Descrição** (obrigatório) – forneça uma descrição da sua oferta a ser exibida na visão geral de listagens do Marketplace. Considere incluir uma proposta de valor, benefícios principais, associações de categoria ou do setor, oportunidades de compra no aplicativo, divulgações necessárias e um link para saber mais. Até 3.000 caracteres de texto podem ser inseridos neste campo, incluindo marcação. Para obter dicas adicionais, confira [Escrever uma ótima descrição do aplicativo](/windows/uwp/publish/write-a-great-app-description).
- **Palavras-chave de pesquisa** – insira até três palavras-chave de pesquisa que os clientes podem usar para localizar sua oferta nos marketplaces.
- **Instruções de introdução** (obrigatório) – explique a clientes potenciais como configurar e começar a usar seu aplicativo.  Este guia de início rápido pode conter links para uma documentação online mais detalhada. Até 3.000 caracteres de texto podem ser inseridos neste campo.

#### <a name="description"></a>Descrição

Esse campo é obrigatório.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Links

- **Política de Privacidade** (obrigatório) – link para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.
- **Materiais de marketing do programa de CSP** (opcional) – forneça um link para materiais de marketing se você optar por estender sua oferta para o programa de [CSP (provedor de soluções na nuvem)](../cloud-solution-providers.md). O CSP amplia sua oferta para uma variedade maior de clientes qualificados, permitindo que os parceiros do CSP agrupem, comercializam e revendam sua oferta. Esses revendedores precisarão de acesso aos materiais para fazer o marketing de sua oferta. Para obter mais informações, confira [Serviços Go-To-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Links úteis** (opcional) – documentos online complementares opcionais sobre seu aplicativo ou serviços relacionados listados fornecendo um **Título** e uma **URL**. Adicione links úteis adicionais clicando em **+ Adicionar uma URL**.

#### <a name="contact-information"></a>Informações de contato

- **Contatos** – para cada contato de cliente, forneça um **Nome**, **Número de telefone** e **Endereço de email** (essas informações *não serão* exibidas publicamente). Uma **URL de Suporte** é necessária para o grupo de **Contato do Suporte** (essa informação *será* exibida publicamente).

    - **Contato do Suporte** (obrigatório) – para perguntas de suporte em geral.
    - **Contato de engenharia** (obrigatório) – para perguntas técnicas.
    - **Contato do gerenciador de canal** (obrigatório) – para perguntas de revendedor relacionadas ao programa de CSP.

#### <a name="files-and-images"></a>Arquivos e imagens

- **Documentos** (obrigatório) – adicione documentos de marketing relacionados para sua oferta, em formato PDF, de pelo menos um e até três documentos por oferta.
- **Imagens** (opcional) – há vários locais em que as imagens de logotipo da sua oferta podem aparecer em todo o Marketplace, exigindo os seguintes tamanhos de pixel no formato PNG:

    - **Pequeno** (48 x 48, obrigatório)
    - **Médio** (90 x 90, obrigatório)
    - **Grande** (216 x 216, obrigatório)
    - **Largo** (255 x 115)

- **Capturas de tela** (obrigatórias) – adicione um máximo de cinco capturas de tela demonstrando sua oferta, dimensionada em 1280 x 720 pixels. Todas as imagens precisam estar no formato .PNG.
- **Vídeos** (opcional) – adicione links para vídeos que demonstrem sua oferta. É possível usar links para vídeos do YouTube e/ou do Vimeo, que são mostrados juntamente com sua oferta aos clientes. Você também precisará inserir uma imagem em miniatura do vídeo, dimensionada para 1280 x 720 pixels em formato PNG. É possível exibir um máximo de quatro vídeos por oferta.

>[!NOTE]
>Se você está enfrentando um problema ao carregar arquivos, verifique se a sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Melhores práticas para listagens de oferta do marketplace](../gtm-offer-listing-best-practices.md)

Selecione **Salvar rascunho** antes de continuar.

## <a name="preview-audience"></a>Público-alvo de versão prévia

Esta página permite que você defina um **público-alvo de versão prévia** limitado para o qual liberar sua oferta antes de publicá-la como ativa para o público-alvo do Marketplace em geral.

> [!IMPORTANT]
> Depois de verificar sua oferta em versão prévia, selecione **Ativar** para que sua oferta possa ser publicada como ativa para um público-alvo que inclua todo o público do Marketplace.

Adicione um email de conta do AAD/MSA por linha, juntamente com uma descrição opcional.

Adicione manualmente até dez endereços de email (ou 20 se estiver carregando um arquivo CSV) para a MSA (conta Microsoft) existente ou contas do Azure Active Directory para ajudar a validar sua oferta antes de publicá-la como ativa. Ao adicionar essas contas, você está definindo um público-alvo que terá permissão para acessar sua oferta em versão prévia antes que ela seja publicada nos marketplaces. Se a oferta já estiver ativa, você ainda poderá definir um público-alvo de versão prévia para testar eventuais alterações ou atualizações à sua oferta.

> [!NOTE]
> O público-alvo de versão prévia difere de um público-alvo privado. Um público-alvo de versão prévia tem permissão para acessar sua oferta _antes_ de ela ser publicada como ativa nos Marketplaces. Você também pode optar por criar um plano e disponibilizá-lo somente para um público-alvo privado. Na guia **listagem do plano**, você pode definir um público-alvo privado com a caixa de seleção **Este é um plano privado**. Em seguida, você pode definir um público-alvo privado de até 20.000 clientes usando as IDs de Locatário do Azure.

Selecione **Salvar rascunho** para continuar.

## <a name="technical-configuration"></a>Configurações técnicas

A guia **configuração técnica** define os detalhes técnicos usados pelo Marketplace para se comunicar com o serviço SaaS. Essa conexão nos permite provisionar sua oferta para o cliente final se ele optar por adquiri-lo e gerenciá-lo. 

>[!Note]
>Você deve implementar a integração com [APIs de preenchimento de SaaS](./pc-saas-fulfillment-api-v2.md) antes de configurar esses detalhes nos detalhes da oferta.

Os diagramas e explicações detalhadas que descrevem o uso dos campos coletados estão disponíveis na documentação para [as APIs](./pc-saas-fulfillment-api-v2.md).

- **URL da página de aterrissagem** (obrigatório) – defina a URL do site de SaaS (por exemplo: `https://contoso.com/signup` ) que os clientes finais verão depois de adquirir sua oferta do Marketplace e disparar o processo de configuração da assinatura de SaaS recém-criada.  Essa URL será chamada com o parâmetro de token de identificação de compra do Marketplace que identifica exclusivamente a compra de SaaS do cliente final específico.  Você deve trocar esse token para os detalhes da assinatura SaaS correspondente usando a API de [resolução](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) .  Esses detalhes e quaisquer outros que você deseja coletar devem ser usados como parte de uma página da Web interativa para o cliente, criada em sua experiência para concluir o registro do cliente final e ativar sua compra.  Nesta página, o usuário deve se inscrever através da autenticação com um clique usando Azure Active Directory (Azure AD). <br> <br> Esta URL com o parâmetro de token de identificação de compra do Marketplace também será chamada quando o cliente final iniciar a experiência SaaS gerenciada no centro de administração portal do Azure ou M365. Você deve lidar com ambos os fluxos, quando o token é fornecido pela primeira vez após a compra de novos clientes e quando ele é fornecido para o cliente existente que gerencia seu SaaS. <br> <br> A página de aterrissagem configurada aqui deve estar em execução 24/7. Essa é a única maneira que você será notificado sobre novas compras de suas ofertas de SaaS feitas no Marketplace ou solicitações de configuração de uma assinatura ativa de uma oferta.

- **Webhook de conexão** (obrigatório) – para todos os eventos assíncronos que a Microsoft precisa enviar para você (por exemplo, a assinatura de SaaS foi cancelada), exigimos que você forneça uma URL de webhook de conexão. Chamaremos essa URL para notificá-lo sobre o evento. <br> <br> O webhook que você fornece deve estar em execução em 24/7, pois essa é a única maneira de você ser notificado sobre atualizações sobre as assinaturas de SaaS de seus clientes adquiridas por meio do Marketplace. Se você ainda não tiver um sistema de webhook em vigor, a configuração mais simples é ter um aplicativo lógico de ponto de extremidade HTTP que escutará todos os eventos postados nele e, em seguida, tratá-los adequadamente (por exemplo, `https://prod-1westus.logic.azure.com:443/work` ). Para saber mais, confira [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos](../../logic-apps/logic-apps-http-endpoint.md).

- **ID de locatário do Azure ad** (obrigatório) – dentro do portal do Azure, exigimos que você [crie um aplicativo Azure Active Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) para que possamos validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para localizar a [ID de locatário](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)), vá para o Azure Active Directory e selecione **Propriedades**, em seguida, procure o número de **ID de diretório** listado (como 50c464d3-4930-494c-963c-1e951d15360e).

- **ID do aplicativo do Azure ad** (obrigatório) – você também precisa da [ID do aplicativo](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Para obter seu valor, vá para o Azure Active Directory e selecione **registros de aplicativo**, em seguida, procure o número de **ID do aplicativo** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

>[!Note]
>A ID do aplicativo do Azure AD está associada à sua ID de editor em sua conta do Partner Center. Certifique-se de usar a mesma ID de aplicativo em todas as suas ofertas.

>[!Note]
>Se o Publicador tiver duas ou mais contas diferentes no Partner Center, duas ou mais IDs de aplicativo diferentes do Azure AD devem ser usadas, cada uma para uma das contas. Cada conta de parceiro no Partner Center deve usar a ID de aplicativo do Azure AD exclusiva para todas as ofertas de SaaS que são publicadas por meio dessa conta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Essa página permite que você forneça uma variedade de opções de plano dentro da mesma oferta. Esses planos (anteriormente chamados de SKUs) podem diferir em termos de versão, monetização ou camadas de serviço. Você deve configurar pelo menos um plano para vender sua oferta no Marketplace.

Depois de criado, você verá nomes, IDs, modelos de preços, disponibilidade (pública ou privada), status de publicação atual e ações disponíveis do seu plano.

As **ações** disponíveis no painel **Visão geral do plano** variam de acordo com o status atual do plano e podem incluir:

- Se o status do plano for **Rascunho** – Excluir rascunho
- Se o status do plano for **Ativo** – Interromper o plano de venda ou Sincronizar público-alvo privado

**Criar plano** (mínimo de um plano para aqueles que optam por vender pela Microsoft)

- **ID do plano:** crie uma ID de plano exclusiva para cada plano nesta oferta. A ID ficará visível para os clientes na URL do produto e em modelos do Azure Resource Manager (se aplicável). Use apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. Essa ID de plano pode conter no máximo 50 caracteres. A ID não pode ser modificada após a seleção de "criar".
- **Nome do plano:** os clientes veem esse nome quando estão decidindo qual plano selecionar dentro de sua oferta. Crie um nome da oferta exclusivo para cada plano nessa oferta. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Listagem de planos

Esta página permite que você defina o nome e a descrição do plano. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Nome** – populada previamente com base em sua entrada **Novo plano** da versão prévia e aparecerá como o título do "Plano de software" da sua oferta exibido no Marketplace.
- **Descrição** – essa descrição é uma oportunidade para explicar o que torna este plano de software exclusivo, bem como eventuais diferenças para outros planos de software dentro de sua oferta. Ela pode conter até 500 caracteres.

Selecione **Salvar rascunho** para continuar.

#### <a name="pricing-and-availability"></a>Preços e disponibilidade

Esta página permite que você configure os mercados em que esse plano estará disponível, o modelo de monetização, o preço e o termo de cobrança desejados. Além disso, você pode indicar se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público-alvo privado).

#### <a name="markets-optional"></a>Mercados (opcional)

Cada plano precisa estar disponível em pelo menos um mercado. Selecione **Editar mercados** e marque a caixa de seleção para qualquer localização de mercado em que você deseja disponibilizar esse plano. Esta página inclui uma caixa de pesquisa e uma opção para selecionar [países/regiões de "impostos remetidos"](tax-details-paid-transactions.md), nos quais a Microsoft remete vendas e impostos sobre o uso em seu nome.

Se você já tiver definido preços para seu plano em dólares americanos (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Examine o preço de cada mercado antes de publicar. Veja os preços usando o link "Preços de exportação (xlsx)" depois de salvar as alterações.

Selecione **Salvar** para continuar.

#### <a name="pricing"></a>Preços

##### <a name="pricing-model"></a>Modelo de preços

**Taxa fixa** – habilite o acesso à sua oferta com uma mensalidade ou anuidade fixa. Isso é, às vezes, chamado de preço baseado em site. Com esse modelo de preços, opcionalmente, você pode definir planos limitados que usam a API do serviço de medição do Marketplace para cobrar dos clientes de acordo com unidades não padrão.  Para obter mais informações sobre cobrança limitada, confira [cobrança limitada usando o serviço de medição do Marketplace](./saas-metered-billing.md).  Você também deve usar essa opção se o comportamento de uso estiver em intermitências para seu serviço SaaS.  Não recomendamos que o cliente alterne planos com frequência diariamente ou por hora.

**Por usuário** – habilite o acesso à sua oferta com o preço com base no número de usuários acessando a oferta ou ocupando estações. Esse modelo baseado no usuário permite que você defina o número mínimo e máximo de usuários permitidos com base no preço. Dessa forma, ao configurar vários planos, é possível configurar pontos de preço diferentes com base no número de usuários.  Esses campos são opcionais. Se deixado desmarcado, o número de usuários será interpretado como não tendo um limite (mínimo de 1 e máximo limitado pela quantidade à qual o sistema der suporte). Esses campos podem ser editados como parte de uma atualização para seu plano.

Depois de publicada, a opção de modelo de preços de cobrança não pode ser alterada. Além disso, todos os planos para a mesma oferta precisam compartilhar o mesmo modelo de preços.

##### <a name="user-limits"></a>Limites de usuário

Se aplicável, selecione e defina limites mínimos e máximos de usuário.

##### <a name="billing-term-and-price"></a>Preço e prazo de cobrança

Selecione o **prazo** e o **preço** segundo os quais os clientes precisam pagar para o preço listado. Pelo menos uma mensalidade ou anuidade precisa ser fornecida ou ambas as opções podem ser disponibilizadas para os clientes.

Os preços definidos em US$ (US$ = dólares americanos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar, exportando a planilha de preços e examinando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. Você é responsável por validar esse preço e é proprietário dessas configurações.
*\*É necessário primeiro salvar as alterações de preços para habilitar a exportação de dados sobre preços.*

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado:

- Depois que um plano é publicado, o modelo de preços não pode ser alterado.
- Depois que um prazo de cobrança for publicado para um plano, ele não poderá ser removido posteriormente.
- Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

#### <a name="free-trial"></a>Avaliação gratuita

As ofertas de SaaS por meio do Marketplace comercial permitem que você forneça uma avaliação gratuita de um mês ao vender pela Microsoft. Para todos os modelos e prazos de cobrança, exceto em planos limitados, o uso de avaliações gratuitas é compatível. Essa opção permite que os clientes tenham uma entrada facilitada por meio de um mês de acesso gratuito.  Se você optar por habilitar uma avaliação gratuita para planos em sua oferta, o cliente não poderá converter para uma assinatura paga antes do final do período inicial de um mês.  Durante esse tempo, os clientes que comprarem sua oferta poderão experimentar qualquer um dos planos compatíveis que tenham a avaliação gratuita habilitada e converter entre eles.  A conversão para uma assinatura paga é feita automaticamente no final do prazo.

>[!NOTE]
>Se o cliente optar por converter em um plano sem avaliações gratuitas, a conversão ocorrerá, mas a avaliação gratuita será perdida imediatamente. Além disso, quando um cliente começa a pagar por um plano, ele não pode mais obter uma avaliação gratuita na mesma assinatura novamente, mesmo que ele converta para um SKU compatível com avaliações gratuitas.

Você pode configurar aqui uma avaliação gratuita para cada plano em sua oferta. Marque a caixa de seleção para permitir uma avaliação de um mês.

![Caixa de seleção de avaliação gratuita de um mês](./media/free-trial-enable.png)

>[!NOTE]
>Depois que sua oferta de transação for publicada com uma avaliação gratuita, ela não poderá ser desabilitada para esse plano. Verifique se essa configuração está correta para a primeira publicação para evitar ter que criar o plano novamente.

Para obter informações sobre as assinaturas de cliente que estão participando de uma avaliação gratuita, use a nova propriedade `isFreeTrial` da API, que será marcada como verdadeira ou falsa. Para obter mais informações, confira a [API Get de Assinatura do SaaS](pc-saas-fulfillment-api-v2.md#get-subscription).

>[!NOTE]
>Avaliações gratuitas não são compatíveis com planos que aproveitam o serviço de medição do Marketplace.

#### <a name="plan-visibility"></a>Visibilidade do plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público-alvo específico de sua escolha. Você pode atribuir uma associação a esse público-alvo restrito usando IDs de locatário do Azure AD.

##### <a name="privacy"></a>Privacidade

Selecione **Este é um plano privado** para tornar seu plano privado e visível somente para o público-alvo restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público-alvo ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele precisa permanecer visível para todos (e não pode ser configurado como um plano privado novamente).

##### <a name="restricted-audience-tenant-ids"></a>**Público-alvo restrito (IDs de locatário)**

Atribua o público-alvo que terá acesso a esse plano privado. O acesso é atribuído usando IDs de locatário com a opção de incluir uma descrição de cada ID do locatário atribuída. Um máximo de dez IDs de locatário podem ser adicionadas ou, se um arquivo de planilha .csv for importado, 20 mil IDs de locatário de cliente.

Um locatário é uma representação de uma organização, com uma ID representada como um GUID (identificador global exclusivo, um número inteiro de 128 bits usado para identificar recursos). É uma instância dedicada do Azure AD que uma organização ou um desenvolvedor de aplicativos recebe ao criar uma relação com a Microsoft, por exemplo, ao inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365. Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure. Para verificar o locatário, entre no portal do Azure com a conta que você deseja usar para gerenciar seu aplicativo. Se houver um locatário, você será conectado a ele automaticamente e verá o nome do locatário diretamente abaixo do nome da conta. Passe o mouse sobre o nome da conta no canto superior direito do portal do Azure para ver seu nome, email, diretório e ID do locatário (um GUID) e seu domínio. Se sua conta estiver associada a vários locatários, você pode selecionar o nome da sua conta para abrir um menu no qual você pode alternar entre locatários. Cada locatário tem sua própria ID exclusiva. Você também pode procurar a ID do locatário da sua organização usando uma URL de nome de domínio em [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Embora o SaaS ofereça a possibilidade de usar IDs de locatário para definir um público-alvo privado, outros tipos de oferta podem usar IDs de assinatura do Azure (que também são representadas como GUIDs).

> [!NOTE]
> Um público-alvo privado (ou público-alvo restrito) difere de um público-alvo de versão prévia. Na página **[Versão prévia](#preview-audience)** , você pode definir um público-alvo de versão prévia. Um público-alvo de versão prévia tem permissão para acessar sua oferta *antes* de ela ser publicada como ativa no Marketplace. Embora a designação de público-alvo privado se aplique apenas a um plano específico, o público-alvo de versão prévia pode ver todos os planos (privado ou não), mas somente durante o período limitado de versão prévia, enquanto o plano é testado e validado.

Selecione **Salvar rascunho** para continuar.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exemplo de lista de planos em uma oferta do Marketplace

:::image type="content" source="media/marketplace-plan.png" alt-text="Exemplo de listagem de planos do Marketplace com observações.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome do plano
2. Descrição do plano

<br>

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Público-alvo de revendedores CSP (Provedor de Soluções na Nuvem)

Optar por tornar sua oferta disponível no programa de CSP permite que os provedores de soluções de nuvem vendam seu produto como parte de uma solução em um pacote para os clientes deles. Para obter mais informações, confira [Provedores de solução na nuvem](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publicar

Depois de concluir todas as seções necessárias da oferta, selecione **Examinar e publicar** no canto superior direito do portal.

### <a name="submit-offer-to-preview"></a>Enviar oferta para versão prévia

Se for a primeira vez que você publicar essa oferta, você poderá:

- Ver o status de conclusão de cada seção da oferta.
    - **Não iniciada** – a seção não foi tocada e precisa ser concluída.
    - **Incompleta** – a seção tem erros que precisam ser corrigidos ou requer que mais informações sejam fornecidas. Você precisará retornar às seções e atualizá-las.
    - **Concluída** – a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta precisam estar no estado concluída para que você envie a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para exame e aprovação. Retorne ao Partner Center e selecione **Go-Live** para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
