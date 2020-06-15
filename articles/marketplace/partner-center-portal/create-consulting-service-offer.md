---
title: Criar uma oferta de serviço de consultoria - Marketplace comercial da Microsoft
description: Saiba como publicar uma oferta de serviço de consultoria para Microsoft AppSource ou Azure Marketplace usando a Central de Parceiros.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: bd9d643708515855e81ac6729843388a690b2c45
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700965"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Microsoft AppSource](https://appsource.microsoft.com/) ou o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Liste as ofertas de serviço de consultoria baseadas no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform no AppSource. Listar ofertas de serviço de consultoria com base no Microsoft Azure no Azure Marketplace. Antes de começar, [crie uma conta do marketplace comercial na Central de Parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se ainda não tiver uma. Verifique se sua conta está inscrita no programa do marketplace comercial.

Antes de criar sua oferta, revise os pré-requisitos em [Pré-requisitos de serviço de consultoria](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no Marketplace comercial:

- Promova sua empresa usando a marca Microsoft.
- Tenha acesso a mais de 100 milhões de usuários do Office 365 e Dynamics 365 no AppSource e mais de 200.000 organizações pelo Azure Marketplace.
- Receba clientes potenciais de alta qualidade desses Marketplaces.
- Seus serviços podem ser promovidos pelas equipes de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre na [Central de Parceiros](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Serviço de consultoria**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Depois que uma oferta for publicada, as edições feitas nela na Central de Parceiros aparecerão somente em frentes de loja após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID aparece para os clientes no endereço web da oferta do Marketplace.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta na Central de Parceiros.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O Alias da oferta não pode ser alterado depois que você seleciona **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração da oferta

### <a name="connect-lead-management"></a>Conectar gerenciamento de clientes potenciais

Ao publicar sua oferta no Marketplace com a Central de Parceiros, você _precisa_ conectá-la a um sistema de gerenciamento de relacionamentos com o cliente (CRM) ou de automação de marketing. Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo.

1. Selecione **Conectar** para especificar para onde devemos enviar seus clientes potenciais. A Central de Parceiros dá suporte aos seguintes sistemas de CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para participação do cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se seu sistema de CRM não estiver listado acima, use a [tabela do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou o [ponto de extremidade Https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar dados de clientes potenciais e depois exporte os dados para seu sistema de CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicá-la na Central de Parceiros.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la na Central de Parceiros, validaremos a conexão e enviaremos um cliente potencial de teste para você. Quando você coloca a oferta em versão prévia antes de ela ficar ativa, pode também testar a conexão do cliente potencial tentando comprar a oferta por conta própria no ambiente de versão prévia.
4. A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

Veja alguns recursos adicionais de gerenciamento de clientes potenciais:

- [Visão geral do gerenciamento de clientes potenciais](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Perguntas frequentes sobre gerenciamento de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de leads](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Visão geral do gerenciamento de clientes potenciais](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) em PDF (desative seu bloqueador de pop-up)

Selecione **Salvar rascunho** antes de continuar.

### <a name="properties"></a>Propriedades

Essa página permite definir o produto principal para o qual seu serviço de consultoria oferece a melhor cobertura, configurar um tipo de serviço de consultoria e escolher produtos aplicáveis.

1. Selecione um **produto principal** na lista suspensa.
2. Selecione um **tipo de serviço de consultoria** na lista suspensa:

    - **Avaliação**: Uma avaliação do ambiente do cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Resumo**: Uma introdução a uma solução ou um serviço de consultoria para atrair o interesse do cliente utilizando estruturas, demonstrações e exemplos de clientes.
    - **Implementação**: uma instalação completa que resulta em uma solução totalmente funcional. Limite a soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito**: uma implementação de escopo limitado para determinar se a solução atende aos requisitos do cliente.
    - **Workshop**: Um engajamento interativo realizado nas instalações do cliente. Pode envolver treinamento, resumos, avaliações ou demonstrações criados no ambiente ou nos dados do cliente.

3. Se você selecionou um produto principal do **Azure**, selecione até três **áreas da solução**. Isso facilita para os clientes do Azure Marketplace encontrarem sua oferta. Se você não escolheu o Azure, pule esta etapa.
4. Se você selecionou um produto principal _diferente_ do Azure, selecione até três **produtos aplicáveis**. Isso facilita para os clientes do AppSource encontrarem sua oferta. Para mais informações, consulte [Diretrizes de listagem de serviço de consultoria do Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
5. Selecione até seis **setores** aos quais sua oferta se aplica. Isso facilitará para os clientes encontrarem sua oferta.
6. Adicione até três **competências** que sua empresa ganhou para mostrar na sua listagem de ofertas de serviços de consultoria. Pelo menos uma competência é necessária, exceto para MSPs Especialista em Azure e MSPs da Rede do Azure.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que serão mostrados no Marketplace. Isso inclui o nome da oferta, a descrição, as imagens etc. Siga as políticas detalhadas na [página das políticas de certificação do Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) ao configurar essa oferta.

> [!NOTE]
> Os detalhes da oferta não precisam estar em inglês se a descrição da oferta iniciar com a frase, &quot;Este aplicativo só está disponível em [idioma, exceto inglês].&quot; Também é bom fornecer um link útil de conteúdo em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** ao criar a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser um nome comercial (e você pode incluir os símbolos de marca registrada ou de copyright).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Descreva brevemente a sua oferta. A descrição pode ter até 100 caracteres e é usada nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>Palavras-chave

Insira até três termos de pesquisa pertinentes ao seu produto principal e serviço de consultoria. Isso facilitará encontrar a sua oferta.

### <a name="duration"></a>Duration

Defina a duração esperada da participação do cliente.

### <a name="contact-information"></a>Informações de contato

Você precisa informar o nome, email e telefone de um **contato principal** e **secundário**. Essas informações não são mostradas aos clientes. Elas estão disponíveis à Microsoft e podem ser fornecidas a parceiros provedores de solução de nuvem (CSP).

### <a name="supporting-documents"></a>Documentos de suporte

Adicione de 1 a 3 documentos de suporte em PDF para sua oferta.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. Todas as imagens precisam estar no formato .png. Imagens borradas serão rejeitadas.

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pela Central de Parceiros.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça arquivos PNG do logotipo da sua oferta em cada um dos seguintes três tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logotipos são necessários e são usados em locais diferentes na lista do Marketplace.

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada uma deve ter 1280x720 pixels de tamanho e estar no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até quatro vídeos que demonstrem sua oferta. Insira o nome do vídeo, seu endereço web (URL) e uma imagem PNG em miniatura do vídeo com tamanho de 1280x720 pixels.

Selecione **Salvar rascunho** antes de continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui, você definirá elementos como preços, mercado e uma chave privada.

1. **Mercado**: Defina o mercado em que sua oferta estará disponível. Você pode selecionar apenas um mercado por oferta.
    1. Para os mercados Estados Unidos ou Canadá, selecione **Editar estados** (ou **províncias**) para especificar onde a oferta estará disponível.
2. **Público-alvo de versão prévia**: Configure a **Chave de ocultar** usada para definir o público particular para sua oferta.
3. **Preço**: Especifique se sua oferta é **gratuita** ou **paga**.

    > [!NOTE]
    > As ofertas de serviço de consultoria são apenas para a listagem. Todas as transações ocorrerão diretamente, fora do Marketplace comercial.

4. Para uma oferta paga, especifique **preço e moeda** e se o preço é **fixo** ou **estimado**. Se for estimado, você precisará especificar na descrição quais fatores afetarão o preço.
5. Selecione **Salvar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Examinar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-la para revisão e publicação.

1. Quando estiver tudo pronto para publicar sua oferta de serviço de consultoria, clique em **Revisar e publicar**.
2. Revise os detalhes na página final de envio.
3. Escreva uma observação para a equipe de certificação se entender que algum dos detalhes da sua oferta precisa de explicação.
4. Quando estiver tudo pronto, selecione **Fechar**.
5. A página **Visão geral da oferta** mostra em qual estágio de publicação sua oferta está.

Para mais informações sobre a estimativa de tempo que a oferta ficará em cada estágio de publicação, consulte [Verificar o status de publicação de sua oferta do Marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Atualizar suas ofertas de serviço de consultoria existentes

- [Atualizar uma oferta existente no marketplace comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
