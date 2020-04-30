---
title: Criar uma oferta de serviço de consultoria no Partner Center-Azure Marketplace
description: Saiba como publicar uma oferta de serviço de consultoria no Azure Marketplace ou AppSource usando o Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869789"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de serviço de consultoria de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [oferta do serviço de consultoria do Dynamics 365 e do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) para Portal do Cloud Partner gerenciar suas ofertas.

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou [AppSource](https://appsource.microsoft.com/). Liste as ofertas do serviço de consultoria com base no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e no Power Platform no AppSource. Listar ofertas de serviço de consultoria com base em Microsoft Azure no Azure Marketplace.

Para criar uma oferta de serviço de consultoria no Azure Marketplace ou nos serviços de consultoria AppSource, você deve primeiro [ter uma conta de editor no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)e sua conta deve ser inscrita no programa do Marketplace comercial. Antes de criar sua oferta, examine os pré-requisitos em [pré-requisitos do serviço de consultoria](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no Marketplace comercial:

- Promova sua empresa usando a marca da Microsoft.
- Pode alcançar mais de 100 milhões usuários do Office 365 e do Dynamics 365 em AppSource e mais de 200.000 organizações por meio do Azure Marketplace.
- Receba leads de alta qualidade desses Marketplaces.
- Seus serviços são promovidos pelas equipes de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma oferta

Depois de atender aos requisitos descritos acima, siga estas etapas para criar uma oferta de serviço de consultoria.

1. Faça logon no [Partner Center](https://partner.microsoft.com)e selecione **painel** no menu superior.
2. Na barra de navegação à esquerda, selecione **Marketplace comercial**e, em seguida, selecione **visão geral**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustra o menu do Marketplace comercial":::

3. Selecione **+ nova oferta**e, em seguida, selecione **serviço de consultoria**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Ilustra o botão para criar uma nova oferta.":::

4. Insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

    - Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace.
    - Use apenas letras minúsculas, números, traços e sublinhados, mas sem espaços. O comprimento é limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1**, a URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - A ID da oferta pode&#39;t ser alterada depois que você selecionar **criar**.

5. Insira um **alias de oferta**. Este é o nome usado para se referir à oferta no Partner Center.

    - Esse nome não é&#39;t usado no Marketplace. Ele&#39;s diferentes do nome da oferta e outros valores que são mostrados aos clientes. Você pode usar esse campo para atribuir um nome à oferta que é mais útil para identificar a oferta internamente; Ele não é mostrado aos clientes.
    - O alias da oferta pode&#39;t ser alterado depois que você seleciona **criar**.

Depois de inserir esses dois valores, selecione **criar** para continuar na página de **configuração da oferta** .

## <a name="offer-setup"></a>Instalação da oferta

Depois de inserir uma ID de oferta e um alias de oferta, o Partner Center cria uma oferta de rascunho e exibe a página de **configuração da oferta** . Siga estas etapas para configurar sua oferta.

### <a name="connect-lead-management"></a>Conectar gerenciamento de leads

Ao publicar sua oferta no Marketplace com o Partner Center, você _deve_ conectá-lo a um CRM (gerenciamento de relacionamento com o cliente) ou a um sistema de automação de marketing. Isso permite que você receba informações de contato do cliente assim que alguém expressar o interesse em ou usa seu produto.

1. Selecione **conectar** para especificar onde você deseja que os clientes potenciais enviem leads do cliente. O Partner Center dá suporte aos seguintes sistemas:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para compromisso com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema CRM não&#39;listado acima, use a [tabela do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou o [ponto de extremidade https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados do cliente potencial e, em seguida, exporte os dados para o sistema CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicar no Partner Center.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-lo no Partner Center, nós&#39;validamos a conexão e enviamos a você um líder de teste. Ao visualizar a oferta antes de ela ficar ativa, você também pode testar sua conexão de cliente potencial tentando comprar a oferta por conta própria no ambiente de visualização.
4. Certifique-se de que a conexão com o destino do cliente potencial permaneça atualizada para que você Don&#39;t perder os leads.

Aqui estão alguns recursos adicionais de gerenciamento de leads:

- [Visão geral do gerenciamento de Lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de Lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (verifique se o bloqueador de pop-ups está desativado)

Selecione **salvar rascunho** antes de continuar para a próxima seção, propriedades.

### <a name="properties"></a>Propriedades

Esta página permite que você defina o produto principal que seu serviço de consultoria oferece melhor cobertura, defina um tipo de serviço de consultoria e escolha produtos aplicáveis.

1. Selecione um **produto primário** na lista suspensa.
2. Selecione um **tipo de serviço de consultoria** na lista suspensa:

    - **Avaliação** : uma avaliação de um ambiente de cliente&#39;s para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Resumo** : uma introdução a uma solução ou um serviço de consultoria para atrair o interesse do cliente usando estruturas, demonstrações e exemplos de clientes.
    - **Implementação** : uma instalação completa que resulta em uma solução totalmente funcional. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito** : uma implementação de escopo limitado para determinar se uma solução atende aos requisitos do cliente.
    - **Workshop** : um engajamento interativo realizado em um cliente&#39;s local. Ele pode envolver treinamentos, resumos, avaliações ou demonstrações criados nos dados ou no ambiente do cliente&#39;s.

1. Se você selecionou um produto principal do **Azure**, selecione até três **áreas de solução**. Isso facilita para os clientes do Azure Marketplace encontrarem sua oferta. Se você não&#39;em escolher o Azure, pule esta etapa.
2. Se você selecionou um produto primário _diferente_ do Azure, selecione até três **produtos aplicáveis**. Isso facilita para os clientes do AppSource a localização de sua oferta. Para obter detalhes, consulte [diretrizes de listagem de serviço de consultoria do Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Selecione até seis **setores** aos quais sua oferta se aplica. Isso facilitará para os clientes a localização de sua oferta.
4. Adicione até três **competências** que sua empresa obteve para exibir em sua listagem de ofertas de serviços de consultoria. Pelo menos uma competência é necessária, exceto para o especialista do Azure MSP&#39;s e a rede do Azure MSP&#39;s.

Selecione **salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui você&#39;definir os detalhes da oferta que são exibidos no Marketplace. Isso inclui o nome da oferta, a descrição, as imagens e assim por diante. Certifique-se de seguir as políticas detalhadas na [página de política do Microsoft&#39;s](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) ao configurar esta oferta.

> [!NOTE]
> Os detalhes da oferta são desnecessários&#39;em inglês se a descrição da oferta começar com a &quot;frase, esse aplicativo estará disponível apenas em [idioma diferente do inglês]. &quot; Também&#39;s para fornecer um link útil para oferecer conteúdo em um idioma que&#39;s diferente daquele usado nos detalhes da listagem de ofertas.

### <a name="name"></a>Name

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido previamente com o texto inserido na caixa alias de **oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir os símbolos de marca registrada ou de direitos autorais).
- Pode&#39;t ter mais de 50 caracteres.
- Pode&#39;t incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isso pode ter até 100 caracteres e é usado nos resultados da pesquisa do Marketplace.

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
  - O tipo de usuário que se beneficia da oferta.
  - O cliente precisa ou emite os endereços da oferta.
- Lembre-se de que as primeiras frases podem ser exibidas nos resultados da pesquisa.
- Don&#39;t confia em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta oferece.
- Tente usar vocabulário específico do setor ou palavras com base no benefício.

Para tornar sua descrição mais atraente, use o editor de Rich Text para formatar sua descrição. O editor de Rich Text permite que você adicione números, marcadores, negrito, itálico e recuos para tornar sua descrição mais legível.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustra o editor de Rich Text para escrever a descrição da oferta." border="false":::

### <a name="keywords"></a>Palavras-chave

Insira até três palavras-chave de pesquisa que são relevantes para seu produto principal e serviço de consultoria. Isso facilitará a localização da sua oferta.

### <a name="duration"></a>Duration

Defina a duração esperada deste compromisso com o cliente.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone para um **contato** **primário** e secundário. Essas informações não&#39;t mostradas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido para parceiros do CSP (provedor de soluções na nuvem).

### <a name="supporting-documents"></a>Documentos de suporte

Adicione até três (mas pelo menos um) suporte a documentos PDF para sua oferta.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato. png. As imagens borradas serão rejeitadas.

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos. png da sua oferta&#39;o logotipo em cada um dos seguintes tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logotipos são necessários e são usados em locais diferentes na listagem do Marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e no formato. png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até quatro vídeos que demonstram sua oferta. Insira o vídeo&#39;s Name, seu endereço Web (URL) e uma imagem thumbnail. png do vídeo com 1280 x 720 pixels de tamanho.

Selecione **salvar rascunho** antes de continuar para a próxima seção, preços e disponibilidade.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui, você definirá elementos como preços, mercado e uma chave privada.

1. **Mercado**: defina o mercado que sua oferta estará disponível no. Você pode selecionar apenas um mercado por oferta.
    1. Para os mercados Estados Unidos ou Canadá, selecione **Editar Estados** (ou **províncias**) para especificar onde sua oferta estará disponível.
2. **Público de visualização**: Configure a **chave de ocultação** usada para definir o público privado para sua oferta.
3. **Preço**: especifique se sua oferta é uma oferta **gratuita** ou **paga** .

    > [!NOTE]
    > As ofertas de serviço de consultoria são apenas para a listagem. Todas as transações ocorrerão diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **preço e a moeda** e se o preço é **fixo** ou **estimado**. Se for estimado, você deverá especificar na descrição quais fatores afetarão o preço.
5. Selecione **salvar rascunho**.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviar sua oferta para revisar e publicar.

1. Quando estiver pronto para publicar sua oferta de serviço de consultoria, clique em **revisar e publicar**.
2. Examine os detalhes na página final de envio.
3. Se necessário, escreva uma nota para a equipe de certificação se acreditar que qualquer um dos detalhes da sua oferta exigirá explicação.
4. Quando estiver pronto, selecione **Enviar**.
5. A página **visão geral da oferta** mostra em que estágio de publicação sua oferta está.

Para obter mais informações sobre quanto tempo você pode esperar que sua oferta esteja em cada estágio de publicação, consulte [verificar o status de publicação de sua oferta do Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Atualize suas ofertas de serviço de consultoria existentes

- [Atualizar uma oferta existente no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
