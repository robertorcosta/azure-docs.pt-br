---
title: Criar uma oferta de máquina virtual no Azure Marketplace
description: Saiba como criar uma oferta de máquina virtual no Azure Marketplace com o SKU necessário.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 499c7e9e4ca4ab5db02c566830738f0ceb07e40d
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984642"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Criar uma oferta de máquina virtual no Azure Marketplace

Este artigo descreve como criar e publicar uma oferta de máquina virtual do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com/). Ele aborda máquinas virtuais que contêm um sistema operacional e um VHD (disco rígido virtual), tanto baseadas em Windows quanto em Linux, além de até 16 discos de dados. 

Antes de você começar, confira [Criar uma conta do marketplace comercial no Partner Center](create-account.md). Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="introduction"></a>Introdução

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Os benefícios da publicação no Azure Marketplace

Ao publicar suas ofertas no Azure Marketplace, você pode:

- Promover sua empresa, com a ajuda da marca Microsoft.
- Alcançar mais de 100 milhões usuários do Office 365 e do Dynamics 365 e mais de 200.000 organizações.
- Obtenha clientes potenciais de alta qualidade desses mercados.
- Tenha seus serviços promovidos pelas equipes de vendas de campo e televendas da Microsoft.

### <a name="before-you-begin"></a>Antes de começar

Se você ainda não fez isso, examine o [guia de publicação da oferta de máquina virtual](../marketplace-virtual-machines.md) e este material da Máquina Virtual do Azure:

- Guias de início rápido
  - [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelos de início rápido do Azure GitHub](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [VMs do Windows](../../virtual-machines/windows/tutorial-manage-vm.md)
- Exemplos
  - [Exemplos da CLI do Azure para VMs do Linux](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell para VMs do Linux](../../virtual-machines/linux/powershell-samples.md)
  - [Exemplos da CLI do Azure para VMs do Windows](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell para VMs do Windows](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Princípios básicos no conhecimento técnico

O processo de projetar, criar e testar ofertas leva tempo e exige experiência na plataforma do Azure e nas tecnologias usadas para criar a oferta.

Sua equipe de engenharia deverá ter conhecimento básico e de trabalho das seguintes tecnologias da Microsoft:

- [Serviços do Azure](https://azure.microsoft.com/services/)
- [Design e arquitetura de aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página **Visão geral**, selecione **Nova oferta** > **Máquina Virtual do Azure**.

    ![Captura de tela mostrando as opções de menu do painel esquerdo e o botão "Nova oferta".](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Depois que sua oferta for publicada, as edições feitas nela no Partner Center serão exibidas no Azure Marketplace somente depois que você republicar a oferta. Sempre republique uma oferta depois de fazer alterações nela.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço web para a oferta do Azure Marketplace e no Azure PowerShell e na CLI do Azure, se aplicável.
- Use apenas letras minúsculas e números. A ID pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. O alias da oferta é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Azure Marketplace. Ele é diferente do nome da oferta e de outros valores que são mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

### <a name="test-drive"></a>Test drive

Um *test drive* é uma ótima maneira de demonstrar sua oferta a clientes potenciais. Ele oferece a eles a opção "experimente antes de comprar", que pode ajudar a aumentar suas conversões e gerar vendas potenciais altamente qualificadas. Para obter mais informações, confira [O que é um test drive?](../what-is-test-drive.md).

Para habilitar um test drive por um período de tempo fixo, marque a caixa de seleção **Habilitar um test drive**. Para remover o test drive de sua oferta, desmarque a caixa de seleção.

Recursos adicionais de test drive:

- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing](../what-is-test-drive.md)
- [Baixe o arquivo PDF da visão geral do test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (verifique se o bloqueador de pop-ups está desativado).

### <a name="customer-leads"></a>Clientes potenciais

Quando você estiver publicando sua oferta no marketplace comercial com o Partner Center, conecte-o ao seu sistema de CRM (gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo. A conexão a um CRM será necessária se você quiser habilitar um test drive (confira a seção anterior). Caso contrário, a conexão a um CRM será opcional.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center dá suporte aos seguintes sistemas de CRM:
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se o sistema CRM não estiver listado aqui, use o [Armazenamento de Tabelas do Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou um [ponto de extremidade HTTPS](commercial-marketplace-lead-management-instructions-https.md) para armazenar os dados de clientes potenciais. Em seguida, exporte os dados para o sistema de CRM.

1. Conecte sua oferta ao destino do cliente potencial ao publicá-la no Partner Center.
1. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la no Partner Center, a Microsoft valida a conexão e envia a você um cliente potencial de teste. Quando você coloca a oferta em versão prévia antes de ela ficar ativa, pode também testar a conexão do cliente potencial tentando implantar a oferta por conta própria no ambiente de versão prévia.
1. Assegure que a conexão com o destino do cliente potencial permaneça atualizada para que você não perca nenhum cliente potencial.

1. Selecione **Salvar rascunho** para continuar.

## <a name="properties"></a>Propriedades

Na página **Propriedades** , você define as categorias que são usadas para agrupar sua oferta no Azure Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md). Ofertas de máquina virtual sempre aparecem na categoria **computação** no Azure Marketplace.

### <a name="legal"></a>Legal

Você precisa fornecer termos e condições de oferta para seus clientes. Você tem duas opções:

- **Usar seus termos e condições**

   Para fornecer seus termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **Termos e condições**. Se você precisar de uma descrição mais longa, insira um endereço web para seus termos e condições. Ele será exibido aos clientes como um link ativo.

   Os clientes precisam aceitar esses termos antes de poderem experimentar sua oferta.

- **Usar o Contrato Standard para o marketplace comercial da Microsoft**

   Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um Contrato Standard para o marketplace comercial. Quando você oferece seu software sob o Contrato Standard, os clientes precisam lê-lo e aceitá-lo apenas uma vez e você não precisa criar termos e condições personalizados.

   Use o Contrato Standard marcando a caixa de seleção **Usar o Contrato Standard para o marketplace comercial da Microsoft** e, em seguida, na janela pop-up, selecione **Aceitar** (talvez você precise rolar para cima para vê-lo).

   ![Captura de tela mostrando o painel Legal no Partner Center com a caixa de seleção "Usar o Contrato Standard para o marketplace comercial da Microsoft".](media/use-standard-contract.png)

  > [!NOTE]
  > Depois de publicar uma oferta usando o Contrato Standard para o marketplace comercial, você não pode usar seus termos e condições personalizados. Você pode oferecer uma solução sob o Contrato Standard ou seus termos e condições.

  Para obter mais informações, confira [Usar o Contrato Standard para o marketplace comercial da Microsoft](../standard-contract.md). Baixe o arquivo PDF do [Contrato Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se o bloqueador de pop-ups está desativado).

  **Aditamentos do Contrato Standard**

  Os aditamentos do Contrato Standard permitem que você selecione os termos de Contrato Standard para simplificar e criar os termos para seu produto ou negócio. Os clientes precisarão examinar os aditamentos do contrato somente se já tiverem examinado e aceitado o Contrato Standard da Microsoft. Há dois tipos de aditamentos:

  * **Aditamentos universais**: esses aditamentos são aplicados universalmente ao Contrato Standard para todos os clientes. Elas são mostradas para cada cliente da oferta no fluxo de compra. Os clientes precisam aceitar os termos do Contrato Standard e os aditamentos para usar sua oferta. Você pode fornecer um aditamento universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para os clientes no AppSource, no Azure Marketplace e/ou no portal do Azure durante o fluxo de compra e de descoberta.

  * **Aditamentos personalizados**: Esses aditamentos especiais do Contrato Standard são destinados a clientes específicos por meio de IDs de locatário do Azure. Você pode escolher o locatário que deseja usar como destino. Somente os clientes do locatário são apresentados com os termos de aditamento personalizados no fluxo de compra da oferta. Os clientes precisam aceitar os termos do Contrato Standard e os aditamentos para usar sua oferta.

    1. Comece selecionando **Adicionar termos de aditamento personalizados (máx. de dez)** . Você pode fornecer até dez termos de aditamento personalizados por oferta. Faça o seguinte:

       a. Insira seus termos de aditamento na caixa de **Termos de aditamento personalizadas**. Você pode inserir um número ilimitado de caracteres. Somente os clientes das IDs de locatário que você especificar para esses termos personalizados poderão vê-los no fluxo de compra da oferta no portal do Azure.

       b. (Obrigatório) Forneça as **IDs de locatário**. Cada aditamento personalizado pode ser direcionado para até 20 IDs de locatário. Se você adicionar um aditamento personalizado, deverá fornecer pelo menos uma ID de locatário, que identifica seu cliente no Azure. O cliente pode encontrá-lo para você no Azure selecionando **Azure Active Directory** > **Propriedades**. O valor da ID de diretório é a ID do locatário (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode encontrar a ID do locatário da organização de seu cliente usando seu endereço web de nome de domínio em [Qual é a minha ID do locatário do Microsoft Azure e do Office 365?](https://www.whatismytenantid.com/).

       c. (Opcional) Forneça uma descrição **amigável** para a ID do locatário, uma que ajude a identificar o cliente que você está direcionando para o aditamento.

        > [!NOTE]
        > Esses dois tipos de aditamento são emparelhados entre si. Os clientes aos quais os aditamentos personalizados são direcionados também terão os aditamentos universais do Contrato Standard durante a compra.

    1. Selecione **Salvar rascunho** para continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Na página **Listagem de ofertas**, você define os detalhes da oferta, como nome da oferta, descrição, links e contatos.

> [!NOTE]
> O conteúdo de listagem de sua oferta, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "este aplicativo está disponível apenas no \<non-English language> ." Você também pode fornecer uma URL para vincular a um site que oferece conteúdo em um idioma diferente daquele usado no conteúdo da listagem de ofertas.

### <a name="marketplace-details"></a>Detalhes do Marketplace

#### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente. O nome:

- Pode ser registrado como marca. Pode ter símbolos de marca registrada e de direitos autorais incluídos por você.
- Não pode conter mais de 50 caracteres.
- Não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 100 caracteres.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 256 caracteres.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira o endereço web (URL) da política de privacidade da sua organização. Verifique se a sua oferta está em conformidade com as leis e regulamentos de privacidade. Você também precisa postar uma política de privacidade válida em seu site.

### <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Para adicionar um link, selecione **Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Name**: os clientes verão o nome na página de detalhes.
- **Link (URL)** : insira um link que permita aos clientes ver seu documento online.

### <a name="customer-support-links"></a>Links de atendimento ao cliente

Forneça o site de suporte em que os clientes podem entrar em contato com sua equipe de suporte.

- Site de suporte do Azure Global
- Site de suporte do Azure Government

### <a name="partner-support-contact"></a>Contato de suporte para parceiros

Forneça informações de contato para os parceiros da Microsoft usarem quando os clientes abrirem um tíquete de suporte. Essas informações não estão listadas no Azure Marketplace.

- Nome
- Email
- Telefone

### <a name="engineering-contact"></a>Contato de engenharia

Forneça informações de contato da Microsoft para usar quando houver problemas com sua oferta, incluindo problemas com a certificação. Essas informações não estão listadas no Azure Marketplace.

- Nome
- Email
- Telefone

### <a name="azure-marketplace-media"></a>Mídia do Azure Marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

>[!NOTE]
>Se você estiver enfrentando um problema ao carregar arquivos, verifique se a sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="azure-marketplace-logos"></a>Logotipos do Azure Marketplace

Forneça arquivos PNG do logotipo da sua oferta com as quatro seguintes dimensões de imagem:

- **Pequeno** (48 &times; 48 pixels)
- **Médio** (90 &times; 90 pixels)
- **Grande** (216 &times; 216 pixels)
- **Largo** (255 &times; 115 pixels)

Todos os quatro logotipos são necessários e são exibidos em várias listagens do Azure Marketplace.

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela precisa ter 1280 &times; 720 pixels de tamanho e estar no formato PNG. Cada captura de tela precisa incluir uma legenda.

#### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstrem sua oferta. Os vídeos devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço web e uma imagem PNG em miniatura do vídeo em 1280 &times; 720 pixels.

Para obter recursos adicionais de listagem do Marketplace, confira [Práticas recomendadas para listagens de ofertas do Marketplace](../gtm-offer-listing-best-practices.md).

Selecione **Salvar rascunho** para continuar.

## <a name="preview"></a>Visualização

Selecione a guia **Versão prévia** e, em seguida, selecione um **Público-alvo de versão prévia** para validar sua oferta antes de publicá-la como ativa para um público-alvo mais amplos do marketplace comercial.

> [!IMPORTANT]
> Depois de verificar sua oferta no painel **Versão prévia**, selecione **Ativar** para publicar sua oferta para o público-alvo do marketplace comercial.

Seu público-alvo de versão prévia é identificado por GUIDs de ID da assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. É possível encontrar a ID da assinatura na página **Assinaturas** no portal do Azure.

Adicione pelo menos uma ID da assinatura do Azure, seja individualmente (até dez IDs) ou carregando um arquivo CSV (até 100 IDs). Ao adicionar essas IDs de assinatura, você define quem pode ter acesso à versão prévia de sua oferta antes que ela seja publicada como ativa. Se a oferta já estiver ativa, você ainda poderá definir um público-alvo de versão prévia para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público-alvo de versão prévia difere de um público-alvo privado. Um público-alvo de versão prévia pode acessar sua oferta _antes_ de ela ser publicada como ativa no Azure Marketplace. O público-alvo de versão prévia pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público-alvo privado depois que sua oferta for publicada integralmente no Azure Marketplace. Um público-alvo privado (definido no painel do plano **Preços e Disponibilidade**) tem acesso exclusivo a um plano específico.

Selecione **Salvar rascunho** antes de prosseguir para a próxima seção.

## <a name="plan-overview"></a>Visão geral do plano

Você pode fornecer uma variedade de opções de plano dentro da mesma oferta no Partner Center. Esses planos eram anteriormente conhecidos como SKUs. Uma oferta requer pelo menos um plano, que pode variar de acordo com o poder aquisitivo do público-alvo, a região do Azure, os recursos ou as imagens de VM.

Depois de criar seus planos, selecione a guia **Visão geral do plano** para exibir:

- Nomes de plano
- Modelos de licença
- Público-alvo (público ou privado)
- Status da publicação atual
- Ações disponíveis

As ações disponíveis no painel **Visão geral do plano** variam de acordo com o status atual do plano.

- Se o status do plano for um rascunho, selecione **Excluir rascunho**.
- Se o status do plano for publicado e estiver ativo, selecione **Interromper o plano de venda** ou **Sincronizar público-alvo privado**.

### <a name="create-a-new-plan"></a>Criar um plano

Selecione **Criar plano** na parte superior. A caixa de diálogo **Novo plano** é exibida.

Na caixa **ID do plano**, crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes no endereço Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não poderá ser alterada depois que você selecionar a opção **Criar**.

Na caixa **Nome do plano**, insira um nome para esse plano. Os clientes veem esse nome quando estão decidindo qual plano selecionar dentro de sua oferta. Crie um nome exclusivo que indica claramente as diferenças entre os planos. Por exemplo, você pode inserir os planos **Windows Server** com *Pago conforme o uso*, *BYOL*, *Avançado* e *Enterprise*.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Defina a configuração de alto nível para o tipo de plano, especifique se ele reutiliza uma configuração técnica de outro plano e identifique as regiões do Azure em que o plano deve estar disponível. Suas seleções aqui determinam quais campos são exibidos em outros painéis para o mesmo plano.

#### <a name="reuse-a-technical-configuration"></a>Reutilizar uma configuração técnica

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **Este plano reutiliza a configuração técnica de outro plano**. Essa opção permite selecionar um dos outros planos do mesmo tipo para essa oferta e permite que você reutilize a configuração técnica dele.

> [!NOTE]
> Quando você reutiliza a configuração técnica de outro plano, toda a guia de **Configuração técnica** desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações que você fizer no futuro, também serão usadas para esse plano. Essa configuração não pode ser alterada após a publicação do plano.

#### <a name="azure-regions"></a>Regiões do Azure

Seu plano precisa ser disponibilizado em pelo menos uma região do Azure.

Selecione a opção **Azure Global** para disponibilizar seu plano para clientes em todas as regiões do Azure Global que têm integração ao marketplace comercial. Para obter mais informações, confira [Disponibilidade geográfica e suporte a moeda](../marketplace-geo-availability-currencies.md).

Selecione a opção **Azure Government** para disponibilizar seu plano na região [Azure Government](../../azure-government/documentation-government-welcome.md). Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como para parceiros qualificados para atendê-los. Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar para o [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação na página de [Avaliação do Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e estiver disponível em uma região específica do Azure, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só será visível se você tiver selecionado **Azure Government** como a região do Azure na seção anterior.

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus sites. Esses links são visíveis somente para clientes do Azure Government.

Selecione **Salvar rascunho** para continuar.

### <a name="plan-listing"></a>Listagem de planos

Nesta seção, você configurará os detalhes da listagem do plano. Esse painel exibe informações específicas, que podem ser diferentes de outros planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Esse campo é preenchido com o nome que você deu ao seu plano quando o criou. Esse nome aparece no Azure Marketplace como o título deste plano. Ele é limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não da oferta. Este resumo é limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo e descreva as diferenças entre os planos em sua oferta. Descreva apenas o plano, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

Selecione **Salvar rascunho** para continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, você configura:

- Mercados em que esse plano está disponível.
- O preço por hora.
- Se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público-alvo privado).

#### <a name="markets"></a>Mercados

Cada plano precisa estar disponível em pelo menos um mercado. Marque a caixa de seleção para cada localização do mercado em que esse plano deve estar disponível para compra. (Os usuários nesses mercados ainda podem implantar a oferta em todas as regiões do Azure selecionadas na seção ["Configuração do plano"](#plan-setup).) O botão **Imposto Remetido** mostra países/regiões nos quais a Microsoft remete o imposto de vendas e uso em seu nome. A publicação na China é limitada a planos que são *Gratuitos* ou BYOL (*traga sua própria licença*).

Se você já tiver definido preços para seu plano em dólares americanos (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Examine seus preços selecionando **Exportar preços (xlsx)** depois de salvar as alterações.

Quando você remove um mercado, os clientes desse mercado que estiverem usando implantações ativas não poderão criar implantações nem escalar verticalmente as implantações existentes deles. As implantações existentes não são afetadas.

#### <a name="pricing"></a>Preços

Para o **Modelo de licença**, selecione **Plano faturado mensalmente baseado em uso** para configurar os preços para esse plano ou selecione **Traga sua própria licença** para permitir que os clientes usem esse plano com a licença existente deles.

Para um plano cobrado mensalmente baseado em uso, use uma das seguintes três opções de entrada de preços:

- **Por núcleo**: forneça preços por núcleo em US$. A Microsoft calcula o preço por tamanho de núcleo e o converte em moedas locais usando a taxa de câmbio atual.
- **Por tamanho de núcleo**: forneça preços por tamanho de núcleo em US$. A Microsoft calcula o preço e o converte em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho de núcleo**: forneça preços para cada tamanho de núcleo para todos os mercados. Você pode importar os preços de uma planilha.

> [!NOTE]
> Salve as alterações de preços para habilitar a exportação de dados sobre preços. Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente. Para garantir que os preços sejam adequados antes de publicá-los, exporte a planilha de preços e examine os preços em cada mercado.

#### <a name="free-trial"></a>Avaliação gratuita

Você pode oferecer uma *avaliação gratuita* de um ou três meses aos seus clientes.

#### <a name="visibility"></a>Visibilidade

Você pode criar cada plano para ser visível para todos ou apenas a um público-alvo previamente selecionado. Atribua associações neste público-alvo restrito usando as IDs da assinatura do Azure.

**Público**: seu plano pode ser visto por todos.

**Público-alvo privado**: torne seu plano visível apenas para um público-alvo pré-selecionado. Depois de ele ser publicado como um plano privado, você pode atualizar o público-alvo ou alterá-lo para público. Depois de você tornar um plano público, ele precisa permanecer como público. Ele não pode ser alterado de volta para um plano privado.

> [!NOTE]
> Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta _antes_ de ela ser publicada como ativa no Azure Marketplace. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

**Público-alvo restrito (IDs da assinatura do Azure)** : atribua o público-alvo que terá acesso a esse plano privado usando as IDs da assinatura do Azure. Opcionalmente, inclua uma descrição de cada ID de assinatura do Azure que você atribuiu. Adicione até dez IDs de assinatura manualmente ou até 20 mil IDs se você estiver importando uma planilha CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

>[!Note]
>Não há suporte para ofertas privadas com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).


#### <a name="hide-a-plan"></a>Ocultar um plano

Se a máquina virtual for destinada a ser usada apenas indiretamente quando referenciada por outro modelo de solução ou aplicativo gerenciado, marque essa caixa de seleção para publicar a máquina virtual, mas ocultá-la de clientes que podem estar pesquisando ou procurando por ela diretamente.

> [!NOTE]
> Os planos ocultos não são compatíveis com links de versão prévia.

Selecione **Salvar rascunho** para continuar.

### <a name="technical-configuration"></a>Configurações técnicas

Forneça as imagens e outras propriedades técnicas associadas a este plano. Para obter mais informações, confira [Criar um ativo técnico de VM do Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> A guia **Configuração técnica** não será exibida se você tiver configurado esse plano para reutilizar pacotes de outro plano na guia **Configuração do plano**.

#### <a name="operating-system"></a>Sistema operacional

No painel **Sistema operacional**, faça o seguinte:

- Para **Família de sistemas operacionais**, selecione o sistema operacional **Windows** ou **Linux**.
- Para **Versão** ou **Fornecedor**, selecione a versão do Windows ou o fornecedor do Linux.
- Para **Nome amigável do sistema operacional**, insira um nome de sistema operacional amigável. Esse nome é visível para os clientes.

#### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados para exibição no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas em uma máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

Para **Opção de implantação de disco**, selecione o tipo de implantação de disco que seus clientes podem usar para a máquina virtual. A Microsoft recomenda limitar a implantação para somente **Implantação de disco gerenciado**.

#### <a name="properties"></a>Propriedades

Para **Suporte à Rede Acelerada**, selecione se sua VM é ou não compatível com [Rede Acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens de VM

Forneça uma versão de disco e o URI de SAS (Assinatura de Acesso Compartilhado) para as imagens de máquina virtual. Adicione até 16 discos de dados para cada imagem de VM. Forneça apenas uma nova versão de imagem por plano em um envio especificado. Depois que uma imagem for publicada, você não poderá editá-la, mas poderá excluí-la. A exclusão de uma versão impede que os usuários novos e existentes implantem uma nova instância da versão excluída.

- **Versão do disco**: a versão da imagem que você está fornecendo.
- **URI da SAS**: a localização na sua conta de armazenamento do Azure em que você armazenou o VHD do sistema operacional. Para saber como obter um URI de SAS, consulte [obter URI de assinatura de acesso compartilhado para sua imagem de VM](get-sas-uri.md).
- As imagens de disco de dados também são URIs de assinatura de acesso compartilhado de VHD que são armazenadas nas respectivas contas de armazenamento do Azure.
- Adicione apenas uma imagem por envio em um plano.

Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados que a solução exige. Durante a implantação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implantação.

Selecione **Salvar rascunho** antes de continuar e retornar para **Visão geral do plano**.

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa de [CSP (provedor de soluções na nuvem)](https://azure.microsoft.com/offers/ms-azr-0145p/). Todos os planos BYOL (traga sua própria licença) são automaticamente aceitos para o programa. Você também pode optar por aceitar seus planos não BYOL.

Selecione **Salvar rascunho** para continuar.

## <a name="test-drive"></a>Test drive

Configure uma demonstração (ou *test drive*) que permite aos clientes experimentar sua oferta por um período de tempo fixo antes de comprá-la. Para criar um ambiente de demonstração para seus clientes, confira [Ofertas em test drive no marketplace comercial](test-drive.md).

Para habilitar um test drive, selecione a caixa de seleção **Habilitar um test drive** no painel **Configuração da oferta**. Para remover o test drive de sua oferta, desmarque a caixa de seleção.

Recursos adicionais de test drive:

- [Práticas recomendadas de marketing](../what-is-test-drive.md)
- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- Arquivo PDF da [visão geral do test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (verifique se o bloqueador de pop-ups está desativado)

Selecione **Salvar rascunho** para continuar.

## <a name="review-and-publish"></a>Examinar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-las para análise e publicação.

No canto superior direito, selecione **Examinar e publicar**.

Neste painel, você pode:

- Exibir o status de conclusão de cada seção da oferta:

  - **Não foi iniciado**: a seção não foi iniciada e precisa ser concluída.
  - **Não concluído**: a seção tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Para obter diretrizes sobre como atualizar as informações incompletas, confira as seções anteriores deste artigo.
  - **Completo**: A seção está concluída e não há erros. Todas as seções da oferta precisam ser concluídas para que você envie a oferta.
- Forneça **Notas para certificação** à equipe de certificação para ajudar a garantir que seu aplicativo seja testado corretamente. Inclua instruções de teste e notas suplementares que possam ajudar a equipe a compreender seu aplicativo.

Para enviar a oferta para publicação, selecione **Examinar e publicar**.

A Microsoft enviará uma mensagem de email para que você saiba quando uma versão prévia da oferta está disponível para análise e aprovação. Para publicar sua oferta ao público (ou, se for uma oferta privada, publicá-la para um público-alvo privado), vá para o Partner Center, acesse a página **Visão geral** e, em seguida, selecione **Ativar**. O status de sua oferta é exibido na parte superior da página quando a publicação está em andamento.

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa de **Validação manual** no processo de publicação representa uma análise extensiva de sua oferta e dos ativos técnicos associados. Se esse processo descobrir erros com sua oferta, você receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e republicar sua oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página **Visão geral da oferta** mostra uma representação visual das etapas, tanto concluídas quanto em andamento, que são necessárias para publicar sua oferta, bem como o período de tempo que cada etapa deve levar para ser concluída.

Esta página também inclui links para ajudá-lo a trabalhar com a oferta, dependendo do status dela:

- Se a oferta for um rascunho: [excluir oferta de rascunho](update-existing-offer.md#delete-a-draft-offer))
- Se a oferta estiver ativa: [parar de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan))
- Se a oferta estiver em versão prévia: [Go-Live](publishing-status.md#publisher-approval))
- Se você ainda não concluiu a saída do Publicador: [Cancelar publicação](update-existing-offer.md#cancel-publishing))

## <a name="marketplace-examples"></a>Exemplos do Marketplace

Aqui está um exemplo de como as informações de oferta são exibidas no Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo grande
2. Price
3. Categorias
4. Termos e condições
5. Endereço de política de privacidade (link)
6. Nome da oferta
7. Descrição
8. Links úteis
9. Capturas de tela/vídeos

<br>Aqui está um exemplo de como as informações de oferta são exibidas nos resultados da pesquisa do Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Ilustra como essa oferta aparece nos resultados da pesquisa do Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa
4. Avaliação

<br>Aqui está um exemplo de detalhes do plano do Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Ilustra os detalhes do plano do Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome e Resumo do plano
2. Tamanhos de VM recomendados
3. Planejar preços

<br>Aqui está um exemplo de como as informações de oferta são exibidas no portal do Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Ilustra como essa oferta aparece na portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome
2. Descrição
3. Links úteis
4. Capturas de tela/vídeos

<br>Aqui está um exemplo de como as informações de oferta são exibidas nos resultados da pesquisa portal do Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Ilustra como essa oferta aparece nos portal do Azure resultados da pesquisa.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa

<br>Aqui está um exemplo dos detalhes do plano de portal do Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Ilustra os detalhes do plano de portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome do plano
2. Descrição do plano

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no marketplace comercial](update-existing-offer.md)
