---
title: Crie uma oferta de máquina virtual Do Azure - Azure Marketplace
description: Aprenda a criar uma oferta de máquina virtual no mercado comercial.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d5626f00e9627338349d9b579bcf26256148b551
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731258"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Crie uma oferta de máquina virtual do Azure

> [!IMPORTANT]
> Estamos mudando o gerenciamento de suas ofertas de VM do Portal de Parceiros na Nuvem para o Partner Center. Até que suas ofertas sejam migradas, continue a seguir as instruções em [Criar a oferta da Máquina Virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) no Portal de Parceiros em Nuvem para gerenciar suas ofertas.

Este artigo descreve como criar e publicar uma oferta de máquina virtual do [Azure ao Azure Marketplace](https://azuremarketplace.microsoft.com/). Ele aborda máquinas virtuais baseadas em Windows e Linux que contêm um sistema operacional, disco rígido virtual (VHD) e até 16 discos de dados.

## <a name="introduction"></a>Introdução

### <a name="publishing-benefits"></a>Benefícios da publicação

A publicação no Azure Marketplace tem os seguintes benefícios:

- Promova sua empresa usando a marca Microsoft
- Alcance mais de 100 milhões de usuários do Office 365 e Dynamics 365 e mais de 200.000 organizações através do Azure Marketplace
- Obtenha leads de alta qualidade desses marketplaces
- Obtenha seus serviços promovidos pelas equipes de campo e televendas da Microsoft

### <a name="before-you-begin"></a>Antes de começar

Se você ainda não fez isso, revise o [guia de publicação da máquina virtual](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e este material de máquina virtual do Azure:

- Guias quickstart
  - [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelos de Início Rápido do Azure GitHub](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [VMs Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Exemplos
  - [Azure CLI Samples para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI Amostras para VMs windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell para VMs windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

Projetar, construir e testar esses ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure quanto das tecnologias usadas para construir a oferta.

Sua equipe de engenharia deve entender as seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho de [máquinas virtuais azure,](https://azure.microsoft.com/services/virtual-machines/) [armazenamento azure](https://azure.microsoft.com/services/?filter=storage#storage)e [rede Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Crie uma oferta de máquina virtual do Azure

Antes de criar uma oferta de máquina virtual do Azure, você deve ter uma conta de mercado comercial no Partner Center. Se você ainda não criou um, consulte [Criar uma conta de mercado comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Faça login na [Central de Parceiros](https://partner.microsoft.com/dashboard/home)e, em seguida, no menu superior, selecione Painel de **instrumentos**.
2. Na barra de navegação esquerda, selecione **Comercial Marketplace,** em seguida, **Visão geral**.
3. Na página **Visão geral,** selecione **+ Nova oferta**, em **seguida, Azure Virtual Machine**. A caixa de diálogo **Nova oferta** é exibida.

    ![Ilustra a página Visão Geral no Partner Center com o botão De oferta Nova e a oferta do Azure Virtual Machine selecionada.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Oferecer ID e alias

Digite um **ID de Oferta**. Este é um identificador único para cada oferta em sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de marketplace e no Azure PowerShell e Azure CLI, se aplicável.
- Use apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e é limitado a 50 caracteres. Por exemplo, se você inserir **test-offer-1** aqui, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .
- O ID de oferta não pode ser alterado depois que você selecionar **Criar**.

Digite um **alias De Oferta**. Este é o nome usado para a oferta no Partner Center. Esse nome não é usado no mercado e é diferente do nome da oferta e de outros valores mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

### <a name="test-drive"></a>Test drive

Configure uma demonstração (test drive) que permite que os clientes experimentem sua oferta antes de comprá-la. Para criar um ambiente de demonstração que permite que os clientes experimentem sua oferta por um período fixo de tempo, consulte [Test Drive sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para habilitar um test drive, selecione **a caixa Desativação de uma** caixa de seleção de test drive. Para remover o test drive de sua oferta, limpe esta caixa de seleção.

Recursos adicionais de test drive:

- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do Test Drives](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (certifique-se de que seu bloqueador pop-up está desligado).

### <a name="lead-management"></a>Gerenciamento de clientes potenciais

Ao publicar sua oferta no mercado comercial com o Partner Center, conecte-a ao sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém manifestar interesse ou usar seu produto. A conexão a um CRM é necessária se você ativar **o Test Drive** (ver seção anterior), caso contrário, é opcional.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center suporta os seguintes sistemas de CRM:
    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para engajamento do cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o sistema de CRM não estiver listado acima, use [a Tabela Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Ponto Final https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar dados de lead do cliente. Em seguida, exporte os dados para o seu sistema de CRM.

2. Conecte sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a conexão com o destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a conexão e enviaremos um lead de teste. Enquanto você visualiza a oferta antes de entrar em operação, você também pode testar sua conexão de chumbo tentando implantar a oferta você mesmo no ambiente de visualização.
4. Certifique-se de que a conexão com o destino principal permaneça atualizada para que você não perca nenhuma pista.

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar sua oferta no mercado, sua versão de aplicativo e os contratos legais que suportam sua oferta.

### <a name="categories"></a>Categorias

Selecione um mínimo de uma e no máximo cinco categorias. Essas categorias são usadas para colocar sua oferta nas áreas de pesquisa de mercado apropriadas. Na descrição da oferta, explique como sua oferta suporta essas categorias. As ofertas de máquinas virtuais aparecem na categoria **Computação** no Azure Marketplace.

### <a name="legal"></a>Legal

Você deve fornecer termos e condições para a oferta. Você tem duas opções:

- Use seus próprios termos e condições
- Use o Contrato Padrão para o mercado comercial da Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Use seus próprios termos e condições

Para fornecer seus próprios termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **Termos e condições.** Se você precisar de uma descrição mais longa, digite um único endereço web que aponte para onde seus termos e condições podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes de tentar sua oferta.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Use o Contrato Padrão para o mercado comercial da Microsoft

Para simplificar o processo de aquisição para os clientes e reduzir a complexidade jurídica para os fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial. Quando você oferece seu software sob o Contrato Padrão, os clientes só precisam lê-lo e aceitá-lo uma vez, e você não precisa criar termos e condições personalizados.

Use o Contrato Padrão selecionando o **Use the Standard Contract para a** caixa de seleção de mercado comercial da Microsoft e, em seguida, **aceite** na janela pop-up (você pode ter que rolar para cima para vê-lo).

![Ilustra a página Visão Geral no Partner Center com o botão De oferta Nova e a oferta de serviço de consultoria selecionada.](media/use-standard-contract.png)

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Padrão para mercado comercial, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o Contrato Padrão **ou** sob seus próprios termos e condições.

Para saber mais sobre o Contrato Padrão, consulte Contrato Padrão para o [mercado comercial](https://docs.microsoft.com/azure/marketplace/standard-contract)da Microsoft . Você pode baixar o [Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178) como PDF (certifique-se de que seu bloqueador pop-up está desligado).

##### <a name="standard-contract-amendments"></a>Alterações de contrato padrão

As Alterações padrão do contrato permitem que você selecione os termos do Contrato Padrão para simplificar e criar os termos para o seu produto ou negócio. Os clientes só precisam revisar as alterações do contrato se já tiverem revisto e aceitado o Contrato Padrão da Microsoft. Existem dois tipos de alterações disponíveis: universal e aduaneira.

**Emendas Universais** – Estas são aplicadas universalmente ao Contrato Padrão para todos os clientes. Eles são mostrados a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem usar sua oferta. Você pode fornecer uma única emenda universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos aos clientes no Portal AppSource, Azure Marketplace e/ou Azure durante o fluxo de descoberta e compra.

**Alterações Personalizadas** – São alterações especiais no Contrato Padrão que são direcionadas a clientes específicos através de IDs de inquilinos do Azure. Você pode escolher o inquilino que deseja atingir. Somente os clientes do inquilino serão apresentados com os termos de alteração aduaneira no fluxo de compra da oferta. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem usar sua oferta.

Comece selecionando **Adicionar termos de alteração personalizados (Max 10)**. Você pode fornecer até dez termos de alteração personalizados por oferta.

- **Termos de alteração aduaneira** - Insira seus próprios termos de alteração na caixa de termos de alteração personalizada. Você pode inserir um número ilimitado de caracteres. Somente os clientes das IDs de inquilino especificadas para estes termos personalizados os verão no fluxo de compra da oferta no portal Azure.
- **IDs de inquilino (obrigatório)** – Cada alteração personalizada pode ser direcionada para até 20 IDs de inquilinos. Se você adicionar uma alteração personalizada, você deve fornecer pelo menos um ID de inquilino, que identifica seu cliente no Azure. seu cliente pode encontrá-lo no Azure em, em seguida, Propriedades. O valor do ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode encontrar o ID de inquilino da organização do seu cliente usando seu endereço web de nome de domínio no [Qual é o meu ID de inquilino microsoft Azure e Office 365?](https://www.whatismytenantid.com/)
- **Descrição** (opcional) – Forneça uma descrição amigável para o ID do inquilino que o ajuda a identificar o cliente que você está mirando com a alteração.

> [!NOTE]
> Esses dois tipos de alterações se acumulam em cima um do outro. Os clientes alvo de alterações personalizadas também receberão a alteração universal do Contrato Padrão durante a compra.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite definir detalhes de oferta, como nome, descrição, links e contatos.

> [!NOTE]
> O conteúdo de listagem de ofertas (como a descrição, documentos, capturas de tela e termos de uso) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Este aplicativo está disponível apenas em [língua não inglesa]" Você também pode fornecer um _endereço web do Link Útil_ para oferecer conteúdo em um idioma diferente do usado no conteúdo de listagem de ofertas.

### <a name="marketplace-details"></a>Detalhes do marketplace

#### <a name="name"></a>Nome

O nome que você digita aqui é mostrado aos clientes como o título da sua lista de ofertas. Este campo está pré-preenchido com o texto que você inseriu na caixa **de alias Oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser registrada (e você pode incluir símbolos de marca e direitos autorais)
- Não pode ter mais de 50 caracteres
- Não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição de sua oferta. Isso pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa de sua oferta. Isso pode ter até 256 caracteres e é usado em resultados de pesquisa de mercado.

#### <a name="description"></a>Descrição

Forneça uma descrição detalhada de sua oferta, até 3.000 caracteres. Isso é exibido aos clientes na visão geral da listagem do mercado comercial.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os principais benefícios de sua oferta
- Categorias ou associações industriais, ou ambos
- Oportunidades de compra no aplicativo
- Quaisquer divulgações necessárias

Aqui estão algumas dicas para escrever sua descrição:

- Descreva claramente a proposição de valor de sua oferta nas primeiras frases de sua descrição. Inclua os seguintes itens:
  - Descrição de sua oferta.
  - O tipo de usuário que se beneficia da sua oferta.
  - Necessidades do cliente ou problemas que a oferta aborda.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados do mecanismo de busca.
- Não confie em recursos e funcionalidades para vender sua oferta. Em vez disso, concentre-se no valor que sua oferta fornece.
- Use palavras específicas do setor ou baseadas em benefícios.

Para tornar sua descrição de oferta mais atraente, use o rico editor de texto para formatar sua descrição. O rico editor de texto permite adicionar números, balas, negrito, itálico e recuos para tornar sua descrição mais legível.

![Ilustra a página Visão Geral no Partner Center com o botão De oferta Nova e a oferta de serviço de consultoria selecionada.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Digite o endereço web (URL) na política de privacidade da sua organização. Certifique-se de que sua oferta está em conformidade com as leis e regulamentos de privacidade. Você também deve postar uma política de privacidade válida em seu site.

### <a name="useful-links"></a>Links úteis

Forneça documentos on-line complementares sobre sua oferta. Para adicionar um link, selecione **+ Adicione um link** e, em seguida, complete os seguintes campos:

- **Nome** – Os clientes verão o nome na página de detalhes.
- **Link (URL)** – Insira um link para que os clientes visualizem seu documento online.

### <a name="customer-support-links"></a>Links de suporte ao cliente

Forneça o site de suporte onde os clientes podem entrar em contato com sua equipe de suporte.

- Site de suporte do Azure Global
- Site de suporte do governo azure

### <a name="partner-support-contact"></a>Contato de suporte ao parceiro

Forneça informações de contato para os parceiros da Microsoft usarem quando seus clientes abrirem um ticket de suporte. Isso não será listado no mercado.

- Nome
- Email
- Telefone

### <a name="engineering-contact"></a>Contato de engenharia

Forneça informações de contato para a Microsoft usar quando houver problemas com sua oferta, incluindo problemas com certificação. Isso não será listado no mercado.

- Nome
- Email
- Telefone

### <a name="marketplace-media"></a>Mídia de mercado

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens devem estar em formato PNG. Imagens embaçadas farão com que sua submissão seja rejeitada.

#### <a name="marketplace-logos"></a>Logotipos de marketplace

Forneça arquivos PNG do logotipo da sua oferta nos seguintes tamanhos de quatro pixels:

- **Pequeno** (48 x 48)
- **Médio** (90 x 90)
- **Grande** (216 x 216)
- **Largo** (255 x 115)

Todos os quatro logotipos são necessários e são usados em diferentes lugares na listagem de marketplace.

#### <a name="screenshots"></a>Capturas de tela

Adicione cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela deve ter 1280 x 720 pixels de tamanho e em formato PNG. Você também deve adicionar uma legenda para descrever sua captura de tela.

#### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstram sua oferta. Estes devem ser hospedados em um serviço de vídeo externo. Digite o nome de cada vídeo, endereço da Web e uma imagem PNG em miniatura do vídeo com 1280 x 720 pixels de tamanho.

Para obter recursos adicionais de listagem de marketplace, consulte [As melhores práticas para listagens de ofertas de marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Selecione **Salvar rascunho** antes de continuar.

## <a name="preview"></a>Visualização

Na guia Pré-visualização, escolha uma audiência de **visualização** limitada para validar sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar sua oferta no Preview, selecione **Ir ao vivo** para publicar sua oferta para o público do mercado comercial.

Seu público de visualização é identificado por GUIDs de assinatura do Azure, juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. Você pode encontrar seu ID de assinatura do Azure na página **assinaturas** no portal Azure.

Adicione pelo menos um ID de assinatura do Azure, individualmente (até 10) ou carregando um arquivo CSV (até 100). Adicionando esses IDs de assinatura, você define quem pode visualizar sua oferta antes de ser publicada ao vivo. Se sua oferta já estiver ao vivo, você ainda pode definir uma audiência de pré-visualização para testar alterações ou atualizações de ofertas.

> [!NOTE]
> Uma audiência de pré-visualização difere de uma audiência privada. Um público de pré-visualização pode acessar sua oferta _antes_ de ser publicada ao vivo nos marketplaces. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado depois que sua oferta for totalmente publicada no mercado. Um público privado (definido na guia **Preço e Disponibilidade** do plano) tem acesso exclusivo a um plano específico.

Selecione **Salvar rascunho** antes de prosseguir para a próxima seção, visão geral do Plano.

## <a name="plan-overview"></a>Visão geral do plano

Você pode fornecer diferentes opções de plano dentro da mesma oferta no Partner Center. Esses planos foram previamente referidos como SKUs. Uma oferta requer pelo menos um plano, que pode diferir em termos de audiência de monetização, regiões do Azure, recursos ou imagens de VM.

Depois de criar seus planos, a guia **'Visão geral do Plano'** mostra:

- Nomes do plano
- Modelos de licença
- Audiência pública ou privada)
- Status de publicação atual
- Ações disponíveis

As ações disponíveis na visão geral do Plano variam dependendo do status atual do seu plano. Elas incluem:

- **Exclusão de rascunho** – Se o status do plano for um rascunho
- **Parar de vender plano** ou **sincronizar audiência privada** – Se o status do plano for publicado ao vivo

### <a name="create-new-plan"></a>Criar um novo plano

Selecione **+ Crie um novo plano** no topo. A caixa de diálogo **Novo plano** é exibida.

Na caixa **Plan ID,** crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Use apenas letras e números minúsculos, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois que você selecionar **Criar**.

Na caixa **nome do Plano,** digite um nome para este plano. Os clientes veem esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome único que aponte claramente as diferenças de cada plano. Por exemplo, você pode usar **o Windows Server** com planos **Pay-as-you-go,** **BYOL,** **Advanced**e **Enterprise**.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Defina a configuração de alto nível para o tipo de plano, se ele reutiliza a configuração técnica de outro plano e em que regiões do Azure o plano deve estar disponível. Suas seleções aqui determinam quais campos são exibidos em outras guias para o mesmo plano.

#### <a name="reuse-technical-configuration"></a>Reutilizar configuração técnica

Se você tiver mais de um plano do mesmo tipo, e os pacotes forem idênticos entre eles, você pode selecionar **este plano reutiliza a configuração técnica de outro plano**. Esta opção permite que você selecione um dos outros planos do mesmo tipo para esta oferta e reutilize sua configuração técnica.

> [!NOTE]
> Quando você reutiliza a configuração técnica de outro plano, toda a guia **de configuração técnica** desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que você fizer no futuro, também serão usados para este plano. Esta configuração não pode ser alterada após a publicação deste plano.

#### <a name="azure-regions"></a>Regiões do Azure

Seu plano deve ser disponibilizado em pelo menos uma região Azure.

Selecione a opção **Azure Global** para disponibilizar seu plano aos clientes em todas as regiões públicas do Azure que tenham integração de mercado comercial. Para obter detalhes, consulte [disponibilidade geográfica e suporte à moeda](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecione a opção **Governo Azure** para disponibilizar seu plano na região [do Governo Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Esta região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como parceiros elegíveis para atendê-los. Você, como editor, é responsável por quaisquer controles de conformidade, medidas de segurança e práticas recomendadas. O governo azure usa data centers e redes fisicamente isolados (localizados apenas nos EUA).

Antes de publicar para [o Governo Do Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)teste e valide seu plano no ambiente, pois certos pontos finais podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de teste do teste do [Governo do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que seu plano for publicado e disponível em uma região específica do Azure, você não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo do Azure

Esta opção só será visível se você selecionar **o Governo Azure** em **regiões Azure**.

Os serviços do Governo Azure lidam com dados que estão sujeitos a certas regulamentações e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para trazer consciência às suas certificações para esses programas, você pode fornecer até 100 links que os descrevem. Estes podem ser links para sua listagem no programa diretamente ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis apenas para clientes do Governo Azure.

Selecione **Salvar rascunho** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

É aqui que você configura detalhes de listagem do plano. Esta guia exibe informações específicas que podem diferir entre planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isso é pré-preenchido com o nome que você deu ao seu plano quando o criou. Este nome aparece no mercado como o título deste plano e é limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo é limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro de sua oferta. Não descreva a oferta, só o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **Salvar rascunho** antes de continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Nesta guia, você configurará o seguinte:

- Mercados este plano estará disponível em
- O preço por hora
- Seja para tornar o plano visível para todos ou apenas para clientes específicos (um público privado)

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de seleção para cada local de mercado onde este plano deve estar disponível para compra (os usuários nesses mercados ainda podem implantar a oferta em todas as regiões do Azure selecionadas na **[configuração do Plano](#plan-setup)**). O botão **Tax Remitted** mostra países em que a Microsoft reemite vendas e usa impostos em seu nome. A publicação para a China é limitada a planos que são **gratuitos** ou **trazem sua própria licença** (BYOL).

Se você já definiu os preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço para o novo mercado será calculado de acordo com as taxas de câmbio atuais. Sempre revise o preço de cada mercado antes de publicar. Revise os preços usando o link **Preços de Exportação (xlsx)** depois de salvar suas alterações.

Quando você remove um mercado, os clientes desse mercado usando implantações ativas não serão capazes de criar novas implantações ou aumentar suas implantações existentes. As implantações existentes não serão afetadas.

#### <a name="pricing"></a>Preços

**Modelo de licença** – **Selecione o plano mensal de cobrança baseado em uso** para configurar os preços para este plano ou traga sua própria **licença** para permitir que os clientes usem esse plano com sua licença existente.

Para um plano de faturamento mensal baseado em uso, use uma das três opções de entrada de preços a seguir:

- **Por núcleo** – Fornecer o preço por núcleo em Dólares dos Estados Unidos (USD). Calcularemos os preços por tamanho de núcleo e converteremos em moedas locais usando a taxa de câmbio atual.
- **Por tamanho de núcleo** – Forneça preços por tamanho de núcleo em USD. Converteremos os preços em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho central** – Forneça preços para cada tamanho de núcleo para todos os mercados. Você pode importar preços de uma planilha.

> [!NOTE]
> Salve as alterações de preços para permitir a exportação de dados de preços. Depois que um preço para um mercado em seu plano é publicado, ele não pode ser alterado mais tarde. Certifique-se de que esses preços estão certos antes de publicar exportando a planilha de preços e revisando o preço em cada mercado.

#### <a name="free-trial"></a>Avaliação gratuita

Você pode oferecer um teste gratuito de um mês ou três meses para seus clientes.

#### <a name="visibility"></a>Visibilidade

Você pode projetar cada plano para ser visível para todos ou para apenas um público pré-selecionado. Atribua adesões a esse público restrito usando IDs de assinatura do Azure.

**Público** – Seu plano pode ser visto por todos.

**Público privado** – Torne seu plano visível apenas para um público pré-selecionado. Depois de publicado como um plano privado, você pode atualizar o público ou alterá-lo para público. Depois de tornar público um plano, ele deve permanecer público; você não pode mudá-lo de volta para privado.

**Audiência Restrita (IDs de assinatura do Azure)** – Atribua ao público que terá acesso a este plano privado usando IDs de assinatura do Azure. Opcionalmente, inclua uma descrição de cada ID de assinatura do Azure que você atribuiu. Adicione até 10 IDs de assinatura manualmente ou 20.000 se importar uma planilha CSV. Os IDs de assinatura do Azure são representados como GUIDs e as letras devem ser minúsculas.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que você definiu na guia **Visualização.** Um público de pré-visualização pode acessar sua oferta _antes_ de sua publicação ao vivo no mercado. Embora a escolha do público privado só se aplique a um plano específico, o público de pré-visualização pode visualizar todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Plano de ocultação

Se sua máquina virtual for destinada a ser usada apenas indiretamente quando referenciada através de outro modelo de solução ou aplicativo gerenciado, selecione esta caixa para publicar sua máquina virtual, mas esconda-a dos clientes que a procuram e navegam diretamente por ela.

> [!NOTE]
> Planos ocultos não suportam links de visualização.

Selecione **Salvar rascunho** antes de continuar.

### <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas associadas a este plano. Para obter detalhes, consulte [Criar um ativo técnico do Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Esta guia não é mostrada se você configurou esse plano para reutilizar pacotes de outro plano na guia **De configuração Do Plano.**

#### <a name="operating-system"></a>Sistema operacional

- **Família do sistema operacional** – Selecione a partir do sistema operacional **Windows** ou **Linux**
- **Lançamento** ou **Fornecedor** – Escolha a versão do Windows ou o fornecedor Linux
- **Nome amigável do SISTEMA OPERACIONAL** – Escolha um nome de sistema operacional amigável. Este nome é visível para os clientes

#### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos de máquinas virtuais recomendados para exibir no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas em uma máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

**Opção de implantação de** disco – Selecione que tipo de implantação de disco seus usuários podem usar ao usar a máquina virtual. A Microsoft recomenda limitar a implantação apenas à implantação de disco gerenciado.

#### <a name="properties"></a>Propriedades

**Suporte a rede acelerada** – Selecione se a VM suportar [rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens da VM

Forneça uma versão em disco e o Uri SAS para as imagens da máquina virtual. Adicione até 16 discos de dados para cada imagem VM. Forneça apenas uma nova versão de imagem por plano em uma determinada submissão. Depois que uma imagem é publicada, você não pode editá-la, mas você pode excluí-la. A exclusão de uma versão impedirá que usuários novos e existentes implantem uma nova instância da versão excluída.

- **Versão do disco** é a versão da imagem que você está fornecendo.
- **SAS URI** é o local no Azure Storage onde você armazenou o sistema operacional VHD.
- As imagens de disco de dados também são URIs SAS VHD armazenadas em seu armazenamento Azure.
- Adicione apenas uma imagem por submissão em um plano.

Independentemente do sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas eles sempre podem adicionar discos durante ou após a implantação.

Selecione **Salvar rascunho** antes de continuar e retornar à **visão geral do Plano**.

## <a name="resell-through-csps"></a>Revender através de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Todos os planos Bring Your Own License (BYOL) são automaticamente optados; você pode optar por optar por seus planos não-BYOL.

Selecione **Salvar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configure uma demonstração (test drive) que permite que os clientes experimentem sua oferta antes de comprá-la. Para criar um ambiente de demonstração que permite que os clientes experimentem sua oferta por um período fixo de tempo, consulte [Test Drive sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para habilitar um test drive, selecione ativar uma caixa de seleção de test drive na guia [De configuração Oferta.](#test-drive) Para remover o test drive de sua oferta, limpe esta caixa de seleção.

Recursos adicionais de test drive:

- Melhores práticas técnicas
- Práticas recomendadas de marketing
- Visão geral do Test Drives (PDF; certifique-se de que o bloqueador pop-up está desligado).

Selecione **Salvar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode submetê-la para revisar e publicar.

No canto superior direito do portal, selecione **Revisar e publicar**.

Nesta página você pode:

- Consulte o status de conclusão de cada seção da oferta.
  - **Não iniciado** – O trecho ainda não foi iniciado e precisa ser concluído.
  - **Incompleto** – A seção tem erros que devem ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores neste documento para obter orientações sobre como atualizar esta seção.
  - **Completo** – A seção está completa e não há erros. Todas as seções da oferta devem estar completas antes de você enviar a oferta.
- **Notas para certificação** – Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente. Forneça quaisquer notas suplementares úteis para entender seu aplicativo.

Para enviar a oferta para publicação, selecione **Revisar e publicar**.

Enviaremos um e-mail para que você saiba quando uma versão de pré-visualização da oferta estiver disponível para revisar e aprovar. Para publicar sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center, localize a página **Visão Geral** da sua oferta e selecione **Go-live**. O status de sua oferta aparecerá no topo da página quando a publicação estiver em andamento.

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa **de validação manual** no processo de publicação representa uma ampla revisão de sua oferta e de seus ativos técnicos associados. Se esse processo descobrir erros com sua oferta, você receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e republicar sua oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da Oferta** mostra uma representação visual das etapas necessárias para publicar esta oferta (concluídas e em andamento) e quanto tempo cada passo deve levar para ser concluído.

Esta página inclui links para executar operações nesta oferta com base na seleção que você faz. Por exemplo:

- Se a oferta for um rascunho - [Exclua a oferta de rascunho](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se a oferta for ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta está em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se você ainda não completou a aprovação do editor - [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exemplos de marketplace

Esses exemplos mostram como a oferta aparece no Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace oferece detalhes

![Azure Marketplace oferece exemplo de tela de detalhes](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Resultados de pesquisa do Azure Marketplace

![Exemplo de tela de detalhes de pesquisa do Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Detalhes do plano do Azure Marketplace

![Plano do Azure Marketplace detalha exemplo de tela](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Portal Azure oferece detalhes

![Portal Azure oferece exemplo de tela de detalhes](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Resultados de pesquisa do portal Azure

![Exemplo de tela de resultados de pesquisa do portal Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Detalhes do plano do portal Azure

![Plano do portal Azure detalha exemplo de tela](media/avm-create6.png)

## <a name="next-step"></a>Próxima etapa

- [Atualize uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
