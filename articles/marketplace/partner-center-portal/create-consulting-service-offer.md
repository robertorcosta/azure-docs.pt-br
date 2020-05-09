---
title: Criar uma oferta de serviço de consultoria-Microsoft Commercial Marketplace
description: Saiba como publicar uma oferta de serviço de consultoria para Microsoft AppSource ou o Azure Marketplace usando o Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994346"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

Este artigo descreve como publicar uma oferta de serviço de consultoria para [Microsoft AppSource](https://appsource.microsoft.com/) ou o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Liste as ofertas do serviço de consultoria com base no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e no Power Platform no AppSource. Listar ofertas de serviço de consultoria com base em Microsoft Azure no Azure Marketplace. Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa comercial do Marketplace.

Antes de criar sua oferta, examine os pré-requisitos em [pré-requisitos do serviço de consultoria](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no Marketplace comercial:

- Promova sua empresa usando a marca da Microsoft.
- Pode alcançar mais de 100 milhões usuários do Office 365 e do Dynamics 365 em AppSource e mais de 200.000 organizações por meio do Azure Marketplace.
- Receba leads de alta qualidade desses Marketplaces.
- Seus serviços são promovidos pelas equipes de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione > **visão geral**do **Marketplace comercial**.
3. Na página Visão geral, selecione **+ novo** > **serviço de consultoria**de oferta.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em vitrines após a republicação da oferta. Certifique-se de sempre republicar depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **criar**.

Insira um **alias de oferta**. Este é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O alias da oferta não pode ser alterado depois que você seleciona **criar**.

Selecione **criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Instalação da oferta

### <a name="connect-lead-management"></a>Conectar gerenciamento de leads

Ao publicar sua oferta no Marketplace com o Partner Center, você _deve_ conectá-lo a um CRM (gerenciamento de relacionamento com o cliente) ou a um sistema de automação de marketing. Isso permite que você receba informações de contato do cliente assim que alguém expressar o interesse em ou usa seu produto.

1. Selecione **conectar** para especificar onde você deseja que os clientes potenciais enviem leads do cliente. O Partner Center dá suporte aos seguintes sistemas:

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
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Visão geral do gerenciamento de Lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (verifique se o bloqueador de pop-ups está desativado)

Selecione **salvar rascunho** antes de continuar.

### <a name="properties"></a>Propriedades

Esta página permite que você defina o produto principal que seu serviço de consultoria oferece melhor cobertura, defina um tipo de serviço de consultoria e escolha produtos aplicáveis.

1. Selecione um **produto primário** na lista suspensa.
2. Selecione um **tipo de serviço de consultoria** na lista suspensa:

    - **Avaliação**: uma avaliação do ambiente de um cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Resumo**: uma introdução a uma solução ou um serviço de consultoria para atrair o interesse do cliente usando estruturas, demonstrações e exemplos de clientes.
    - **Implementação**: uma instalação completa que resulta em uma solução totalmente funcional. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito:** uma implementação de escopo limitado para determinar se uma solução atende aos requisitos do cliente.
    - **Workshop**: um engajamento interativo realizado nos locais de um cliente. Ele pode envolver treinamentos, resumos, avaliações ou demonstrações criados nos dados ou no ambiente do cliente.

3. Se você selecionou um produto principal do **Azure**, selecione até três **áreas de solução**. Isso facilita para os clientes do Azure Marketplace encontrarem sua oferta. Se você não escolheu o Azure, pule esta etapa.
4. Se você selecionou um produto primário _diferente_ do Azure, selecione até três **produtos aplicáveis**. Isso facilita para os clientes do AppSource a localização de sua oferta. Para obter detalhes, consulte [diretrizes de listagem de serviço de consultoria do Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
5. Selecione até seis **setores** aos quais sua oferta se aplica. Isso facilitará para os clientes a localização de sua oferta.
6. Adicione até três **competências** que sua empresa obteve para exibir em sua listagem de ofertas de serviços de consultoria. Pelo menos uma competência é necessária, exceto para o especialista do Azure MSPs e o MSPs de rede do Azure.

Selecione **salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que são exibidos no Marketplace. Isso inclui o nome da oferta, a descrição, as imagens e assim por diante. Certifique-se de seguir as políticas detalhadas na [página políticas de certificação do Marketplace comercial](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) ao configurar esta oferta.

> [!NOTE]
> Os detalhes da oferta não devem estar em inglês se a descrição da oferta começar com a &quot;frase, esse aplicativo estará disponível somente no [idioma diferente do inglês]. &quot; Também é conveniente fornecer um link útil para oferecer conteúdo em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido previamente com o texto inserido na caixa alias de **oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir os símbolos de marca registrada ou de direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

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
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta oferece.
- Tente usar vocabulário específico do setor ou palavras com base no benefício.

Para tornar a descrição da sua oferta mais atraente, use o editor de Rich Text para aplicar formatação.

![Usando o editor de Rich Text](./media/rich-text-editor.png)

| <center>Alterar formato de texto | <center>Adicionar marcadores ou numeração | <center>Adicionar ou remover recuo de texto |
| --- | --- | --- |
| <center>![Usando o editor de Rich Text para alterar o formato de texto](./media/text-editor3.png) |  <center>![Usando o editor de Rich Text para adicionar listas](./media/text-editor4.png) |  <center>![Usando o editor de Rich Text para recuar](./media/text-editor5.png) |

### <a name="keywords"></a>Palavras-chave

Insira até três palavras-chave de pesquisa que são relevantes para seu produto principal e serviço de consultoria. Isso facilitará a localização da sua oferta.

### <a name="duration"></a>Duration

Defina a duração esperada deste compromisso com o cliente.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone para um **contato** **primário** e secundário. Essas informações não são mostradas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido para parceiros do CSP (provedor de soluções na nuvem).

### <a name="supporting-documents"></a>Documentos de suporte

Adicione até três (mas pelo menos um) suporte a documentos PDF para sua oferta.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato. png. As imagens borradas serão rejeitadas.

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos PNG do logotipo da sua oferta em cada um dos seguintes tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logotipos são necessários e são usados em locais diferentes na listagem do Marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até quatro vídeos que demonstram sua oferta. Insira o nome do vídeo, seu endereço Web (URL) e uma imagem PNG em miniatura do vídeo com 1280 x 720 pixels de tamanho.

Selecione **salvar rascunho** antes de continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui, você definirá elementos como preços, mercado e uma chave privada.

1. **Mercado**: defina o mercado que sua oferta estará disponível no. Você pode selecionar apenas um mercado por oferta.
    1. Para os mercados Estados Unidos ou Canadá, selecione **Editar Estados** (ou **províncias**) para especificar onde sua oferta estará disponível.
2. **Público de visualização**: Configure a **chave de ocultação** usada para definir o público privado para sua oferta.
3. **Preço**: especifique se sua oferta é uma oferta **gratuita** ou **paga** .

    > [!NOTE]
    > As ofertas de serviço de consultoria são apenas para a listagem. Todas as transações ocorrerão diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **preço e a moeda** e se o preço é **fixo** ou **estimado**. Se for estimado, você deverá especificar na descrição quais fatores afetarão o preço.
5. Selecione **salvar rascunho** antes de continuar.

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
