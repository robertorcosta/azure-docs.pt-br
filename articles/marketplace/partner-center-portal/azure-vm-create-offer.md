---
title: Criar uma oferta de máquina virtual do Azure-Azure Marketplace
description: Saiba como criar uma oferta de máquina virtual no Marketplace comercial.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869054"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Criar uma oferta de máquina virtual do Azure

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de VM do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, continue seguindo as instruções em [criar oferta de máquina virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) no portal do Cloud Partner para gerenciar suas ofertas.

Este artigo descreve como criar e publicar uma oferta de máquina virtual do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com/). Ele trata máquinas virtuais baseadas em Windows e Linux que contêm um sistema operacional, disco rígido virtual (VHD) e até 16 discos de dados.

## <a name="introduction"></a>Introdução

### <a name="publishing-benefits"></a>Benefícios da publicação

A publicação no Azure Marketplace tem os seguintes benefícios:

- Promova sua empresa usando a marca da Microsoft
- Alcance mais de 100 milhões usuários do Office 365 e do Dynamics 365 e mais de 200.000 organizações por meio do Azure Marketplace
- Obtenha leads de alta qualidade desses Marketplaces
- Obtenha seus serviços promovidos pelas equipes de campo e televendas da Microsoft

### <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver feito isso, examine o [Guia de publicação da oferta de máquina virtual](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e este material de máquina virtual do Azure:

- Guias de início rápido
  - [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelos de Início Rápido do Azure GitHub](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [VMs Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Exemplos
  - [Exemplos de CLI do Azure para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Exemplos de CLI do Azure para VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell para VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Conceitos básicos no conhecimento técnico

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta.

Sua equipe de engenharia deve compreender as seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Criar uma oferta de máquina virtual do Azure

Para poder criar uma oferta de máquina virtual do Azure, você deve ter uma conta do Marketplace comercial no Partner Center. Se você ainda não tiver criado um, consulte [criar uma conta do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home)e, no menu superior, selecione **painel**.
2. Na barra de navegação à esquerda, selecione **Marketplace comercial**e, em seguida, **visão geral**.
3. Na página **visão geral** , selecione **+ nova oferta**e, em seguida, **máquina virtual do Azure**. A caixa de diálogo **nova oferta** é exibida.

    ![Ilustra a página de visão geral no Partner Center com o botão nova oferta e a oferta de máquina virtual do Azure selecionada.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>ID da oferta e alias

Insira uma **ID de oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e em Azure PowerShell e CLI do Azure, se aplicável.
- Use apenas letras minúsculas e números. Ele pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se você inserir **Test-offer-1** aqui, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **criar**.

Insira um **alias de oferta**. Este é o nome usado para a oferta no Partner Center. Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.

Selecione **criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Instalação da oferta

### <a name="test-drive"></a>Test drive

Configure uma demonstração (test drive) que permite aos clientes experimentar sua oferta antes de adquiri-la. Para criar um ambiente de demonstração que permita aos clientes experimentar sua oferta por um período de tempo fixo, consulte [testar sua oferta no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para habilitar uma test drive, marque a caixa de seleção **habilitar um test drive** . Para remover test drive de sua oferta, desmarque essa caixa de seleção.

Recursos de test drive adicionais:

- [Práticas recomendadas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral das unidades de teste](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (verifique se o bloqueador de pop-ups está desativado).

### <a name="lead-management"></a>Gerenciamento de clientes potenciais

Ao publicar sua oferta no Marketplace comercial com o Partner Center, conecte-o ao seu sistema de gerenciamento de relacionamento com o cliente (CRM). Isso permite que você receba informações de contato do cliente assim que alguém expressar o interesse em ou usa seu produto. A conexão com um CRM será necessária se você habilitar o **Test Drive** (consulte a seção anterior), caso contrário, será opcional.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center dá suporte aos seguintes sistemas CRM:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para compromisso com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema CRM não estiver listado acima, use a [tabela do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou o [ponto de extremidade https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados do cliente potencial. Em seguida, exporte os dados para o sistema CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicar no Partner Center.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos a você um líder de teste. Ao visualizar a oferta antes de ela ficar ativa, você também pode testar sua conexão de cliente potencial tentando implantar a oferta por conta própria no ambiente de visualização.
4. Certifique-se de que a conexão com o destino do cliente potencial permaneça atualizada para que você não perca nenhum cliente potencial.

Selecione **salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite que você defina as categorias e os setores usados para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="categories"></a>Categorias

Selecione no mínimo um e no máximo cinco categorias. Essas categorias são usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Na descrição da oferta, explique como sua oferta dá suporte a essas categorias. As ofertas de máquina virtual aparecem sob a categoria **computação** no Azure Marketplace.

### <a name="legal"></a>Legal

Você deve fornecer os termos e condições para a oferta. Você tem duas opções:

- Use seus próprios termos e condições
- Usar o contrato padrão para o Marketplace comercial da Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Use seus próprios termos e condições

Para fornecer seus próprios termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **termos e condições** . Se você precisar de uma descrição mais longa, insira um único endereço Web que aponte para onde os termos e condições podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes que possam experimentar sua oferta.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Usar o contrato padrão para o Marketplace comercial da Microsoft

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão para o mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez, e você não precisa criar termos e condições personalizados.

Use o contrato padrão marcando a caixa de seleção **usar o contrato padrão para o Marketplace comercial da Microsoft** e, em seguida, **aceite** na janela pop-up (talvez seja necessário rolar para cima para vê-la).

![Ilustra a página de visão geral no Partner Center com o botão nova oferta e a oferta de serviço de consultoria selecionada.](media/use-standard-contract.png)

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão para o Marketplace comercial, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o contrato Standard **ou** em seus próprios termos e condições.

Para saber mais sobre o contrato Standard, confira contrato Standard para o [Marketplace comercial](https://docs.microsoft.com/azure/marketplace/standard-contract)da Microsoft. Você pode baixar o [contrato padrão](https://go.microsoft.com/fwlink/?linkid=2041178) como um PDF (verifique se o bloqueador de pop-ups está desativado).

##### <a name="standard-contract-amendments"></a>Emendas de contrato padrão

As emendas de contrato padrão permitem que você selecione os termos de contrato padrão para simplificar e criar os termos para seu produto ou negócio. Os clientes precisam apenas examinar as emendas ao contrato se já tiverem revisado e aceito o contrato padrão da Microsoft. Há dois tipos de emendas disponíveis: universal e personalizada.

**Emendas universais** – elas são aplicadas universalmente ao contrato padrão para todos os clientes. Eles são mostrados para cada cliente da oferta no fluxo de compra. Os clientes devem aceitar os termos do contrato padrão e as emendas antes de poderem usar sua oferta. Você pode fornecer uma única emenda universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para clientes no AppSource, no Azure Marketplace e/ou portal do Azure durante a descoberta e o fluxo de compra.

**Emendas personalizadas** – são emendas especiais ao contrato padrão que são direcionados a clientes específicos por meio de IDs de locatário do Azure. Você pode escolher o locatário que deseja direcionar. Somente os clientes do locatário serão apresentados com os termos personalizados de emenda no fluxo de compra da oferta. Os clientes devem aceitar os termos do contrato padrão e as emendas antes de poderem usar sua oferta.

Comece selecionando **Adicionar termos de emenda personalizada (máx. 10)**. Você pode fornecer até dez termos personalizados de emenda por oferta.

- **Termos personalizados de emenda** – insira seus próprios termos de emenda na caixa termos personalizados de emenda. Você pode inserir um número ilimitado de caracteres. Somente os clientes das IDs de locatário que você especificar para esses termos personalizados poderão vê-los no fluxo de compra da oferta na portal do Azure.
- **IDs de locatário** (obrigatório) – cada alteração personalizada pode ser direcionada para até 20 IDs de locatário. Se você adicionar uma emenda personalizada, deverá fornecer pelo menos uma ID de locatário, que identifica seu cliente no Azure. o cliente pode encontrar para você no Azure em, em seguida, em Propriedades. O valor da ID de diretório é a ID do locatário (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode encontrar a ID de locatário da organização de seu cliente usando seu endereço Web de nome de domínio em [o que é minha Microsoft Azure e ID de locatário do Office 365?](https://www.whatismytenantid.com/).
- **Descrição** (opcional) – forneça uma descrição amigável para a ID do locatário que ajuda a identificar o cliente que você está direcionando para o aditamento.

> [!NOTE]
> Esses dois tipos de emendas se empilham um sobre o outro. Os clientes destinados a emendas personalizadas também terão a emenda universal ao contrato padrão durante a compra.

Selecione **salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite definir detalhes da oferta, como nome da oferta, descrição, links e contatos.

> [!NOTE]
> A oferta de conteúdo de listagem (como descrição, documentos, capturas de tela e termos de uso) não precisa estar em inglês, desde que a descrição da oferta comece com a frase ", este aplicativo está disponível somente em [idioma diferente do inglês]". Você também pode fornecer um _endereço da Web de link útil_ para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

### <a name="marketplace-details"></a>Detalhes do Marketplace

#### <a name="name"></a>Name

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto inserido na caixa alias de **oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir os símbolos de marca registrada e direitos autorais)
- Não pode ter mais de 50 caracteres de comprimento
- Não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Isso pode ter até 100 caracteres e é usado nos resultados da pesquisa do Marketplace.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta. Isso pode ter até 256 caracteres e é usado nos resultados da pesquisa do Marketplace.

#### <a name="description"></a>Descrição

Forneça uma descrição detalhada da sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem do Marketplace comercial.

Inclua um ou mais dos seguintes em sua descrição:

- O valor e os principais benefícios de sua oferta
- Associações de categoria ou da indústria, ou ambas
- Oportunidades de compra no aplicativo
- Todas as divulgações necessárias

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente a proposição de valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
  - Descrição da sua oferta.
  - O tipo de usuário que se beneficia da sua oferta.
  - As necessidades do cliente ou os problemas que a oferta atendem.
- Lembre-se de que as primeiras frases podem ser exibidas nos resultados do mecanismo de pesquisa.
- Não confie em recursos e funcionalidades para vender sua oferta. Em vez disso, concentre-se no valor que sua oferta oferece.
- Use palavras específicas do setor ou de benefício.

Para tornar a descrição da sua oferta mais atraente, use o editor de Rich Text para formatar sua descrição. O editor de Rich Text permite que você adicione números, marcadores, negrito, itálico e recuos para tornar sua descrição mais legível.

![Ilustra a página de visão geral no Partner Center com o botão nova oferta e a oferta de serviço de consultoria selecionada.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Link de política de privacidade

Insira o endereço da Web (URL) para a política de privacidade da sua organização. Garanta que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também deve postar uma política de privacidade válida em seu site.

### <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Para adicionar um link, selecione **+ Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Nome** – os clientes verão o nome na página de detalhes.
- **Link (URL)** – Insira um link para que os clientes exibam seu documento online.

### <a name="customer-support-links"></a>Links de atendimento ao cliente

Forneça o site de suporte onde os clientes podem acessar sua equipe de suporte.

- Site de suporte global do Azure
- Site de suporte do Azure governamental

### <a name="partner-support-contact"></a>Contato de suporte do parceiro

Forneça informações de contato para que os parceiros da Microsoft usem quando os clientes abrirem um tíquete de suporte. Isso não será listado no Marketplace.

- Name
- Email
- Telefone

### <a name="engineering-contact"></a>Contato de engenharia

Forneça informações de contato da Microsoft para usar quando houver problemas com sua oferta, incluindo problemas com a certificação. Isso não será listado no Marketplace.

- Name
- Email
- Telefone

### <a name="marketplace-media"></a>Mídia do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

>[!Note]
>Se você tiver um problema ao carregar arquivos, verifique se sua rede local não bloqueia o https://upload.xboxlive.com serviço usado pelo Partner Center.

#### <a name="marketplace-logos"></a>Logotipos do Marketplace

Forneça arquivos PNG do logotipo da sua oferta nos seguintes quatro tamanhos de pixel:

- **Pequeno** (48 x 48)
- **Médio** (90 x 90)
- **Grande** (216 x 216)
- **Largo** (255 x 115)

Todos os quatro logotipos são necessários e são usados em locais diferentes na listagem do Marketplace.

#### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela deve ter 1280 x 720 pixels de tamanho e no formato PNG. Você também deve adicionar uma legenda para descrever a captura de tela.

#### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstram sua oferta. Eles devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço Web e uma imagem PNG em miniatura do vídeo com 1280 x 720 pixels de tamanho.

Para obter recursos adicionais de listagem do Marketplace, consulte [práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Selecione **salvar rascunho** antes de continuar.

## <a name="preview"></a>Visualização

Na guia Visualização, escolha um público- **alvo** limitado para validar sua oferta antes de publicá-lo em tempo real para o público do Marketplace mais amplo.

> [!IMPORTANT]
> Depois de verificar sua oferta em versão prévia, selecione **entrar em tempo real** para publicar sua oferta no público público do Marketplace comercial.

Seu público-alvo de visualização é identificado por GUIDs de ID de assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. Você pode encontrar sua ID de assinatura do Azure na página **assinaturas** no portal do Azure.

Adicione pelo menos uma ID de assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você define quem pode visualizar sua oferta antes que ela seja publicada ao vivo. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público de visualização difere de um público privado. Um público de visualização pode acessar sua oferta _antes_ de ser publicado em tempo real nos Marketplaces. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público privado depois que sua oferta for totalmente publicada no Marketplace. Um público privado (definido na guia **preço e disponibilidade** do plano) tem acesso exclusivo a um plano específico.

Selecione **salvar rascunho** antes de prosseguir para a próxima seção, visão geral do plano.

## <a name="plan-overview"></a>Visão geral do plano

Você pode fornecer opções de plano diferentes na mesma oferta no Partner Center. Esses planos foram mencionados anteriormente como SKUs. Uma oferta requer pelo menos um plano, que pode diferir em termos de público monetização, regiões do Azure, recursos ou imagens de VM.

Depois de criar seus planos, a guia **visão geral do plano** mostra:

- Nomes de plano
- Modelos de licença
- Público-alvo (público ou privado)
- Status de publicação atual
- Ações disponíveis

As ações disponíveis na visão geral do plano variam de acordo com o status atual do seu plano. Elas incluem:

- **Excluir rascunho** – se o status do plano for um rascunho
- **Parar vender plano** ou **sincronizar público privado** – se o status do plano for publicado ao vivo

### <a name="create-new-plan"></a>Criar novo plano

Selecione **+ criar novo plano** na parte superior. A caixa de diálogo **novo plano** é exibida.

Na caixa **ID do plano** , crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes no endereço da Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não pode ser alterada depois que você seleciona **criar**.

Na caixa **nome do plano** , insira um nome para este plano. Os clientes veem esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome exclusivo que indica claramente as diferenças de cada plano. Por exemplo, você pode usar **o Windows Server** com **planos pagos conforme o**uso, **BYOL**, **avançado**e **corporativo**.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Defina a configuração de alto nível para o tipo de plano, se ele reutiliza a configuração técnica de outro plano e em quais regiões do Azure o plano deve estar disponível. Suas seleções aqui determinam quais campos são exibidos em outras guias para o mesmo plano.

#### <a name="reuse-technical-configuration"></a>Reutilizar configuração técnica

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **este plano reutilizando a configuração técnica de outro plano**. Essa opção permite selecionar um dos outros planos do mesmo tipo para essa oferta e reutilizar sua configuração técnica.

> [!NOTE]
> Quando você reutiliza a configuração técnica de outro plano, a guia de **configuração técnica** inteira desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações feitas no futuro, também serão usados para esse plano. Essa configuração não pode ser alterada depois que este plano é publicado.

#### <a name="azure-regions"></a>Regiões do Azure

Seu plano deve ser disponibilizado em pelo menos uma região do Azure.

Selecione a opção **global do Azure** para disponibilizar seu plano para clientes em todas as regiões públicas do Azure que têm integração com o Marketplace comercial. Para obter detalhes, confira [disponibilidade geográfica e suporte a moeda](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecione a opção **Azure governamental** para disponibilizar seu plano na região do [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribal dos EUA, bem como parceiros qualificados para atendê-los. Você, como Publicador, é responsável por qualquer controle de conformidade, medidas de segurança e práticas recomendadas. O Azure governamental usa data centers isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar no [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), teste e valide seu plano no ambiente, pois determinados pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação de [Microsoft Azure governamental avaliação](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e disponível em uma região específica do Azure, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure governamental

Essa opção só estará visível se você selecionar **Azure governamental** em **regiões do Azure**.

Os serviços do Azure governamental lidam com os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para dar reconhecimento às suas certificações para esses programas, você pode fornecer até 100 links que os descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis somente para clientes do Azure governamental.

Selecione **salvar rascunho** antes de continuar.

### <a name="plan-listing"></a>Lista de planos

É aqui que você configura os detalhes da listagem do plano. Essa guia exibe informações específicas que podem diferir entre os planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isso é preenchido previamente com o nome que você atribuiu ao seu plano quando o criou. Esse nome aparece no Marketplace como o título deste plano e é limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo é limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo, bem como as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **salvar rascunho** antes de continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Nessa guia, você configurará o seguinte:

- Mercados este plano estará disponível em
- O preço por hora
- Se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público privado)

#### <a name="markets"></a>Mercados

Cada plano deve estar disponível em pelo menos um mercado. Marque a caixa de seleção para cada local do mercado em que esse plano deve estar disponível para compra (os usuários nesses mercados ainda podem implantar a oferta em todas as regiões do Azure selecionadas na **[configuração do plano](#plan-setup)**). O botão **remetido de imposto** mostra países nos quais a Microsoft remete vendas e impostos sobre o uso em seu nome. A publicação na China é limitada a planos que são **gratuitos** ou BYOL ( **traga sua própria licença** ).

Se você já tiver definido preços para seu plano em dólares de Estados Unidos (USD) e adicionar outro local de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Examine os preços usando o link de **preços de exportação (xlsx)** depois de salvar as alterações.

Quando você remove um mercado, os clientes desse mercado que usam implantações ativas não poderão criar novas implantações ou escalar verticalmente suas implantações existentes. As implantações existentes não serão afetadas.

#### <a name="pricing"></a>Preços

**Modelo de licença** – selecione **plano cobrado mensalmente baseado em uso** para configurar os preços para este plano ou **traga sua própria licença** para permitir que os clientes usem esse plano com a licença existente.

Para um plano cobrado mensalmente baseado em uso, use uma das três opções de entrada de preços a seguir:

- **Por núcleo** – forneça o preço por núcleo em dólares de Estados Unidos (USD). Calcularemos o preço por tamanho de núcleo e converteremos em moedas locais usando a taxa de câmbio atual.
- **Por tamanho de núcleo** – forneça preços por tamanho de núcleo em USD. Converteremos os preços em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho de núcleo** – forneça preços para cada tamanho de núcleo para todos os mercados. Você pode importar preços de uma planilha.

> [!NOTE]
> Salve as alterações de preços para habilitar a exportação de dados de preços. Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente. Verifique se esses preços estão corretos antes da publicação exportando a planilha de preços e revisando o preço em cada mercado.

#### <a name="free-trial"></a>Avaliação gratuita

Você pode oferecer uma avaliação gratuita de um mês ou três meses para seus clientes.

#### <a name="visibility"></a>Visibilidade

Você pode criar cada plano para ser visível para todos ou apenas para um público selecionado. Atribua associações neste público restrito usando as IDs de assinatura do Azure.

**Público** – seu plano pode ser visto por todos.

**Público privado** – torne seu plano visível apenas para um público selecionado. Depois de publicado como um plano privado, você pode atualizar o público ou alterá-lo para público. Depois de tornar um plano público, ele deve permanecer público; Você não pode alterá-lo de volta para privado.

**Público-alvo restrito (IDs de assinatura do Azure)** – atribua o público que terá acesso a esse plano privado usando as IDs de assinatura do Azure. Opcionalmente, inclua uma descrição de cada ID de assinatura do Azure que você atribuiu. Adicione até 10 IDs de assinatura manualmente ou 20.000 se estiver importando uma planilha CSV. As IDs de assinatura do Azure são representadas como GUIDs e as letras devem estar em minúsculas.

> [!NOTE]
> Um público privado ou restrito é diferente do público de visualização que você definiu na guia **Visualização** . Um público de visualização pode acessar sua oferta _antes_ de sua publicação em tempo real no Marketplace. Embora a escolha de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Ocultar plano

Se sua máquina virtual for destinada a ser usada apenas indiretamente quando for referenciada por outro modelo de solução ou aplicativo gerenciado, selecione esta caixa para publicar sua máquina virtual, mas ocultá-la de clientes pesquisando e navegando diretamente.

> [!NOTE]
> Os planos ocultos não dão suporte a links de visualização.

Selecione **salvar rascunho** antes de continuar.

### <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas associadas a este plano. Para obter detalhes, consulte [criar um ativo técnico de VM do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Essa guia não será mostrada se você tiver configurado esse plano para reutilizar pacotes de outro plano na guia **configuração do plano** .

#### <a name="operating-system"></a>Sistema operacional

- **Família do sistema operacional** – selecione do sistema operacional **Windows** ou **Linux**
- **Lançamento** ou **fornecedor** – escolha a versão do Windows ou o fornecedor do Linux
- **Nome amigável do so** – escolha um nome de sistema operacional amigável. Esse nome é visível para os clientes

#### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos recomendados de máquina virtual para exibir no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas em uma máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

**Opção de implantação de disco** – selecione o tipo de implantação de disco que os usuários podem usar ao usar a máquina virtual. A Microsoft recomenda limitar a implantação somente à implantação de disco gerenciado.

#### <a name="properties"></a>Propriedades

**Suporte à rede acelerada** – selecione se sua VM der suporte [à rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens da VM

Forneça uma versão de disco e o URI de SAS para as imagens de máquina virtual. Adicione até 16 discos de dados para cada imagem de VM. Forneça apenas uma nova versão de imagem por plano em um determinado envio. Depois que uma imagem for publicada, você não poderá editá-la, mas poderá excluí-la. A exclusão de uma versão impedirá que os usuários novos e existentes implantem uma nova instância da versão excluída.

- A **versão do disco** é a versão da imagem que você está fornecendo.
- O **URI de SAS** é o local no armazenamento do Azure no qual você armazenou o VHD do sistema operacional.
- As imagens de disco de dados também são URIs SAS de VHD armazenadas no armazenamento do Azure.
- Adicione apenas uma imagem por envio em um plano.

Independentemente do sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

Selecione **salvar rascunho** antes de continuar e voltar para a **visão geral do plano**.

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa CSP ( [provedores de soluções de nuvem](https://azure.microsoft.com/offers/ms-azr-0145p/) ). Todos os planos BYOL (traga sua própria licença) são automaticamente aceitos; Você pode optar por aceitar seus planos não BYOL.

Selecione **salvar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configure uma demonstração (test drive) que permite aos clientes experimentar sua oferta antes de adquiri-la. Para criar um ambiente de demonstração que permita aos clientes experimentar sua oferta por um período de tempo fixo, consulte [testar sua oferta no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para habilitar uma test drive, marque a caixa de seleção Habilitar um test drive na guia [configuração da oferta](#test-drive) . Para remover test drive de sua oferta, desmarque essa caixa de seleção.

Recursos de test drive adicionais:

- Práticas recomendadas técnicas
- Práticas recomendadas de marketing
- Visão geral das unidades de teste (PDF; Verifique se o bloqueador de pop-ups está desativado).

Selecione **salvar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-las para revisar e publicar.

No canto superior direito do portal, selecione **revisar e publicar**.

Nesta página, você pode:

- Consulte o status de conclusão de cada seção da oferta.
  - **Não iniciado** – a seção não foi iniciada e precisa ser concluída.
  - **Incompleto** – a seção tem erros que devem ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores neste documento para obter orientação sobre como atualizar esta seção.
  - **Concluída** – a seção está concluída e não há erros. Todas as seções da oferta devem ser concluídas para que você possa enviar a oferta.
- **Notas de certificação** – forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente. Forneça notas suplementares úteis para entender seu aplicativo.

Para enviar a oferta para publicação, selecione **revisar e publicar**.

Enviaremos um email para que você saiba quando uma versão de visualização da oferta está disponível para revisão e aprovação. Para publicar sua oferta no público (ou se uma oferta privada, para um público privado), vá para o Partner Center, localize a página de **visão geral** da sua oferta e selecione **Go-Live**. O status da sua oferta será exibido na parte superior da página quando a publicação estiver em andamento.

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa de **validação manual** no processo de publicação representa uma análise extensiva de sua oferta e seus ativos técnicos associados. Se esse processo descobrir erros com sua oferta, você receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e republicar sua oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da oferta** mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída e em andamento) e quanto tempo cada etapa deve levar para ser concluída.

Esta página inclui links para executar operações nessa oferta com base na seleção feita por você. Por exemplo:

- Se a oferta for uma oferta de [rascunho de exclusão](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver em tempo real, [pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em visualização- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não concluiu a desconexão do Publicador, [cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exemplos do Marketplace

Esses exemplos mostram como a oferta aparece no Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Detalhes da oferta do Azure Marketplace

![Exemplo de tela detalhes da oferta do Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Resultados da pesquisa do Azure Marketplace

![Exemplo de tela detalhes da pesquisa do Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Detalhes do plano do Azure Marketplace

![Exemplo de tela detalhes do plano do Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Detalhes da oferta de portal do Azure

![Exemplo de tela detalhes da oferta de portal do Azure](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>portal do Azure resultados da pesquisa

![Exemplo de tela de resultados da pesquisa portal do Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Detalhes do plano de portal do Azure

![Exemplo de tela detalhes do plano de portal do Azure](media/avm-create6.png)

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
