---
title: Crie uma oferta de aplicativo Power BI - Azure Marketplace
description: Saiba como criar e publicar uma oferta de aplicativo Power BI para o Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867590"
---
# <a name="create-a-power-bi-app"></a>Criar um aplicativo do Power BI

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas do seu aplicativo Power BI do Portal de Parceiros da Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Criar uma oferta de aplicativo power bi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) para o Cloud Partner Portal para gerenciar suas ofertas.

Este artigo descreve como criar e publicar uma oferta de aplicativo Power BI para o Microsoft [AppSource](https://appsource.microsoft.com/).

Antes de criar uma oferta de aplicativo power bi, você deve ter uma conta de mercado comercial no Partner Center. Se você ainda não criou uma, consulte Criar uma conta de Mercado Comercial na conta [do Partner Center.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Faça login no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione Visão geral **do mercado** > **comercial**.

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="visão geral do menu do mercado comercial" border="false":::

3. Selecione **+ Nova oferta** > **Power BI App**. A caixa de diálogo **Nova oferta** é exibida.

> [!IMPORTANT]
> Se a opção **Power BI App**&#39;não for exibida ou&#39;não estiver habilitada, sua conta não&#39;ter permissão para criar esse tipo de oferta. Por favor, verifique se você&#39;cumpriu todos os [requisitos](create-power-bi-app-overview.md) para este tipo de oferta, incluindo o registro de uma conta de desenvolvedor

### <a name="offer-id-and-alias"></a>Oferecer ID e alias

Digite um **ID de Oferta**. Este é um identificador único para cada oferta em sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de marketplace e modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e é limitado a 50 caracteres. Por exemplo, se você inserir **test-offer-1,** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .

- O ID de oferta não pode ser alterado depois que você selecionar **Criar**.

Digite um **alias De Oferta**. Este é o nome usado para se referir à oferta no Partner Center.

- Esse nome não é usado no mercado e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Isso não pode ser alterado depois que você selecionar **Criar**.

Depois de inserir esses dois valores, selecione **Criar** para continuar na página visão geral da Oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da Oferta** mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída satisfaz e próxima) e quanto tempo cada passo deve levar para ser concluído.

Ele inclui links para executar operações nesta oferta com base na seleção que você faz. Por exemplo:

- Se a oferta for um rascunho - [Exclua a oferta de rascunho](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se a oferta for ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta está em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não completou a assinatura do editor - [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuração da oferta

Siga estes passos para configurar sua oferta.

### <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

Ao publicar sua oferta no mercado com o Partner Center, você deve conectá-la ao seu sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém manifestar interesse ou usar seu produto.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center suporta os seguintes sistemas de CRM:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para Engajamento do Cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema de CRM não estiver listado acima, use [a Tabela Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Ponto Final https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar dados de lead do cliente. Em seguida, exporte os dados para o seu sistema de CRM.

2. Conecte sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a conexão com o destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos um lead de teste. Enquanto você visualiza a oferta antes de entrar em operação, você também pode testar sua conexão de chumbo tentando comprar a oferta você mesmo no ambiente de visualização.
4. Certifique-se de que a conexão com o destino principal permaneça atualizada para que você não perca nenhuma pista.

Aqui estão alguns recursos adicionais de gerenciamento de chumbo:

- [Visão geral do gerenciamento de líderes](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de líderes](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (certifique-se de que seu bloqueador pop-up está desligado)

Selecione **Salvar rascunho** antes de continuar para a próxima seção, Propriedades.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar sua oferta no mercado, sua versão de aplicativo e os contratos legais que suportam sua oferta.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e no máximo três categorias. Essas categorias são usadas para colocar sua oferta nas áreas de pesquisa de mercado apropriadas e são mostradas em sua página de detalhes de oferta. Na descrição da oferta, explique como sua oferta suporta essas categorias.

### <a name="industry"></a>Setor

Opcionalmente, selecione até duas indústrias e duas verticais em cada indústria. Enquanto as categorias são usadas para exibir sua oferta, a indústria e as verticais são usadas em filtros de pesquisa e são aplicadas na Vitrine. Se sua oferta tiver como alvo uma indústria específica e/ou vertical, use a descrição da oferta para explicar como sua oferta suporta as indústrias ou verticais selecionadas. Se sua oferta não&#39;específica do setor, deixe esta seção em branco.

> [!NOTE]
> À medida que trabalhamos para introduzir novas indústrias e verticais para melhorar a experiência de descoberta de ofertas, algumas indústrias ou verticais podem ainda não ser visíveis na Vitrine. Indústrias e verticais marcadas com um (*) estarão disponíveis em uma data futura. Todas as ofertas publicadas são descobertas através da pesquisa de palavras-chave.
<p>&nbsp;

| **Setor** | **Subindústria** |
| --- | --- |
| *Automotivo | *Automotivo |
| Agricultura | *Outros - Não segmentados |
| Distribuição | *Atacado<br>Envio de encomendas e pacotes |
| Educação | *Ensino<br> Superior* Ensino Fundamental e Médio / K-12<br>*Bibliotecas e Museus |
| Serviços Financeiros | *Mercado bancário<br> e de capitais* Seguro |
| Governamental | *Defesa e Inteligência (costumava ser chamada<br> de Segurança Nacional e Pública)* Segurança Pública e Justiça<br>*Governo Civil |
| Saúde (costumava ser chamada de Saúde) | *Pagador de<br> Saúde* Provedor de Saúde<br>*Farmacêuticos |
| Fabricação e Recursos (costumava ser chamado de Manufatura) | *Química e<br> Agroquímica* Fabricação Discreta<br>*Energia |
| Varejo e Bens de Consumo (costumava ser chamado de Varejo) | *Bens de<br> Consumo* Varejistas |
| *Mídia e Comunicações (costumava ser chamada de Mídia e Entretenimento) | *Mídia e<br> Entretenimento* Telecomunicações |
| Serviços Profissionais | *Legal<br> * Serviços profissionais parceiros |
| *Arquitetura e Construção (costumava ser chamada engenharia de arquitetura) | *Outros - Não segmentados |
| *Hospitalidade e Viagens | *Hotéis e<br> Lazer* Viagem e Transporte<br>*Restaurantes e Serviços de Alimentação |
| *Outras Indústrias do Setor Público | *Silvicultura<br> e Pesca* Nonprofits |
| *Imóveis | *Outros - Não segmentados |

### <a name="legal"></a>Legal

#### <a name="terms-and-conditions"></a>Termos e condições

Para fornecer seus próprios termos e condições personalizados, insira até 10.000 caracteres na caixa **Termos e condições.** Se seus termos e condições exigirem uma descrição mais longa, digite um único link da Web para onde eles podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes de tentar sua oferta.

Selecione **Salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui você definirá os detalhes da oferta que são exibidos no mercado. Isso inclui o nome da oferta, descrição, imagens e assim por diante.

### <a name="language"></a>Linguagem

Selecione o idioma no qual sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado (como nome de oferta, descrição e imagens) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> Os detalhes da oferta não são necessários para &quot;estar em inglês se a descrição da oferta começar com a frase, este aplicativo está disponível apenas em [língua não-inglesa]. &quot; Também&#39;bem fornecer um Link Útil para oferecer conteúdo em um idioma que&#39;diferente do usado na lista de ofertas.

### <a name="name"></a>Nome

O nome que você digita aqui é o título da sua oferta. Este campo está pré-preenchido com o texto que você inseriu na caixa **de alias Oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir símbolos de marca ou direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição de sua oferta. Isso pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa de sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem de marketplace.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os principais benefícios que sua oferta proporciona.
- Categorias ou associações industriais, ou ambos.
- Oportunidades de compra no aplicativo.
- Qualquer divulgação necessária.

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente o valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
  - Descrição da oferta.
  - O tipo de usuário que se beneficia da oferta.
  - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados de pesquisa.
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta fornece.
- Tente usar vocabulário específico da indústria ou redação baseada em benefícios.

Para tornar sua oferta **Descrição** mais atraente, use o rico editor de texto para formatar sua descrição. O rico editor de texto permite adicionar números, balas, negrito, itálico e recuos para tornar sua descrição mais legível.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="ilustra o editor de texto rico" border="false":::

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Digite até três palavras-chave de pesquisa opcionais para ajudar os clientes a encontrar sua oferta no mercado. Para obter melhores resultados, use também essas palavras-chave em sua descrição.

### <a name="helpprivacy-web-addresses"></a>Endereços da Web de ajuda/privacidade

Forneça links para ajudar os clientes a entender melhor sua oferta.

#### <a name="help-link"></a>Link de ajuda

Digite o endereço web onde os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Digite o endereço web na política de privacidade da sua organização. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também é responsável por postar uma política de privacidade válida em seu site.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, e-mail e número de telefone para um **contato de suporte** e um contato de **engenharia**. Essas informações não&#39;apresentadas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido aos parceiros CSP (Cloud Solution Provider).

- Contato de suporte (necessário): Para perguntas gerais de suporte.
- Contato de engenharia (obrigatório): Para questões técnicas e de certificação.
- Contato do Programa CSP (opcional): Para dúvidas de revendedor relacionadas ao programa CSP.

Na seção **de contato de suporte,** forneça o endereço web do site de **suporte** onde os parceiros podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um e até três documentos de marketing relacionados em formato PDF. Por exemplo, white papers, folhetos, checklists ou apresentações.

### <a name="marketplace-images"></a>Imagens de mercado

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato .png. As imagens embaçadas serão rejeitadas.

>[!Note]
>Se você tiver um problema de upload de arquivos, https://upload.xboxlive.com certifique-se de que sua rede local não bloqueie o serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos .png da sua oferta&#39;logotipo em dois tamanhos: **Pequeno** (48 x 48 pixels) e **Grande** (216 x 216 pixels).

Ambos os logotipos são necessários e são usados em diferentes lugares na listagem de marketplace.

#### <a name="screenshots"></a>Capturas de tela

Adicione pelo menos uma e até cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e em formato .png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram sua oferta. Digite o nome do&#39;de vídeo, seu endereço web e miniatura .png imagem do vídeo em 1280 x 720 pixels de tamanho.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

Para saber mais sobre como criar listagens de ofertas, consulte [Ofertas de práticas recomendadas](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)de listagem .

## <a name="technical-configuration"></a>Configuração técnica

Promova seu aplicativo no Power BI Service para a produção e forneça o link de instalador de aplicativos Power BI que permite que os clientes instalem seu aplicativo. Para obter mais informações, consulte [Publicar aplicativos com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Conteúdo suplementar

Forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não&#39;apresentadas aos clientes ou publicadas no mercado.

### <a name="validation-assets"></a>Ativos de validação

Opcionalmente, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua configurações típicas, contas, parâmetros ou outras informações que possam ser usadas para testar a opção Conectar dados. Essas informações são visíveis apenas para a equipe de validação e são usadas apenas para fins de validação.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de&#39;ter concluído todas as seções exigidas da oferta, você pode enviar sua oferta para revisar e publicar.

No canto superior direito do portal, selecione **Revisar e publicar**.

Na página de revisão você pode:

- Consulte o status de conclusão de cada seção da oferta. Você não pode&#39;publicar até que todas as seções da oferta estejam marcadas como completas.
  - **Não foi iniciado** - A seção ainda não foi iniciada e precisa ser concluída.
  - **Incompleto** - A seção tem erros que precisam ser corrigidos ou exige que você forneça mais informações. Consulte as seções anteriores neste documento para obter orientação.
  - **Completa** - A seção tem todos os dados necessários e não há erros. Todas as seções da oferta devem estar completas antes de você enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender sua oferta.

Para enviar a oferta para publicação, **selecione Publicar**.

Enviaremos um e-mail para que você saiba quando uma versão de pré-visualização da oferta estiver disponível para revisar e aprovar. Para publicar sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center e selecione **Go-live**.
