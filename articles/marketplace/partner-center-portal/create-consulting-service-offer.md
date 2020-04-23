---
title: Crie uma oferta de serviço de consultoria no Partner Center - Azure Marketplace
description: Saiba como publicar uma oferta de serviço de consultoria para o Azure Marketplace ou o AppSource usando o Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869789"
---
# <a name="create-a-consulting-service-offer"></a>Crie uma oferta de serviço de Consultoria

> [!IMPORTANT]
> Estamos mudando a gestão de suas ofertas de serviços de consultoria do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções na [oferta de serviços de consultoria Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) para o Cloud Partner Portal gerenciar suas ofertas.

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou [o AppSource](https://appsource.microsoft.com/). O serviço list consulting oferece com base no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e na Power Platform no AppSource. O serviço list consulting oferece com base no Microsoft Azure no Azure Marketplace.

Para criar uma oferta de serviço de Consultoria nos serviços do Azure Marketplace ou da AppSource Consulting, você deve primeiro [ter uma conta de editor no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), e sua conta deve estar inscrita no programa de marketplace comercial. Antes de criar sua oferta, revise os pré-requisitos em [pré-requisitos de consultoria.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no mercado comercial:

- Promova sua empresa usando a marca Microsoft.
- Potencialmente atingir mais de 100 milhões de usuários do Office 365 e Dynamics 365 no AppSource e mais de 200.000 organizações através do Azure Marketplace.
- Receba leads de alta qualidade desses marketplaces.
- Tenha seus serviços promovidos pelas equipes de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma oferta

Após atender aos requisitos descritos acima, siga estas etapas para criar uma oferta de serviço de Consultoria.

1. Faça login no [Partner Center](https://partner.microsoft.com)e selecione **Dashboard** no menu superior.
2. Na barra de navegação esquerda, selecione **Comercial Marketplace**e selecione **Visão geral**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustra o cardápio para o mercado comercial":::

3. Selecione **+ Nova Oferta,** em seguida, selecione **Serviço de Consultoria**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Ilustra o botão para criar uma nova oferta.":::

4. Digite um **ID de Oferta**. Este é um identificador único para cada oferta em sua conta.

    - Este ID é visível para os clientes no endereço web para a oferta de marketplace.
    - Use apenas letras minúsculas, números, traços e sublinhados, mas sem espaços. O comprimento é limitado a 50 caracteres. Por exemplo, se você inserir **test-offer-1,** a URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - O ID de oferta&#39;não pode ser alterado depois de selecionar **Criar**.

5. Digite um **alias De Oferta**. Este é o nome usado para se referir à oferta no Partner Center.

    - Este nome não&#39;usado no mercado. Ele&#39;é diferente do nome da oferta e outros valores que são mostrados aos clientes. Você pode usar este campo para atribuir um nome à oferta que é mais útil para você para identificar a oferta internamente; não é mostrado aos clientes.
    - O alias de oferta pode&#39;não ser alterado depois de selecionar **Criar**.

Depois de inserir esses dois valores, selecione **Criar** para continuar na página **de configuração Oferta.**

## <a name="offer-setup"></a>Configuração da oferta

Depois de inserir um alias de ID de oferta e oferta, o Partner Center cria uma oferta de rascunho e exibe a página **de configuração oferta.** Siga estes passos para configurar sua oferta.

### <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

Ao publicar sua oferta no mercado com o Partner Center, você _deve_ conectá-la a um CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) ou a um sistema de automação de marketing. Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse ou usar seu produto.

1. Selecione **Conectar** para especificar para onde você deseja enviar leads de clientes. O Partner Center suporta os seguintes sistemas:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para Engajamento do Cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema de CRM não estiver&#39;listado acima, use [a Tabela Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Ponto Final https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar dados de lead do cliente e exporte os dados para o sistema de CRM.

2. Conecte sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a conexão com o destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center,&#39;validaremos a conexão e enviaremos um lead de teste. Enquanto você visualiza a oferta antes de entrar em operação, você também pode testar sua conexão de chumbo tentando comprar a oferta você mesmo no ambiente de visualização.
4. Certifique-se de que a conexão com o destino de chumbo permaneça atualizada para que você não perca&#39;nenhum leads.

Aqui estão alguns recursos adicionais de gerenciamento de chumbo:

- [Visão geral do gerenciamento de líderes](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de líderes](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o bloqueador pop-up está desligado)

Selecione **Salvar rascunho** antes de continuar para a próxima seção, Propriedades.

### <a name="properties"></a>Propriedades

Esta página permite definir o produto principal que seu serviço de Consultoria oferece as melhores capas, definir um tipo de serviço de Consultoria e escolher produtos aplicáveis.

1. Selecione um **produto principal** na lista de paradas.
2. Selecione um tipo de serviço de **consultoria** na lista de paradas:

    - **Avaliação** : Uma avaliação do ambiente de&#39;de um cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Briefing** : Uma introdução a uma solução ou um serviço de consultoria para atrair o interesse do cliente usando frameworks, demonstrações e exemplos de clientes.
    - **Implementação** : Uma instalação completa que resulte em uma solução totalmente funcionando. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito** : Uma implementação de escopo limitado para determinar se uma solução atende aos requisitos do cliente.
    - **Workshop** : Um engajamento interativo realizado em um cliente&#39;as instalações. Ele pode envolver treinamento, briefings, avaliações ou demonstrações construídas sobre os dados ou ambiente do&#39;do cliente.

1. Se você selecionou um produto principal do **Azure,** selecione até três **Áreas de Solução**. Isso facilita que os clientes no Azure Marketplace encontrem sua oferta. Se você não&#39;não escolheu o Azure, pule este passo.
2. Se você selecionou um produto principal _diferente_ do Azure, selecione até três **produtos aplicáveis**. Isso facilita que os clientes no AppSource encontrem sua oferta. Para obter detalhes, consulte [As Diretrizes de Listagem de Serviços de Consultoria](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) da Microsoft AppSource (PDF).
3. Selecione até seis **Indústrias às** as que sua oferta se aplica. Isso tornará mais fácil para os clientes encontrar sua oferta.
4. Adicione três **Competências** que sua empresa ganhou para exibir em sua lista de ofertas de serviçode consultoria. Pelo menos uma competência é necessária, exceto para o Azure Expert MSP&#39;s e Azure Networking MSP&#39;s.

Selecione **Salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui você&#39;definirá os detalhes da oferta que são exibidos no mercado. Isso inclui o nome da oferta, descrição, imagens e assim por diante. Certifique-se de seguir as políticas detalhadas na [página de diretiva do&#39;da Microsoft](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) enquanto configura esta oferta.

> [!NOTE]
> Os detalhes da oferta&#39;não é necessário estar &quot;em inglês se a descrição da oferta começar com a frase, este aplicativo está disponível apenas em [língua não-inglesa]. &quot; Também&#39;bem fornecer um Link útil para oferecer conteúdo em um idioma que&#39;diferente do usado nos detalhes de listagem da Oferta.

### <a name="name"></a>Nome

O nome que você digita aqui é exibido como o título de sua oferta. Este campo está pré-preenchido com o texto que você inseriu na caixa **de alias Oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir símbolos de marca ou direitos autorais).
- Não pode&#39;ter mais de 50 caracteres.
- Não&#39;pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição de sua oferta. Isso pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa de sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem de marketplace.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os principais benefícios que sua oferta oferece
- Categorias ou associações industriais, ou ambos
- Oportunidades de compra no aplicativo
- Quaisquer divulgações necessárias

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente o valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
  - Descrição da oferta.
  - O tipo de usuário que se beneficia da oferta.
  - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados de pesquisa.
- Não&#39;contar com recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta fornece.
- Tente usar vocabulário específico da indústria ou redação baseada em benefícios.

Para tornar sua descrição mais atraente, use o rico editor de texto para formatar sua descrição. O rico editor de texto permite adicionar números, balas, negrito, itálico e recuos para tornar sua descrição mais legível.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustra o rico editor de texto para escrever a descrição da oferta." border="false":::

### <a name="keywords"></a>Palavras-chave

Digite até três palavras-chave de pesquisa relevantes para o seu produto principal e serviço de Consultoria. Isso tornará mais fácil encontrar sua oferta.

### <a name="duration"></a>Duration

Defina a duração esperada deste compromisso com seu cliente.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, e-mail e número de telefone para um contato **primário** e **secundário**. Essas informações não&#39;apresentadas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido aos parceiros CSP (Cloud Solution Provider).

### <a name="supporting-documents"></a>Documentos de suporte

Adicione até três (mas pelo menos um) documentos PDF com suporte para sua oferta.

### <a name="marketplace-images"></a>Imagens de mercado

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato .png. As imagens embaçadas serão rejeitadas.

>[!Note]
>Se você tiver um problema de upload de arquivos, https://upload.xboxlive.com certifique-se de que sua rede local não bloqueie o serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos .png de sua oferta&#39;logotipo em cada um dos seguintes tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logotipos são necessários e são usados em diferentes lugares na listagem de marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e em formato .png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione quatro vídeos que demonstram sua oferta. Digite o nome&#39;de vídeo, seu endereço web (URL) e uma imagem miniatura .png do vídeo com 1280 x 720 pixels de tamanho.

Selecione **Salvar rascunho** antes de continuar para a próxima seção, Preços e disponibilidade.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui você definirá elementos como preços, mercado e uma chave privada.

1. **Mercado**: Defina o mercado em que sua oferta estará disponível. Você só pode selecionar um mercado por oferta.
    1. Para os mercados dos Estados Unidos ou Canadá, selecione **Editar estados** (ou **províncias)** para especificar onde sua oferta estará disponível.
2. **Visualização audiência**: Configure a **chave ocultausada** para definir o público privado para sua oferta.
3. **Preços**: Especifique se sua oferta é uma oferta **gratuita** ou **paga.**

    > [!NOTE]
    > As ofertas de serviços de consultoria são apenas para a listagem. Qualquer transação acontecerá diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **Preço e** a moeda e se o preço é **Fixo** ou **Estimado**. Se Estimado, você deve especificar na descrição quais fatores afetarão o preço.
5. Selecione **Salvar rascunho**.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviar sua oferta para revisar e publicar.

1. Quando estiver pronto para publicar sua oferta de serviço de consultoria, clique **em Revisar e publicar**.
2. Reveja os detalhes na página final de submissão.
3. Se necessário, escreva uma nota para a equipe de certificação se você acreditar que qualquer um dos detalhes de sua oferta requerem explicação.
4. Quando estiver pronto, **selecione Enviar**.
5. A página **visão geral da Oferta** mostra em que estágio de publicação sua oferta está.

Para obter mais informações sobre quanto tempo você pode esperar que sua oferta esteja em cada fase de publicação, consulte [Verifique o status de publicação da sua oferta de Comercial Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Atualize suas ofertas de serviços de consultoria existentes

- [Atualize uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
