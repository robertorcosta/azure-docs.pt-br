---
title: Criar uma oferta de contêiner do Azure no Partner Center-Azure Marketplace
description: Este artigo descreve como criar e publicar uma oferta de contêiner para o Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 3170849955485c0d098ed9f39c22332456e46fe4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81867356"
---
# <a name="create-an-azure-container-offer"></a>Criar uma oferta de Contêiner do Azure

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de contêiner do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [contêineres](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para Portal do Cloud Partner gerenciar suas ofertas.

Este artigo descreve como criar e publicar uma oferta de contêiner para o Azure Marketplace. Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Faça logon no [Partner Center](https://partner.microsoft.com/dashboard/home)e, no menu superior, selecione **painel**.
2. No menu à esquerda, selecione **Marketplace comercial**e, em seguida, **visão geral**.
3. Na página **visão geral** , selecione **+ nova oferta**e, em seguida, **contêiner do Azure**. A caixa de diálogo **nova oferta** é exibida.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Ilustração da página Visão geral no Partner Center. O botão nova oferta e a oferta de serviço de consultoria são realçados.":::

> [!TIP]
> Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em vitrines após a republicação da oferta. Certifique-se de sempre republicar depois de fazer alterações.

### <a name="offer-id-and-alias"></a>ID da oferta e alias

Insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID pode ser vista por clientes no endereço da Web para a oferta do Marketplace e modelos de Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **criar**.

**Insira um alias de** **oferta**. Este é o nome usado para se referir à oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Isso não pode ser alterado depois que você seleciona **criar**.

Selecione **criar** antes de continuar.

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da oferta** mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída e próxima) e quanto tempo cada etapa deve levar para ser concluída.

Esta página mostra links diferentes com base no status atual da oferta. Por exemplo:

- Se a oferta for uma oferta de [rascunho de exclusão](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver em tempo real, [pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em visualização- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não concluiu a saída do Publicador, [cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Instalação da oferta

Siga estas etapas para configurar sua oferta.

### <a name="connect-lead-management--optional"></a>Gerenciamento de leads de conexão – opcional

Ao publicar sua oferta no Marketplace com o Partner Center, você pode conectá-lo ao seu sistema CRM (gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar o interesse em ou usa seu produto.

1. **Selecione um destino de cliente potencial no qual você deseja que enviemos leads do cliente**. O Partner Center dá suporte aos seguintes sistemas CRM:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para compromisso com o cliente
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Se o sistema CRM não estiver listado acima, use a [tabela do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou o [ponto de extremidade https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados do cliente potencial e, em seguida, exporte os dados para o sistema CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicar no Partner Center.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos a você um líder de teste. Ao visualizar a oferta antes de ela ficar ativa, você também pode testar sua conexão de cliente potencial tentando comprar a oferta por conta própria no ambiente de visualização.
4. Verifique se a conexão com o destino do cliente potencial permanece atualizada para que você não perca nenhum cliente potencial.

Aqui estão alguns recursos adicionais de gerenciamento de leads:

- [Visão geral do gerenciamento de Lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de Lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (verifique se o bloqueador de pop-ups está desativado)

Selecione **salvar rascunho** antes de continuar para a próxima seção, propriedades.

### <a name="properties"></a>Propriedades

Esta página permite que você defina as categorias usadas para agrupar sua oferta no Marketplace e os contratos legais que dão suporte à sua oferta.

#### <a name="category"></a>Categoria

Selecione no mínimo um e no máximo cinco categorias. Essas categorias são usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas e são mostradas na página de detalhes da oferta. Na descrição da oferta, explique como sua oferta dá suporte a essas categorias. Os contêineres aparecem sob **contêineres** e, em seguida, na categoria **imagens de contêiner** .

#### <a name="legal"></a>Legal

Você deve fornecer os termos e condições para a oferta. Há duas opções:

- Use o contrato padrão para o Marketplace comercial da Microsoft.
- Forneça seus próprios termos e condições.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o Marketplace comercial da Microsoft

Oferecemos um modelo de contrato padrão para ajudar a facilitar transações no mercado comercial. Você pode optar por oferecer sua solução sob o contrato Standard, que os clientes precisam apenas verificar e aceitar uma vez. Essa é uma boa opção se você não quiser criar termos e condições personalizados.

Para saber mais sobre o contrato Standard, confira [contrato Standard para o Marketplace comercial da Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Você também pode baixar o PDF do [contrato padrão](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se o bloqueador de pop-ups está desativado).

Para usar o contrato padrão, marque a caixa de seleção **usar o contrato padrão para o Marketplace comercial da Microsoft** e clique em **aceitar**.

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão do Microsoft Commercial Marketplace, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o contrato Standard ou em seus próprios termos e condições.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustra a caixa de seleção usando o contrato Standard para o Marketplace comercial da Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer seus próprios termos e condições personalizados, insira-os na caixa **termos e condições** . Você pode inserir uma quantidade ilimitada de caracteres de texto nessa caixa. Os clientes devem aceitar estes termos antes que possam experimentar sua oferta.

Selecione **salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite que você defina os detalhes da oferta que são exibidos no Marketplace comercial. Isso inclui o nome, a descrição e as imagens da oferta.

> [!NOTE]
> Os detalhes da oferta não devem estar em inglês se a descrição da oferta começar com a frase "este aplicativo está disponível somente em [idioma diferente do inglês]". Também é conveniente fornecer um link útil para oferecer conteúdo em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

### <a name="name"></a>Name

O nome que você digitar aqui será exibido como o título da sua oferta. Esse campo é preenchido previamente com o texto inserido na caixa alias de **oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Podem ser marcadas (e você pode incluir os símbolos de marca registrada e direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Isso pode ter até 100 caracteres e é usado nos resultados da pesquisa do Marketplace.

### <a name="long-summary"></a>Resumo longo

Uma descrição mais detalhada da sua oferta. Isso pode ter até 256 caracteres e é usado nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem do Marketplace.

Inclua um ou mais dos seguintes em sua descrição:

- O valor e a chave beneficiam sua oferta
- Associações de categoria ou da indústria, ou ambas
- Oportunidades de compra no aplicativo
- Todas as divulgações necessárias

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente o valor de sua oferta nas primeiras frases da sua descrição. Inclua os seguintes itens:
  - Descrição da oferta.
  - O tipo de usuário que se beneficia da oferta
  - O cliente precisa ou emite os endereços da oferta.
- Lembre-se de que as primeiras frases podem ser exibidas nos resultados da pesquisa.
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta oferece.
- Tente usar vocabulário específico do setor ou palavras com base no benefício.

Para tornar a **Descrição** da sua oferta mais atraente, use o editor de Rich Text para formatar sua descrição. com numeração, marcadores, negrito, itálico e recuos para tornar sua descrição mais legível.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustra o editor de Rich Text." border="false" :::

- Use essa lista suspensa para aplicar um estilo de parágrafo ao texto.

    :::image type="content" source="media/text-editor3.png" alt-text="Ilustra o controle de estilo de texto no editor de Rich Text." border="false":::

- Use esses ícones para aplicar numeração ou marcadores ao texto.

     :::image type="content" source="media/text-editor4.png" alt-text="Ilustra os controles de lista com marcadores e número no editor de Rich Text." border="false":::

- Use esses ícones para adicionar ou remover recuos de ou para texto.

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustra os controles de recuo no editor de Rich Text." border="false":::

#### <a name="privacy-policy-link"></a>Link de política de privacidade

Insira o endereço Web da política de privacidade da sua organização. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também é responsável por publicar uma política de privacidade válida em seu site.

#### <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Você pode adicionar até 25 links. Para adicionar um link, selecione **+ Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Título** – os clientes verão isso na página de detalhes da sua oferta.
- **Link (URL)** – Insira um link para que os clientes exibam seu documento online. O link deve começar com http://ou https://.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone para um **contato de suporte** e um **contato de engenharia**. Essas informações não são mostradas aos clientes, mas estão disponíveis para a Microsoft. Ele também pode ser fornecido para parceiros do CSP (provedor de soluções na nuvem).

- Contato de suporte (obrigatório): para perguntas de suporte geral.
- Contato de engenharia (obrigatório): para questões técnicas e problemas de certificação.
- Contato do programa CSP (opcional): para perguntas de revendedor relacionadas ao programa CSP.

Na seção **contato de suporte** , forneça o **site de suporte** em que os parceiros podem encontrar suporte para sua oferta com base em se a oferta está disponível no Azure global, no Azure governamental ou em ambos.

Na seção de **contato do programa CSP** , forneça o link ( **materiais de marketing do programa CSP** ) em que os parceiros do CSP podem encontrar materiais de marketing para sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

Para saber mais sobre a criação de listagens de ofertas, consulte [práticas recomendadas de listagem de ofertas](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato PNG. As imagens borradas serão rejeitadas.

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

 Forneça arquivos PNG do logotipo da sua oferta em cada um dos quatro tamanhos de pixel a seguir:

- **Pequeno** (48 x 48)
- **Médio** (90 X 90)
- **Grande** (216 x 216)
- **Largo** (255 X 115)

Todos os quatro logotipos são necessários e são usados em locais diferentes na listagem do Marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram sua oferta. Insira o nome do vídeo, seu endereço Web e uma imagem PNG em miniatura do vídeo com 1280 x 720 pixels de tamanho.

#### <a name="offer-examples"></a>Exemplos de oferta

Os exemplos a seguir mostram como os campos de listagem de oferta aparecem em diferentes locais da oferta.

Isso mostra a página de **listagem de ofertas** no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustra a página de listagem de ofertas no Azure Marketplace." :::

Isso mostra os resultados da pesquisa no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustra os resultados da pesquisa no Azure Marketplace.":::

Isso mostra a página de **listagem de ofertas** no portal do Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustra a página de listagem de ofertas no portal do Azure.":::

Isso mostra os resultados da pesquisa no portal do Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustra os resultados da pesquisa em portal do Azure.":::

## <a name="preview"></a>Visualização

Na guia Visualização, você pode escolher um público- **alvo** limitado para validar sua oferta antes de publicá-la em tempo real.

> [!IMPORTANT]
> Depois de exibir sua oferta na versão **prévia**, você deve selecionar **entrar em tempo real** para publicar sua oferta no público.

Especifique seu público de visualização usando GUIDs de ID de assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes.

> [!NOTE]
> Você pode encontrar sua ID de assinatura do Azure na página assinaturas no portal do Azure.

Adicione pelo menos uma ID de assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você determina quem pode visualizar sua oferta antes que ela seja publicada ao vivo. Se sua oferta já estiver ativa, você poderá escolher um público de visualização para testar alterações ou atualizações na sua oferta.

> [!NOTE]
> O público de visualização difere de um público privado. Um público de **Visualização** pode ver e confirmar todos os planos de oferta antes que eles estejam ativos no Marketplace, incluindo aqueles que serão publicados somente em um público **privado** (definido na guia disponibilidade).

Selecione **salvar rascunho** antes de continuar.

### <a name="plan-overview"></a>Visão geral do plano

Esta guia permite que você forneça opções de plano diferentes na mesma oferta. Esses planos foram mencionados anteriormente como SKUs ou unidades de manutenção de estoque. Os planos podem diferir em termos de quais nuvens estão disponíveis, como nuvens globais, nuvens governamentais e a imagem referenciada pelo plano. Para listar sua oferta no Marketplace comercial, você deve configurar pelo menos um plano.

Depois de criar seus planos, a guia **visão geral do plano** mostra:

- Nomes de plano
- Modelo de preços
- Disponibilidade de nuvem (global ou governamental)
- Status de publicação atual
- Quaisquer ações disponíveis

As ações disponíveis na visão geral do plano variam de acordo com o status atual do seu plano. Elas incluem:

- **Excluir rascunho** – se o status do plano for um rascunho.
- **Parar plano de venda** – se o status do plano for publicado ao vivo.

#### <a name="create-new-plan"></a>Criar novo plano

Selecione **criar novo plano**. A caixa de diálogo **novo plano** é exibida.

Na caixa **ID do plano** , crie um identificador de plano exclusivo para cada plano nesta oferta. Essa ID será visível para os clientes no endereço da Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não pode ser alterada depois que você seleciona **criar**.

Na caixa **nome do plano** , insira um nome para este plano. Os clientes veem esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome exclusivo para cada plano nesta oferta. Por exemplo, você pode usar um nome de oferta do **Windows Server** com planos do **Windows Server 2016** e **do Windows Server 2019**.

### <a name="plan-setup"></a>Configuração do plano

Essa guia permite que você escolha em quais nuvens o plano está disponível. Suas respostas nessa guia afetam quais campos são exibidos em outras guias.

#### <a name="cloud-availability"></a>Disponibilidade na nuvem

Seu plano deve estar disponível em pelo menos uma nuvem.

Selecione a opção **global do Azure** para que seu plano possa ser usado por clientes em todas as regiões globais do Azure que usam o Marketplace comercial. Para obter detalhes, confira [disponibilidade geográfica e suporte a moeda](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecione a opção de [**nuvem do Azure governamental**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) para que sua solução apareça aqui. Essa é uma nuvem de comunidade governamental com acesso controlado para clientes das agências federais, estaduais e locais do governo dos EUA, bem como parceiros qualificados para atendê-los. Como Publicador, você é responsável por qualquer controle de conformidade, medidas de segurança e práticas recomendadas para essa comunidade em nuvem. O Azure governamental usa data centers isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de [publicar](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) no Azure governamental, teste e confirme sua solução dentro dessa área, pois os resultados podem ser diferentes. Para criar e testar sua solução, solicite uma conta de avaliação de [Microsoft Azure governamental avaliação](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e disponível em uma nuvem específica, você não poderá remover essa nuvem.

#### <a name="azure-government-cloud-certifications"></a>Certificações de nuvem do Azure governamental

Essa opção só poderá ser vista se a **nuvem do Azure governamental** estiver selecionada em **disponibilidade da nuvem**.

Os serviços do Azure governamental lidam com os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Para mostrar suas certificações para esses programas, você pode fornecer até 100 links que os descrevem. Eles podem ser links para suas listagens no programa diretamente ou para seu próprio site. Esses links são visíveis somente para clientes do Azure governamental.

## <a name="plan-listing"></a>Lista de planos

Essa guia exibe informações específicas para cada plano diferente na oferta atual.

### <a name="plan-name"></a>Nome do plano

Isso é preenchido previamente com o nome que você atribuiu ao seu plano quando o criou. Você pode alterar esse nome conforme necessário. Pode ter até 50 caracteres de comprimento. Esse nome aparece como o título deste plano no Azure Marketplace e portal do Azure. Ele é usado como o nome do módulo padrão depois que o plano está pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Um breve resumo do seu plano de software (não a oferta). Esse resumo aparece nos resultados da pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo, bem como as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. Essa descrição será exibida no Azure Marketplace e no portal do Azure na página de **listagem da oferta** . Pode ser o mesmo conteúdo fornecido no resumo do plano e conter até 2.000 caracteres.

Selecione **salvar** depois de concluir esses campos.

#### <a name="plan-examples"></a>Exemplos de planos

Os exemplos a seguir mostram como os campos de listagem de plano são exibidos em modos de exibição diferentes.

Estes são os campos no Azure Marketplace ao exibir detalhes do plano:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustra os campos que você vê ao exibir detalhes do plano no Azure Marketplace.":::

Estes são detalhes do plano no portal do Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustra os detalhes do plano no portal do Azure.":::

## <a name="plan-availability"></a>Disponibilidade do plano

Se você quiser ocultar sua oferta publicada para que os clientes não possam Pesquisar, navegar ou comprá-la no Marketplace, marque a caixa de seleção **ocultar plano** na guia **disponibilidade** .

Este campo é usado quando:

- A oferta destina-se a ser usada indiretamente quando referenciada, embora outro aplicativo.
- A oferta não deve ser adquirida individualmente.
- O plano foi usado para teste inicial e não é mais relevante.
- O plano foi usado para ofertas temporárias ou sazonais e não deve mais ser oferecido.

## <a name="technical-configuration"></a>Configuração técnica

As imagens de contêiner devem ser hospedadas em um [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/)privado. Na guia **configuração técnica** , forneça informações de referência para o repositório de imagens de contêiner dentro do registro de contêiner do Azure.

Depois que a oferta for publicada, a imagem de contêiner será copiada para o Azure Marketplace em um registro de contêiner público específico. Todas as solicitações para usar sua imagem de contêiner são servidas no registro de contêiner público do Azure Marketplace, não em sua privada. Para obter detalhes, consulte [preparar seus ativos técnicos do contêiner do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

### <a name="image-repository-details"></a>Detalhes do repositório de imagens

Forneça as seguintes informações na guia **detalhes do repositório de imagens** .

**ID da assinatura do Azure** – forneça a ID da assinatura na qual o uso é relatado e os serviços são cobrados pelo registro de contêiner do Azure que inclui a imagem de contêiner. Você pode encontrar essa ID na [página assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

**Nome do grupo de recursos do Azure** – forneça o nome do [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) que contém o registro de contêiner do Azure com a imagem de contêiner. O grupo de recursos deve estar acessível na ID da assinatura (acima). Você pode encontrar o nome na página [grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) na portal do Azure.

**Nome do registro de contêiner do Azure** – forneça o nome do [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) que tem sua imagem de contêiner. O registro de contêiner deve estar no grupo de recursos do Azure que você forneceu anteriormente. Inclua somente o nome do registro, não o nome do servidor de logon completo. Não deixe de omitir **azurecr.Io** do nome. Você pode encontrar o nome do registro na [página registros de contêiner](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) no portal do Azure.

**Nome de usuário do administrador para o registro de contêiner do Azure** – forneça o [nome de usuário do administrador](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) vinculado ao registro de contêiner do Azure que tem sua imagem de contêiner. O nome de usuário e a senha são necessários para garantir que sua empresa tenha acesso ao registro. Para obter o nome de usuário e a senha do administrador, defina a propriedade **habilitada** para o administrador como **true** usando a CLI (interface de linha de comando) do Azure. Opcionalmente, você pode definir o **usuário administrador** para **habilitar** o no portal do Azure.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustra a caixa de diálogo atualizar registro de contêiner.":::

**Senha para o registro de contêiner do Azure** – forneça a senha para o nome de usuário do administrador que está associado ao registro de contêiner do Azure e tem sua imagem de contêiner. O nome de usuário e a senha são necessários para garantir que sua empresa tenha acesso ao registro. Você pode obter a senha do portal do Azure acessando**chaves de acesso** **do registro** > de contêiner ou com CLI do Azure usando o [comando show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustra o menu de chave de acesso.":::

**Nome do repositório no registro de contêiner do Azure**. Forneça o nome do repositório do registro de contêiner do Azure que tem sua imagem. Inclua o nome do repositório ao enviar a imagem por push para o registro. Você pode encontrar o nome do repositório acessando a página**repositórios** [do registro](https://azure.microsoft.com/services/container-registry/) > de contêiner. Para obter mais informações, consulte [Exibir repositórios de registro de contêiner no portal do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> Depois que o nome é definido, ele não pode ser alterado. Use um nome exclusivo para cada oferta em sua conta.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Marcas de imagem para novas versões da sua oferta

Os clientes devem ser capazes de obter atualizações automaticamente do Azure Marketplace quando você publica uma atualização. Se não quiserem atualizar, eles deverão ser capazes de permanecer em uma versão específica da imagem. Você pode fazer isso adicionando novas marcas de imagem cada vez que fizer uma atualização para a imagem.

### <a name="image-tag"></a>Tag de imagem

Esse campo deve incluir uma marca **mais recente** que aponta para a versão mais recente da imagem em todas as plataformas com suporte. Ele também deve incluir uma marca de versão (por exemplo, começando com XX. XX. XX, em que XX é um número). Os clientes devem usar [marcas de manifesto](https://github.com/estesp/manifest-tool) para direcionar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las.

Todas as marcas de manifesto (exceto a marca mais recente) devem começar com X **-** . y ou x. y. Z-em que x, Y e Z são inteiros. Por exemplo, se uma marca **mais recente** apontar para 1.0.1-Linux-x64, 1.0.1-Linux-arm32 e 1.0.1-Windows-arm32, essas seis marcas precisarão ser adicionadas a esse campo. Para obter detalhes, consulte [preparar seus ativos técnicos do contêiner do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Lembre-se de adicionar uma marca de teste à sua imagem para que você possa identificar a imagem durante o teste.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-las para revisar e publicar.

No canto superior direito do portal, selecione **revisar e** **publicar**.

Na página revisão, você pode:

- Consulte o status de conclusão de cada seção da oferta. Não é possível publicar até que todas as seções da oferta sejam marcadas como concluídas.
  - **Não iniciado – não** foi iniciado e precisa ser concluído.
  - **Incompleto** – tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores neste documento para obter ajuda.
  - **Concluído** – inclui todos os dados necessários sem erros. Todas as seções da oferta devem ser concluídas para que você possa enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que sua oferta seja testada corretamente. Além disso, forneça notas suplementares úteis para entender sua oferta.

Para enviar a oferta para publicação, selecione **publicar**.

Enviaremos um email para que você saiba quando uma versão de visualização da oferta está disponível para revisão e aprovação.

Para publicar sua oferta no público (ou se uma oferta privada, para um público privado), vá para o Partner Center e selecione **Go-Live**.

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
