---
title: Crie uma oferta de módulo Azure IoT Edge com o Partner Center - Azure Marketplace
description: Saiba como criar uma oferta de módulo IoT Edge no Azure Marketplace usando o Partner Center
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6b56b748ef31bcfd33893e55d3ea5f8d9851a3ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674469"
---
# <a name="create-an-iot-edge-module-offer"></a>Criar uma oferta de módulo do IoT Edge

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas do seu módulo IoT Edge do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções no [módulo IoT Edge, ofereça uma visão geral da publicação](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) para o Cloud Partner Portal gerenciar suas ofertas.

Este artigo descreve como criar e publicar uma oferta de módulo edge de Internet das Coisas (IoT) para o Azure Marketplace.

Antes de criar uma oferta de módulo IoT Edge, você deve ter uma conta de mercado comercial no Partner Center. Se você ainda não criou um, consulte [Criar uma conta de mercado comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Conecte-se ao Partner Center.
2. No menu de navegação à esquerda, selecione Visão geral **do mercado** > **comercial**.

    ![Ilustra o menu de navegação à esquerda.](./media/cs-menu-overview.png)

3. Selecione **+ Nova oferta** > **módulo IoT Edge**. A caixa de diálogo **Nova oferta** é exibida.

> [!IMPORTANT]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas vitrines após a republicação da oferta. Certifique-se de sempre republicar depois de fazer alterações.

### <a name="offer-id-and-alias"></a>Oferecer ID e alias

Digite um **ID de Oferta**. Este é um identificador único para cada oferta em sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de marketplace e modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e é limitado a 50 caracteres. Por exemplo, se você inserir **test-offer-1,** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .
- O ID de oferta não pode ser alterado depois de selecionar Criar.

Digite um **alias De Oferta**. Este é o nome usado para se referir à oferta no Partner Center.

- Esse nome não é usado no mercado e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Isso não pode ser alterado depois que você selecionar **Criar**.

Depois de inserir esses dois valores, selecione **Criar** antes de continuar para a próxima página, 'Oferta' visão geral.

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da Oferta** mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída satisfaz e próxima) e quanto tempo cada passo deve levar para ser concluído.

Esta página inclui links para executar operações nesta oferta com base na seleção que você faz. Por exemplo:

- Se a oferta for um rascunho - [Exclua a oferta de rascunho](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se a oferta for ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta está em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não completou a assinatura do editor - [Cancele a publicação.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuração da oferta

Siga estes passos para configurar sua oferta.

### <a name="connect-lead-management"></a>Conecte o gerenciamento de chumbo

Ao publicar sua oferta no mercado com o Partner Center, você pode conectá-la opcionalmente ao seu sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém manifestar interesse ou usar seu produto.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center suporta os seguintes sistemas de CRM:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para Engajamento do Cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema de CRM não estiver listado acima, use [a Tabela Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Ponto Final https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar dados de lead do cliente e exporte os dados para o sistema de CRM.

2. Conecte sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a conexão com o destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos um lead de teste. Enquanto você visualiza a oferta antes de entrar em operação, você também pode testar sua conexão de chumbo tentando comprar a oferta você mesmo no ambiente de visualização.
4. Certifique-se de que a conexão com o destino principal permaneça atualizada para que você não perca nenhuma pista.

Aqui estão alguns recursos adicionais de gerenciamento de chumbo:

- [Visão geral do gerenciamento de líderes](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de líderes](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o bloqueador pop-up está desligado).

Selecione **Salvar rascunho** antes de continuar para a próxima seção, Propriedades.

### <a name="properties"></a>Propriedades

Esta página permite definir as categorias usadas para agrupar sua oferta no mercado e os contratos legais que suportam sua oferta.

#### <a name="category"></a>Categoria

Selecione um mínimo de uma e no máximo cinco categorias. Essas categorias são usadas para colocar sua oferta nas áreas de pesquisa de mercado apropriadas e são mostradas em sua página de detalhes de oferta. Na descrição da oferta, explique como sua oferta suporta essas categorias. Nas páginas de navegação, todos os módulos IoT Edge são mostrados na categoria de  **módulo si> IoT Edge.**

#### <a name="legal"></a>Legal

Você deve fornecer termos e condições para a oferta. Você tem duas opções:

- Use o Contrato Padrão para o Microsoft Commercial Marketplace.
- Forneça seus próprios termos e condições.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o Microsoft Commercial Marketplace

Oferecemos um modelo de Contrato Padrão para ajudar a facilitar transações no mercado comercial. Você pode optar por oferecer sua solução sob o Contrato Padrão, que os clientes só precisam verificar e aceitar uma vez. Esta é uma boa opção se você não quiser criar termos e condições personalizados.

Para saber mais sobre o Contrato Padrão, consulte [Contrato Padrão para o Microsoft Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). Você também pode baixar o [Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (certifique-se de que seu bloqueador pop-up está desligado).

Para usar o Contrato Padrão, selecione o **Usar o Contrato Padrão para a** caixa de seleção de mercado comercial da Microsoft e, em seguida, clique em **Aceitar**.

> [!NOTE]
> Depois de publicar uma oferta usando o contrato Standard para o mercado comercial da Microsoft, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o Contrato Padrão ou sob seus próprios termos e condições.

![Ilustra usando o Contrato Padrão para a caixa de seleção de mercado comercial da Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer seus próprios termos e condições personalizados, insira-os na caixa **Termos e Condições.** Você pode inserir uma quantidade ilimitada de caracteres de texto nesta caixa. Os clientes devem aceitar estes termos antes de tentar sua oferta.

Selecione **Salvar rascunho** antes de continuar para a próxima seção, lista de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui você definirá os detalhes da oferta que são exibidos no mercado. Isso inclui o nome da oferta, descrição, imagens e assim por diante. Certifique-se de seguir as políticas detalhadas na página de política da Microsoft enquanto configura esta oferta.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em inglês se a descrição da oferta começar com a frase: "Este aplicativo está disponível apenas em [língua não-inglesa]." Também é bom fornecer um Link útil para oferecer conteúdo em um idioma diferente do usado nos detalhes de listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digita aqui é o título da sua oferta. Este campo está pré-preenchido com o texto que você inseriu na caixa **de alias Oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir símbolos de marca ou direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição de sua oferta. Isso pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais detalhada de sua oferta. Isso pode ter até 256 caracteres e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa de sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem de marketplace.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os principais benefícios que sua oferta oferece
- Categorias ou associações industriais, ou ambos
- Oportunidades de compra no aplicativo
- Quaisquer divulgações necessárias

As ofertas do módulo IoT Edge devem incluir um parágrafo mínimo de requisitos de hardware na parte inferior da descrição. Por exemplo:

*Requisitos mínimos de hardware: Linux x64 e arm32 OS, 1 GB de RAM, 500 Mb de armazenamento*

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente o valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
    - Descrição da oferta.
    - O tipo de usuário que se beneficia da oferta.
    - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados de pesquisa.
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que sua oferta fornece.
- Tente usar vocabulário específico da indústria ou redação baseada em benefícios.

Para tornar sua oferta **Descrição** mais atraente, use o rico editor de texto para formatar sua descrição. O rico editor de texto permite adicionar números, balas, negrito, itálico e recuos para tornar sua descrição mais legível.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustra o rico editor de texto." border="false":::

- Para alterar o formato do seu conteúdo, destaque o texto que deseja formatar e selecionar um estilo de texto, conforme mostrado nesta captura de tela:

     :::image type="content" source="media/text-editor3.png" alt-text="Ilustra o controle de estilo de texto no rico editor de texto." border="false":::

- Para adicionar uma lista com uma numeração ou numerada ao texto, use as opções mostradas nesta captura de tela:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Ilustra os controles de lista de números e balas no rico editor de texto." border="false":::

- Para adicionar ou remover o recuo ao texto, use as opções mostradas nesta captura de tela:

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustra os controles de recuo no rico editor de texto." border="false":::

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Digite o endereço web da política de privacidade da sua organização. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também é responsável por postar uma política de privacidade válida em seu site.

#### <a name="useful-links"></a>Links úteis

Forneça documentos on-line complementares sobre sua oferta. Você pode adicionar até 25 links. Para adicionar um link, selecione **+ Adicione um link** e, em seguida, complete os seguintes campos:

- **Título** - Os clientes verão o título na página de detalhes da sua oferta.
- **Link (URL)** - Insira um link para que os clientes visualizem seu documento on-line. O link deve começar com http:// ou https://.

Certifique-se de adicionar pelo menos um link à sua documentação e um link para os dispositivos IoT Edge compatíveis do catálogo de [dispositivos Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, e-mail e número de telefone para um **contato de suporte** e um contato de **Engenharia.** Essas informações não são mostradas aos clientes. Ele está disponível para a Microsoft e pode ser fornecido aos parceiros CSP (Cloud Solution Provider).

- Contato de suporte (necessário): Para perguntas gerais de suporte.
- Contato de engenharia (obrigatório): Para questões técnicas e de certificação.
- Contato do Programa CSP (opcional): Para dúvidas de revendedor relacionadas ao programa CSP.

Na seção **de contato de suporte,** forneça o endereço web do site de **suporte** onde os parceiros podem encontrar suporte para sua oferta com base em se a oferta está disponível no Azure global, No Governo do Azure ou ambos.

Na seção **de contato do Programa CSP,** forneça o link (**CSP Program Marketing Materials**) onde os parceiros csp podem encontrar materiais de marketing para sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de marketplace

Para saber mais sobre como criar listagens de ofertas, consulte [Ofertas de práticas recomendadas](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)de listagem .

### <a name="marketplace-images"></a>Imagens de mercado

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato .png. As imagens embaçadas serão rejeitadas.

#### <a name="store-logos"></a>Logotipos da Loja

Forneça arquivos .png do logotipo da sua oferta em cada um dos seguintes tamanhos de quatro pixels:

- **Pequeno (48 x 48)**
- **Médio (90 x 90)**
- **Grande (216 x 216)**
- **Largo (255 x 115)**

Todos os quatro logotipos são necessários e são usados em diferentes lugares na listagem de marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione cinco capturas de tela que mostram como sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e em formato .png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram sua oferta. Digite o nome do vídeo, seu endereço web e uma imagem em miniatura .png do vídeo com 1280 x 720 pixels de tamanho.

#### <a name="offer-examples"></a>Exemplos de oferta

Os exemplos a seguir mostram como os campos de listagem de ofertas aparecem em diferentes locais da oferta.

Esta captura de tela mostra a página **de listagem de ofertas** no Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Ilustra a página de listagem de ofertas no Azure Marketplace.":::

Esta captura de tela mostra os resultados da pesquisa no Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Ilustra os resultados da pesquisa no Azure Marketplace.":::

Esta captura de tela mostra a página **de listagem de ofertas** no portal Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Ilustra a página de listagem de ofertas no portal Azure.":::

Esta captura de tela mostra os resultados da pesquisa no portal Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Ilustra a página de listagem de ofertas no portal Azure.":::

Selecione **Salvar rascunho** antes de prosseguir para a próxima seção, Visualizar.

## <a name="preview"></a>Visualização

Na **guia Pré-visualização,** você pode escolher uma audiência de **visualização** limitada para validar sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de ver sua oferta no Preview, você deve selecionar **Ir ao vivo** para publicar sua oferta ao público.

Especifique seu público de visualização usando GUIDs de assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes.

> [!NOTE]
> Você pode encontrar seu ID de assinatura do Azure na página assinaturas no portal Azure.

Adicione pelo menos um ID de assinatura do Azure, individualmente (até 10) ou carregando um arquivo CSV (até 100). Adicionando esses IDs de assinatura, você define quem pode visualizar sua oferta antes de ser publicada ao vivo. Se sua oferta já estiver ao vivo, você pode definir uma audiência de pré-visualização para testar alterações ou atualizações à sua oferta.

> [!NOTE]
> O público de pré-visualização difere de uma audiência privada. Um público **de pré-visualização** pode ver e confirmar todos os planos de oferta antes de estarem ao vivo no mercado, incluindo aqueles que serão publicados apenas para um público **privado** (definido na guia Disponibilidade).

Selecione **Salvar rascunho** antes de prosseguir para a próxima seção, visão geral do Plano.

### <a name="plan-overview"></a>Visão geral do plano

Esta guia permite que você forneça diferentes opções de plano dentro da mesma oferta no Partner Center. Esses planos, antes eram chamados de SKUs, ou unidades de manutenção de estoque. Os planos podem diferir em termos de quais nuvens estão disponíveis, como nuvens globais, nuvens governamentais e a imagem referenciada pelo plano. Para listar sua oferta no mercado, você deve configurar pelo menos um plano.

Depois de criar seus planos, a guia **'Visão geral do Plano'** mostra:

- Nomes do plano
- Modelo de preços
- Disponibilidade em nuvem (Global ou Governo)
- Status de publicação atual
- Quaisquer ações disponíveis

As ações disponíveis na visão geral do Plano variam dependendo do status atual do seu plano. Elas incluem:

- **Exclua rascunho**: Se o status do plano for um Rascunho.
- **Plano de venda :** Se o status do plano for publicado ao vivo.

#### <a name="create-new-plan"></a>Criar um novo plano

Selecione **Criar novo plano**. A caixa de diálogo **Novo plano** é exibida.

Na caixa **Plan ID,** crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Use apenas letras e números minúsculos, traços ou sublinhados, e um máximo de 50 caracteres.

Na caixa **nome do Plano,** digite um nome para este plano. Os clientes veem esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome único para cada plano nesta oferta. Por exemplo, você pode usar um nome de oferta do **Windows Server** com planos Windows **Server 2016** e **Windows Server 2019**.

> [!NOTE]
> O ID do plano não pode ser alterado depois que você selecionar **Criar**.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Esta guia permite configurar em quais nuvens o plano está disponível. Suas respostas nesta guia afetam quais campos são exibidos em outras guias.

#### <a name="cloud-availability"></a>Disponibilidade de nuvem

Seu plano deve estar disponível em pelo menos uma nuvem usando o Azure IoT Hub.

Selecione a opção **Azure Global** para que seu plano possa ser usado pelos clientes em todas as regiões globais do Azure que usam o marketplace. Para obter detalhes, consulte [disponibilidade geográfica e suporte à moeda](https://aka.ms/AzureGovCurrencies).

Selecione a opção [Nuvem do Governo do Azure](https://aka.ms/WhatIsAzureGovernment) para fazer sua solução aparecer aqui. Esta é uma nuvem comunitária do governo com acesso controlado para clientes de agências governamentais federais, estaduais e locais ou tribais dos EUA, bem como parceiros elegíveis para atendê-los. Como editor, você é responsável por quaisquer controles de conformidade, medidas de segurança e práticas recomendadas para esta comunidade de nuvem. O governo azure usa data centers e redes fisicamente isolados (localizados apenas nos EUA). Antes [de publicar](https://aka.ms/azuregovpublish) para o Governo do Azure, teste e confirme sua solução dentro dessa área, pois os resultados podem ser diferentes. Para finalizar e testar sua solução, solicite uma conta de teste do teste do [Governo do Microsoft Azure](https://aka.ms/AzureGovernmentTrial).

> [!NOTE]
> Depois que seu plano é publicado e disponível em uma nuvem específica, você não pode remover essa nuvem.

#### <a name="azure-government-cloud-certifications"></a>Certificações de Nuvem do Governo do Azure

Essa opção só será visível se **o Azure Government Cloud** for selecionado sob disponibilidade na **Nuvem**.

Os serviços do governo azure lidam com dados que estão sujeitos a certas regulamentações e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para trazer consciência às suas certificações para esses programas, você pode fornecer até 100 links que descrevem suas certificações. Estes podem ser links para suas listagens no programa diretamente ou em seu próprio site. Esses links são visíveis apenas para clientes do Governo Azure.

## <a name="plan-listing"></a>Listagem de planos

Esta guia exibe informações específicas para cada plano diferente dentro da mesma oferta.

### <a name="plan-name"></a>Nome do plano

Isso está pré-preenchido com o nome que você deu ao seu plano quando o criou. Você pode mudar este nome, conforme necessário. Pode ter até 50 caracteres. Este nome aparece como o título deste plano no Azure Marketplace e portal Azure. É usado como o nome padrão do módulo depois que o plano estiver pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo aparece nos resultados de pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano único, bem como diferenças entre planos dentro de sua oferta. Não descreva a oferta, só o plano. Esta descrição aparecerá no Azure Marketplace e no portal Azure na página de listagem de Ofertas. Pode ser o mesmo conteúdo que você forneceu no resumo do plano e contém até 2.000 caracteres.

Selecione **Salvar rascunho** depois de concluir esses campos.

#### <a name="plan-examples"></a>Exemplos de planos

Os exemplos a seguir mostram como os campos de listagem de planos aparecem em diferentes pontos de vista.

Estes são os campos no Azure Marketplace ao visualizar detalhes do plano:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Ilustra os campos que você vê ao visualizar detalhes do plano no Azure Marketplace.":::

Estes são os detalhes do plano no portal Azure:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Ilustra detalhes do plano no portal Azure.":::

## <a name="availability"></a>Disponibilidade

Se você quiser ocultar sua oferta publicada para que os clientes não possam pesquisar, navegar ou comprá-la no mercado, selecione a caixa de seleção **ocultar plano** na guia Disponibilidade.

Este campo é comumente usado quando:

- A oferta destina-se a ser usada apenas indiretamente quando referenciada por outro aplicativo.
- A oferta não deve ser comprada individualmente.
- O plano foi usado para testes iniciais e não é mais relevante.
- O plano foi utilizado para ofertas temporárias ou sazonais e não deve mais ser oferecido.

## <a name="technical-configuration"></a>Configuração técnica

O tipo de **oferta do módulo IoT Edge** é um tipo específico de contêiner que é executado em um dispositivo IoT Edge. Na guia **Configuração Técnica,** você fornecerá informações de referência para o repositório de imagem do contêiner dentro do Registro de Contêineres do [Azure,](https://aka.ms/ContainerRegistry)juntamente com configurações que permitem que os clientes usem o módulo facilmente.

Depois que a oferta é publicada, sua imagem de contêiner IoT Edge é copiada para o Azure Marketplace em um registro de contêiner público específico. Todas as solicitações dos usuários do Azure para usar seu módulo são atendidas a partir do registro de contêineres públicos do Azure Marketplace, não do seu registro privado de contêineres.

Você pode segmentar várias plataformas e fornecer várias versões da imagem do contêiner do módulo usando tags. Para saber mais sobre tags e versionamentos, consulte [Prepare seus ativos técnicos do módulo IoT Edge](https://aka.ms/AzureIoTTechAsset).

### <a name="image-repository-details"></a>Detalhes do repositório de imagens

Você fornecerá as seguintes informações na guia Detalhes do **repositório de imagens.**

**Selecione a fonte da imagem**: Selecione a opção Registro de contêiner do **Azure.**

**ID de assinatura do Azure**: Forneça o ID de assinatura onde o uso de recursos é relatado e os serviços são cobrados para o Registro de Contêineres do Azure que inclui a imagem do contêiner. Você pode encontrar este ID na [página assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal Azure.

**Nome do grupo de recursos do Azure**: Forneça o nome do [grupo de recursos](https://aka.ms/ResourceManagerAzurePortal) que contém o Registro de Contêiner do Azure com a imagem do contêiner. O grupo de recursos deve estar acessível no ID de assinatura (acima). Você pode encontrar o nome na página [de grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) no portal Azure.

**Nome do registro do contêiner Azure**: Forneça o nome do Registro de [Contêineres do Azure](https://aka.ms/DockerContainerRegistriesAzure) que tem a imagem do contêiner. O registro de contêineres deve estar presente no grupo de recursos do Azure que você forneceu anteriormente. Forneça apenas o nome do registro, não o nome do servidor de login completo. Certifique-se de omitir **azurecr.io** do nome. Você pode encontrar o nome do registro na [página Registros de Contêineres](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) no portal Azure.

Nome de usuário do Administrador **para o Registro de Contêineres do Azure**: Forneça o nome de usuário do [administrador](https://aka.ms/AdminAccountContainerRegistry) associado ao Registro de Contêineres do Azure que tem a imagem do contêiner. O nome de usuário e a senha são necessários para garantir que sua empresa tenha acesso ao registro. Para obter o nome de usuário e a senha do administrador, defina a propriedade **habilitada para administrador** **estonteá-la como True** usando a Interface de Linha de Comando do Azure (CLI). Você pode opcionalmente definir **o usuário do Administrador** para **habilitar** no portal Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Ilustra a caixa de diálogo Atualizar registro de contêiner.":::

**Senha para o Registro de Contêineres do Azure**: Forneça a senha para o nome de usuário do administrador que está associado ao Registro de Contêineres do Azure e tem a imagem do contêiner. O nome de usuário e a senha são necessários para garantir que sua empresa tenha acesso ao registro. Você pode obter a senha do portal Azure indo para As Chaves de Acesso **ao Registro** > de**Contêineres** ou com o Azure CLI usando o [comando show.](https://aka.ms/azacrcredentialshow)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Ilustra a tela-chave de acesso no portal Azure.":::

**Nome do repositório no Registro de Contêineres do Azure**. Forneça o nome do repositório do Registro de Contêineres Do Azure que tem sua imagem. Você especifica o nome do repositório quando você empurra a imagem para o registro. Você pode encontrar o nome do repositório indo para a**página Repositórios** [de Registro](https://aka.ms/ContainerRegistry) > de Contêiner . Para obter mais informações, consulte [Ver repositórios de registro de contêineres no portal Azure](https://aka.ms/ContainerRegistryRepositoriesAzure). Note que depois que o nome é definido, ele não pode ser alterado. Use um nome único para cada oferta em sua conta.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tags de imagem para novas versões de sua oferta

Os clientes devem ser capazes de receber automaticamente atualizações do Azure Marketplace quando você publicar uma atualização. Se eles não querem atualizar, eles devem ser capazes de ficar em uma versão específica da sua imagem. Você pode fazer isso adicionando novas tags de imagem cada vez que você fizer uma atualização para a imagem.

**Tag de imagem**. Este campo deve incluir uma **tag mais recente** que aponta para a versão mais recente de sua imagem em todas as plataformas suportadas. Ele também deve incluir uma tag de versão (por exemplo, começando com xx.xx.xx, onde xx é um número). Os clientes devem usar [tags de manifesto](https://aka.ms/GitHubmanifest-tool) para segmentar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las. Todas as tags de manifesto (exceto a última tag) devem começar com X.Y- ou X.Y.Z- onde X, Y e Z são inteiros. Por exemplo, se uma tag mais recente aponta para 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, essas seis tags precisam ser adicionadas a este campo. Para obter detalhes sobre tags e versionamento, consulte [Prepare seus ativos técnicos do módulo IoT Edge.](https://aka.ms/PrepareIoTEdgeModTechAssets)

### <a name="default-deployment-settings-optional"></a>Configurações de implantação padrão (opcional)

Defina as configurações mais comuns para implantar seu módulo do IoT Edge. Otimize as implantações dos clientes, permitindo que eles iniciem seu módulo IoT Edge fora da caixa com essas configurações padrão.

**Rotas padrão**. O IoT Edge Hub gerencia a comunicação entre módulos, o IoT Hub e dispositivos. Você pode definir rotas para entrada e saída de dados entre módulos e o IoT Hub, o que lhe dá a flexibilidade de enviar mensagens para onde eles precisam ir sem a necessidade de serviços adicionais para processar mensagens ou escrever código adicional. As rotas são construídas usando pares de nome/valor. Você pode definir até cinco nomes de rota padrão, cada um com até 512 caracteres de comprimento.

Certifique-se de usar a [sintaxe](https://aka.ms/DeclareRoutesAzureIoT) de rota correta no valor da rota (geralmente definida como FROM/message/* INTO $upstream). Isso significa que todas as mensagens enviadas por quaisquer módulos vão para o seu Hub IoT. Para consultar o seu módulo, use o nome padrão do módulo, que será o **seu Nome de Oferta,** sem espaços ou caracteres especiais. Para consultar outros módulos que ainda não são conhecidos, use o <FROM_MODULE_NAME convenção> para informar seus clientes que eles precisam atualizar essas informações. Para obter detalhes sobre as rotas do IoT Edge, consulte [As rotas Declare](https://aka.ms/DeclareRoutesAzureIoT).

Por exemplo, se o módulo ContosoModule ouvir entradas no ContosoInput e dados de saída no ContosoOutput, faz sentido definir as duas rotas padrão a seguir:

- Nome #1: ToContosoModule
- Valor #1: DE /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nome #2: FromContosoModuleToCloud
- Valor #2: DE /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Módulo padrão duas propriedades desejadas**. Um módulo gêmeo é um documento JSON no IoT Hub que armazena as informações do estado para uma instância de módulo, incluindo as propriedades desejadas. As propriedades desejadas são usadas juntamente com as propriedades relatadas para sincronizar a configuração ou as condições do módulo. O backend da solução pode definir as propriedades desejadas e o módulo pode lê-las. O módulo também pode receber notificações de alteração nas propriedades desejadas. As propriedades desejadas são criadas usando até cinco pares de nome/valor e cada valor padrão deve ser inferior a 512 caracteres. Você pode definir até cinco propriedades desejadas por gêmeos nome/valor. Os valores das propriedades desejadas por gêmeos devem ser JSON válidos, não-escapados, sem matrizes com uma hierarquia máxima aninhada de quatro níveis. Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), você pode adicionar um parâmetro como o valor padrão. Para saber mais sobre as propriedades desejadas por gêmeos, consulte [Definir ou atualizar as propriedades desejadas](https://aka.ms/DefineUpdateProperties).

Por exemplo, se um módulo suporta uma taxa de atualização dinamicamente configurável usando propriedades duplas desejadas, faz sentido definir a seguinte propriedade padrão desejada:

- Nome #1: Taxa de atualização
- Valor #1: 60

**Variáveis de ambiente padrão**. As variáveis de ambiente fornecem informações suplementares a um módulo que está ajudando no processo de configuração. As variáveis de ambiente são criadas usando pares de nome/valor. Cada nome e valor de variável de ambiente padrão deve ter menos de 512 caracteres, e você pode definir até cinco. Quando um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), você pode adicionar um parâmetro como o valor padrão.

Por exemplo, se um módulo precisar aceitar termos de uso antes de ser iniciado, será possível definir a seguinte variável de ambiente:

- Nome #1: ACCEPT_EULA
- valor #1: Y

**Opções padrão de criação de contêiner**. As opções de criação de contêineres direcionam a criação do contêiner Docker do módulo IoT Edge. O IoT Edge suporta opções de API do mecanismo Docker Criar contêiner. Veja todas as opções em [Contêineres list.](https://aka.ms/ContainerList) O campo de opções de criação deve ser válido JSON, não-escapou e menos de 512 caracteres.

Por exemplo, se um módulo exigir a vinculação da porta, defina as seguintes opções de criação:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}}}

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode submetê-la para revisar e publicar.

No canto superior direito do portal, selecione **Revisar e publicar**.

Na página de revisão você pode ver o status da publicação:

- Consulte o status de conclusão de cada seção da oferta. Você não pode publicar até que todas as seções da oferta estejam marcadas como completas.
    - **Não foi iniciado** - A seção ainda não foi iniciada e precisa ser concluída.
    - **Incompleto** - A seção tem erros que precisam ser corrigidos ou exige que você forneça mais informações. Consulte as seções anteriores neste documento para obter orientação.
    - **Completa** - A seção tem todos os dados necessários e não há erros. Todas as seções da oferta devem estar completas antes de você enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que sua oferta seja testada corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender sua oferta.

Para enviar a oferta para publicação, **selecione Publicar**.

Enviaremos um e-mail para que você saiba quando uma versão de pré-visualização da oferta estiver disponível para revisar e aprovar. Para publicar sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center e selecione **Go-live**.

## <a name="next-steps"></a>Próximas etapas

- [Atualize uma oferta existente no mercado comercial](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)