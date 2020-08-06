---
title: Criar uma oferta de aplicativo Power BI no Microsoft AppSource
description: Saiba como criar e publicar uma oferta de aplicativo Power BI para Microsoft AppSource.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c464e26419648d396b39718f357bcef7e4fa5552
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800290"
---
# <a name="create-a-power-bi-app-offer"></a>Criar uma oferta de aplicativo Power BI

Este artigo descreve como criar e publicar uma oferta de aplicativo Power BI para o Microsoft [AppSource](https://appsource.microsoft.com/).

Antes de começar, [crie uma conta do marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página de Visão geral, selecione **+ Nova oferta** > **Aplicativo de Serviço Power BI**.

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Depois que uma oferta for publicada, as edições feitas nela no Partner Center aparecerão somente em frentes de loja após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

> [!IMPORTANT]
> Se **aplicativo de serviço Power BI** não for mostrado ou habilitado, sua conta não terá permissão para criar esse tipo de oferta. Verifique se você atendeu a todos os [requisitos](create-power-bi-app-overview.md) para esse tipo de oferta, incluindo o registro de uma conta de desenvolvedor.

## <a name="new-offer"></a>Nova oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço Web para a oferta do marketplace e nos modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1** aqui, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- O Alias da oferta não pode ser alterado depois que você seleciona **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Visão geral da oferta

Esta página mostra uma representação visual das etapas necessárias para publicar esta oferta (concluída e próxima) e por quanto tempo cada etapa deve levar para ser concluída.

Ela inclui links para executar operações nessa oferta com base na seleção feita por você. Por exemplo:

- Se a oferta for uma oferta rascunho- [excluir rascunho](update-existing-offer.md#delete-a-draft-offer))
- Se a oferta estiver em tempo real, [pare de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan))
- Se a oferta estiver em visualização- [Go-Live](publishing-status.md#publisher-approval))
- Se você ainda não concluiu a saída do Publicador- [Cancelar publicação](update-existing-offer.md#cancel-publishing))

## <a name="offer-setup"></a>Configuração da oferta

### <a name="customer-leads"></a>Clientes potenciais

Quando você publica sua oferta no marketplace com o Partner Center, conecte-a ao sistema CRM (gerenciamento de relacionamentos com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center dá suporte aos seguintes sistemas de CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) para Participação do Usuário
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se o sistema CRM não estiver listado acima, use a [Tabela do Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou o [Ponto de Extremidade Https](commercial-marketplace-lead-management-instructions-https.md) para armazenar os dados do cliente potencial. Em seguida, exporte os dados para o sistema de CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicá-la na Central de Parceiros.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la na Central de Parceiros, validaremos a conexão e enviaremos um cliente potencial de teste para você. Quando você coloca a oferta em versão prévia antes de ela ficar ativa, pode também testar a conexão do cliente potencial tentando comprar a oferta por conta própria no ambiente de versão prévia.
4. A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

Veja alguns recursos adicionais de gerenciamento de clientes potenciais:

- [Visão geral do gerenciamento de clientes potenciais](commercial-marketplace-get-customer-leads.md)
- [Perguntas frequentes sobre gerenciamento de leads](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Erros comuns de configuração de leads](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Visão geral do gerenciamento de clientes potenciais](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) em PDF (verifique se o bloqueador de pop-up está desativado)

Selecione **Salvar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite que você defina as categorias e os setores usados para agrupar sua oferta no marketplace, a versão do seu aplicativo e os contratos legais que dão suporte à sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Legal

#### <a name="terms-and-conditions"></a>Termos e condições

Para fornecer os termos e as condições personalizados, insira até 10.000 caracteres na caixa **Termos e condições**. Se os termos e as condições exigirem uma descrição mais longa, insira um único link da Web no qual eles podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Para que os clientes experimentem sua oferta, eles devem aceitar esses termos.

Selecione **Salvar rascunho** antes de seguir para a próxima seção chamada Listagem de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que são mostrados no marketplace. Isso inclui o nome da oferta, a descrição, as imagens etc.

### <a name="language"></a>Linguagem

Selecione o idioma no qual sua oferta será listada. Atualmente, **Inglês (Estados Unidos)** é a única opção disponível.

Defina os detalhes do marketplace (como nome da oferta, descrição e imagens) para cada idioma/mercado. Selecione o nome do idioma/mercado para fornecer essas informações.

> [!NOTE]
> Os detalhes da oferta não precisam estar em inglês caso a descrição da oferta inicie com a frase: "Este aplicativo só está disponível em [idioma diferente do inglês]." Também é bom fornecer um link útil de conteúdo em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Produtos
3. Categorias
4. Setores
5. Endereço de suporte (link)
6. Termos de uso
7. Política de privacidade
8. Nome da oferta
9. Resumo
10. Descrição
11. Capturas de tela/vídeos

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

### <a name="search-keywords"></a>Palavras-chave para pesquisa

Insira até três palavras-chave de pesquisa opcionais para ajudar os clientes a localizar sua oferta no marketplace. Para obter melhores resultados, use essas palavras-chave em sua descrição também.

### <a name="helpprivacy-web-addresses"></a>Endereços Web de Ajuda/Privacidade

Forneça links para ajudar os clientes a entenderem melhor sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira o endereço Web onde os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira o endereço Web para a política de privacidade da sua organização. Você é o responsável por garantir que sua oferta esteja em conformidade com as leis e os regulamentos de privacidade. Você também é o responsável por publicar uma política de privacidade válida em seu site.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o e-mail e o número de telefone de um **Contato de suporte** e um **Contato de engenharia**. Essas informações não são mostradas aos clientes. Elas estão disponíveis para a Microsoft e podem ser fornecidas a parceiros CSP (provedores de solução de nuvem).

- Contato de suporte (obrigatório): Para questões gerais de suporte.
- Contato de engenharia (obrigatório): para questões técnicas e problemas de certificação.
- Contato de programa CSP (opcional): Para questões sobre o revendedor relacionadas ao programa CSP.

Na seção **Contato do suporte**, forneça o endereço Web do **site de suporte** onde os parceiros podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Forneça pelo menos um e até três documentos de marketing relacionados no formato PDF. Por exemplo, white papers, folhetos, listas de verificação ou apresentações.

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. Imagens borradas serão rejeitadas.

>[!NOTE]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço `https://upload.xboxlive.com` que é usado pelo Partner Center.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** (entre 216 x 216 e 350 x 350 pixels). O Partner Center usará isso para criar um logotipo de tamanho **pequeno** (48 x 48 pixels). Opcionalmente, você pode substituir isso por uma imagem diferente.

Os dois tamanhos de logotipo são necessários para uso em locais diferentes na lista:

- **Pequeno** (48 x 48)
- **Grande** (entre 216 x 216 e 350 x 350)

[!INCLUDE [Logo suggestions](./includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de tela

Adicione pelo menos uma e até cinco capturas de tela que mostram como sua oferta funciona. Cada uma deve ter 1280 x 720 pixels de tamanho e estar no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstrem sua oferta. Insira o nome do vídeo, seu endereço Web e a imagem PNG de miniatura do vídeo com 1280 x 720 pixels de tamanho.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

Para saber mais sobre a criação de listagens de ofertas, confira [Melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Configuração técnica

Promova seu aplicativo no serviço Power BI para produção e forneça o link do instalador do aplicativo Power BI que permite que os clientes instalem seu aplicativo. Para obter mais informações, consulte [Publicar aplicativos com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Conteúdo complementar

Forneça informações adicionais sobre sua oferta para nos ajudar a validá-la. Essas informações não são mostradas aos clientes ou publicadas no marketplace.

### <a name="validation-assets"></a>Ativos de validação

Opcionalmente, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua definições de configuração típicas, contas, parâmetros ou outras informações que podem ser usadas para testar a opção Conectar Dados. Essas informações são visíveis somente para a equipe de validação e são usadas somente para fins de validação.

## <a name="review-and-publish"></a>Examinar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-la para revisão e publicação.

No canto superior direito do portal, selecione **Revisar e publicar**.

Na página de revisão, você pode:

- Ver o status de conclusão de cada seção da oferta. Você não conseguirá publicar até que todas as seções da oferta estejam marcadas como concluídas.
  - **Não iniciada** – a seção não foi iniciada e precisa ser concluída.
  - **Incompleta** – a seção tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores deste documento para obter orientações.
  - **Concluída** – a seção tem todos os dados necessários e não há erros. Todas as seções da oferta precisam ser concluídas para que você envie a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente. Também forneça observações suplementares que ajudem a entender a sua oferta.

Para enviar a oferta para publicação, selecione **Publicar**.

Enviaremos um email para que você saiba quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Para publicar sua oferta no público, vá para o Partner Center e selecione **Go-Live**.
