---
title: Criar uma oferta de aplicativo Power BI no Microsoft Commercial Marketplace
description: Saiba como criar e publicar uma oferta de aplicativo Power BI para Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839140"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Criar um aplicativo Power BI para Microsoft AppSource

Este artigo descreve como criar e publicar uma oferta de aplicativo Power BI para o Microsoft [AppSource](https://appsource.microsoft.com/).

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa comercial do Marketplace.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione > **visão geral**do **Marketplace comercial**.
3. Na página Visão geral, selecione **+ novo oferta** > **Power bi aplicativo de serviço**.

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-pbi-app.png)

> [!NOTE]
> Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em vitrines após a republicação da oferta. Certifique-se de sempre republicar depois de fazer alterações.

> [!IMPORTANT]
> Se **Power bi aplicativo de serviço** não for mostrado ou habilitado, sua conta não terá permissão para criar esse tipo de oferta. Verifique se você atendeu a todos os [requisitos](create-power-bi-app-overview.md) para esse tipo de oferta, incluindo o registro de uma conta de desenvolvedor.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e os modelos de Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1** aqui, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **criar**.

Insira um **alias de oferta**. Este é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O alias da oferta não pode ser alterado depois que você seleciona **criar**.

Selecione **criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Visão geral da oferta

Esta página mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída e próxima) e por quanto tempo cada etapa deve levar para ser concluída.

Ele inclui links para executar operações nessa oferta com base na seleção feita por você. Por exemplo: 

- Se a oferta for uma oferta de [rascunho de exclusão](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver em tempo real, [pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em visualização- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não concluiu a saída do Publicador, [cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Instalação da oferta

### <a name="connect-lead-management"></a>Conectar gerenciamento de leads

Ao publicar sua oferta no Marketplace com o Partner Center, você deve conectá-lo ao seu sistema CRM (gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar o interesse em ou usa seu produto.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center dá suporte aos seguintes sistemas CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para compromisso com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema CRM não estiver listado acima, use a [tabela do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou o [ponto de extremidade https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados do cliente potencial. Em seguida, exporte os dados para o sistema CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicar no Partner Center.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos a você um líder de teste. Ao visualizar a oferta antes de ela ficar ativa, você também pode testar sua conexão de cliente potencial tentando comprar a oferta por conta própria no ambiente de visualização.
4. Verifique se a conexão com o destino do cliente potencial permanece atualizada para que você não perca nenhum cliente potencial.

Aqui estão alguns recursos adicionais de gerenciamento de leads:

- [Visão geral do gerenciamento de Lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de Lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (verifique se o bloqueador de pop-ups está desativado)

Selecione **salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite que você defina as categorias e os setores usados para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione no mínimo um e no máximo três categorias. Essas categorias são usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas e são mostradas na página de detalhes da oferta. Na descrição da oferta, explique como sua oferta dá suporte a essas categorias.

### <a name="industry"></a>Setor

Opcionalmente, selecione até duas indústrias e duas verticais em cada setor. Embora as categorias sejam usadas para exibir sua oferta, o setor e as verticais são usados em filtros de pesquisa e são aplicados na vitrine. Se sua oferta tiver como alvo um setor específico e/ou vertical, use a descrição da oferta para explicar como sua oferta dá suporte aos setores ou verticais selecionados. Se sua oferta não for específica do setor, deixe esta seção em branco.

> [!NOTE]
> À medida que trabalhamos para apresentar novos setores e verticais para melhorar a experiência de descoberta da oferta, alguns setores ou verticais talvez ainda não estejam visíveis na vitrine. Os setores e as verticais marcados com um (*) estarão disponíveis em uma data futura. Todas as ofertas publicadas são detectáveis por meio da pesquisa de palavra-chave.
<p>&nbsp;

| **Setor** | **Submercado** |
| --- | --- |
| * Automotivo | * Automotivo |
| Agricultura | * Outros-não segmentados |
| Distribuição | * Atacado<br>Envio de pacote e remessa |
| Educação | *Ensino<br> superior* Educação principal e secundária/K-12<br>* Bibliotecas e museus |
| Serviços Financeiros | *Mercados<br> bancários e de capital* Seguros |
| Governamental | *Defesa e inteligência (usada para ser chamada de segurança nacional e pública<br> )* Segurança pública e justiça<br>* Governo civis |
| Saúde (costumava ser chamada de integridade) | *Pagador<br> de integridade* Provedor de integridade<br>* Produtos farmacêuticos |
| Fabricação e recursos (usados para serem chamados de fabricação) | *Química e Agrochemical<br> * Fabricação discreta<br>* Energia |
| Bens de varejo e de consumo (usados para serem chamados de varejo) | *Bens<br> de consumo* Varejistas |
| * Mídia e comunicações (usadas para serem chamadas de mídia e entretenimento) | *Mídia e entretenimento<br> * Telecomunicações |
| Serviços Profissionais | *Informações<br> legais* Serviços profissionais de parceiros |
| * Arquitetura e construção (usada para ser chamada de engenharia de arquitetura) | * Outros-não segmentados |
| * Hospedagem e viagem | *Hotéis e lazer<br> * Viagem e transporte<br>* Serviços de restaurantes e alimentos |
| * Outros setores do setor público | *Floresta e pesca<br> * Sem fins lucrativos |
| * Espaço real | * Outros-não segmentados |

### <a name="legal"></a>Legal

#### <a name="terms-and-conditions"></a>Termos e condições

Para fornecer seus próprios termos e condições personalizados, insira até 10.000 caracteres na caixa **termos e condições** . Se seus termos e condições exigirem uma descrição mais longa, insira um único link da Web no qual eles podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes que possam experimentar sua oferta.

Selecione **salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que são exibidos no Marketplace. Isso inclui o nome da oferta, a descrição, as imagens e assim por diante.

### <a name="language"></a>Linguagem

Selecione o idioma no qual sua oferta será listada. Atualmente, o **Inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do Marketplace (como nome da oferta, descrição e imagens) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> Os detalhes da oferta não devem estar em inglês se a descrição da oferta começar com a frase "este aplicativo está disponível somente em [idioma diferente do inglês]". Também é bom fornecer um link útil para oferecer conteúdo em um idioma diferente daquele usado na listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título da sua oferta. Esse campo é preenchido previamente com o texto inserido na caixa alias de **oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir os símbolos de marca registrada ou de direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isso pode ter até 100 caracteres e é usado nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem do Marketplace.

Inclua um ou mais dos seguintes em sua descrição:

- O valor e a chave beneficiam sua oferta.
- As associações de categoria ou da indústria, ou ambas.
- Oportunidades de compra no aplicativo.
- Quaisquer divulgações necessárias.

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

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Insira até três palavras-chave de pesquisa opcionais para ajudar os clientes a localizar sua oferta no Marketplace. Para obter melhores resultados, use também essas palavras-chave em sua descrição.

### <a name="helpprivacy-web-addresses"></a>Endereços da Web de ajuda/privacidade

Forneça links para ajudar os clientes a entenderem melhor sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira o endereço da Web onde os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira o endereço da Web para a política de privacidade da sua organização. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também é responsável por publicar uma política de privacidade válida em seu site.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone para um **contato de suporte** e um **contato de engenharia**. Essas informações não são mostradas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido para parceiros do CSP (provedor de soluções na nuvem).

- Contato de suporte (obrigatório): para perguntas de suporte geral.
- Contato de engenharia (obrigatório): para questões técnicas e problemas de certificação.
- Contato do programa CSP (opcional): para perguntas de revendedor relacionadas ao programa CSP.

Na seção **contato de suporte** , forneça o endereço Web do **site de suporte** em que os parceiros podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um e até três documentos de marketing relacionados no formato PDF. Por exemplo, White papers, folhetos, listas de verificação ou apresentações.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato. png. As imagens borradas serão rejeitadas.

>[!NOTE]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos. png do logotipo da sua oferta em dois tamanhos de pixel:
- **Pequeno** (48 x 48)
- **Grande** (216 x 216)

Ambos os logotipos são necessários e são usados em locais diferentes na listagem do Marketplace.

#### <a name="screenshots"></a>Capturas de tela

Adicione pelo menos uma e até cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e no formato. png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram sua oferta. Insira o nome do vídeo, seu endereço Web e a imagem de miniatura. png do vídeo com 1280 x 720 pixels de tamanho.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

Para saber mais sobre a criação de listagens de ofertas, consulte [práticas recomendadas de listagem de ofertas](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Configuração técnica

Promova seu aplicativo no serviço de Power BI para produção e forneça o link do instalador do aplicativo Power BI que permite que os clientes instalem seu aplicativo. Para obter mais informações, consulte [publicar aplicativos com dashboards e relatórios no Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Conteúdo complementar

Forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não são mostradas aos clientes ou publicadas no Marketplace.

### <a name="validation-assets"></a>Ativos de validação

Opcionalmente, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua definições de configuração típicas, contas, parâmetros ou outras informações que podem ser usadas para testar a opção conectar dados. Essas informações são visíveis somente para a equipe de validação e são usadas somente para fins de validação.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviar sua oferta para revisar e publicar.

No canto superior direito do portal, selecione **revisar e publicar**.

Na página revisão, você pode:

- Consulte o status de conclusão de cada seção da oferta. Não é possível publicar até que todas as seções da oferta sejam marcadas como concluídas.
  - **Não iniciado** -a seção não foi iniciada e precisa ser concluída.
  - **Incompleto** -a seção tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores neste documento para obter diretrizes.
  - **Complete** -a seção tem todos os dados necessários e não há erros. Todas as seções da oferta devem ser concluídas para que você possa enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente. Além disso, forneça notas suplementares úteis para entender sua oferta.

Para enviar a oferta para publicação, selecione **publicar**.

Enviaremos um email para que você saiba quando uma versão de visualização da oferta está disponível para revisão e aprovação. Para publicar sua oferta no público (ou se uma oferta privada, para um público privado), vá para o Partner Center e selecione **Go-Live**.
