---
title: Criar uma oferta de serviço de consultoria - Marketplace comercial da Microsoft
description: Saiba como publicar uma oferta de serviço de consultoria para Microsoft AppSource ou Azure Marketplace usando a Central de Parceiros.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 7b42c12499977b5f7bc1704c57904e90be2e8466
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398007"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Microsoft AppSource](https://appsource.microsoft.com/) ou o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Liste as ofertas de serviço de consultoria baseadas no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform no AppSource. Listar ofertas de serviço de consultoria com base no Microsoft Azure no Azure Marketplace. Antes de começar, [crie uma conta do marketplace comercial na Central de Parceiros](create-account.md) se ainda não tiver uma. Verifique se sua conta está inscrita no programa do marketplace comercial.

Antes de criar sua oferta, revise os pré-requisitos em [Pré-requisitos de serviço de consultoria](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no Marketplace comercial:

- Promova sua empresa usando a marca Microsoft.
- Pode alcançar mais de 100 milhões usuários Microsoft 365 e Dynamics 365 em AppSource e mais de 200.000 organizações por meio do Azure Marketplace.
- Receba clientes potenciais de alta qualidade desses Marketplaces.
- Seus serviços podem ser promovidos pelas equipes de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre na [Central de Parceiros](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Serviço de consultoria**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

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

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) para participação do cliente
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se seu sistema de CRM não estiver listado acima, use a [tabela do Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou o [ponto de extremidade Https](commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de clientes potenciais e depois exporte os dados para seu sistema de CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicá-la na Central de Parceiros.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la na Central de Parceiros, validaremos a conexão e enviaremos um cliente potencial de teste para você. Quando você coloca a oferta em versão prévia antes de ela ficar ativa, pode também testar a conexão do cliente potencial tentando comprar a oferta por conta própria no ambiente de versão prévia.
4. A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

Veja alguns recursos adicionais de gerenciamento de clientes potenciais:

- [Visão geral do gerenciamento de clientes potenciais](commercial-marketplace-get-customer-leads.md)
- [Perguntas frequentes sobre gerenciamento de leads](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Erros comuns de configuração de leads](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
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

3. Se você selecionou **Azure** como produto principal, selecione até três **áreas de solução**. Isso facilita para os clientes do Azure Marketplace encontrarem sua oferta. Se você não escolheu o Azure, pule esta etapa.

    - Análise
    - Modernização do aplicativo
    - Archive
    - IA + Machine Learning
    - Backup
    - Big Data
    - Plataforma de dados
    - Gerenciamento de datacenter
    - DevOps
    - Recuperação de desastre
    - Identidade
    - Internet das coisas
    - Migração
    - Rede
    - Segurança
    - Armazenamento

1. Se você selecionou **Azure** como produto principal, terá a opção de selecionar até seis **indústrias**. Isso facilita para os clientes do Azure Marketplace encontrarem sua oferta. Consulte a lista completa de setores em [práticas recomendadas de listagem de ofertas](../gtm-offer-listing-best-practices.md). Se você não escolheu o Azure, pule esta etapa.
1. Se você selecionou um produto principal *diferente* do Azure, selecione até três **produtos aplicáveis**. Isso facilita para os clientes do AppSource encontrarem sua oferta. Para mais informações, consulte [Diretrizes de listagem de serviço de consultoria do Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
1. Se você selecionou um produto primário *diferente* do Azure, terá a opção de selecionar até duas **indústrias** e duas **verticais** para cada setor. Isso facilita para os clientes do AppSource encontrarem sua oferta. Veja a lista completa de setores e verticais na [oferta de práticas recomendadas de listagem](../gtm-offer-listing-best-practices.md).
1. Adicione até três **competências** que sua empresa ganhou para mostrar na sua listagem de ofertas de serviços de consultoria. Pelo menos uma competência é necessária, exceto para MSPs Especialista em Azure e MSPs da Rede do Azure.

Selecione **Salvar rascunho** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que são mostrados no marketplace. Isso inclui o nome da oferta, a descrição, as imagens etc. Siga as políticas detalhadas na [página das políticas de certificação do Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) ao configurar essa oferta.

> [!NOTE]
> Os detalhes da oferta não precisam estar em inglês se a descrição da oferta iniciar com a frase, &quot;Este aplicativo só está disponível em [idioma, exceto inglês].&quot; Também é bom fornecer um link útil de conteúdo em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

Aqui está um exemplo de como as informações de oferta são exibidas no Azure Marketplace (os preços listados são apenas para fins de exemplo e não se destinam a refletir os custos reais):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Preço
3. Áreas de solução
4. Setores
5. Nome da oferta
6. Resumo
7. Descrição
8. Capturas de tela/vídeos

<br>Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Preço
3. Produtos
4. Setores
5. Nome da oferta
6. Resumo
7. Descrição
8. Capturas de tela/vídeos
9. Documentos

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** ao criar a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser um nome comercial (e você pode incluir os símbolos de marca registrada ou de direitos autorais).
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

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center o usará para criar um logotipo **pequeno** . Você pode, opcionalmente, substituir isso por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na lista.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada uma deve ter 1280 x 720 pixels de tamanho e estar no formato PNG.

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

Para mais informações sobre a estimativa de tempo que a oferta ficará em cada estágio de publicação, consulte [Verificar o status de publicação de sua oferta do Marketplace comercial](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Atualizar suas ofertas de serviço de consultoria existentes

- [Atualizar uma oferta existente no marketplace comercial](update-existing-offer.md)
