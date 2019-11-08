---
title: Guia Marketplace da oferta de aplicativo do Azure
description: Use a guia Marketplace para identificar ativos de marketing para uma oferta de aplicativo do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 967b66a67d51b3a79bcf930ce977af48acc3dd63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827574"
---
# <a name="azure-application-marketplace-tab"></a>Guia Marketplace de aplicativo do Azure

Use a guia Marketplace para descrever seu aplicativo do Azure e fornecer ativos de marketing. Essa guia inclui as seguintes formas: visão geral, artefatos de marketing, gerenciamento de leads e ofício.

## <a name="overview-form"></a>Formulário Visão Geral

O formulário Visão Geral tem os campos obrigatórios e opcionais mostrados na próxima captura de tela. Os campos obrigatórios são indicados por um asterisco (*).

![Formulário Visão Geral](./media/azureapp-marketplace-overview.png)

A tabela a seguir descreve as configurações a serem usadas para a criação de uma vitrine para a oferta.   Os campos anexados a um asterisco são obrigatórios.

|      Campo         |    DESCRIÇÃO    |
|  ---------------   |  ---------------  |
| **Título\***        | Título da oferta. Ele será exibido com destaque no marketplace. O tamanho máximo é de 50 caracteres. |
| **\* de resumo**      | Breve resumo da oferta. O tamanho máximo é de 100 caracteres.           |
| **Resumo longo\*** | Resumo mais longo da oferta (embora possa ser igual ao resumo). O tamanho máximo é de 256 caracteres.           |
| **Descrição\***  | Descrição da oferta. O tamanho máximo é de 3.000 caracteres. É permitido HTML simples, incluindo &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e marcas de cabeçalho.  |
| **Identificador de marketing\*** | Um URL exclusivo para associar a essa oferta geralmente inclui sua organização e o nome da solução, com um comprimento máximo de 50 caracteres. Escolha um identificador de marketing curto e amigável para o serviço. Isso será usado nas URLs do marketplace para essa oferta. Por exemplo, se sua ID de editor for "contoso" e seu identificador de marketing for "sampleApp", a URL para sua oferta no Azure Marketplace será https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Visualizar IDs de assinatura\*** | Adicione de um a 100 identificadores de assinatura de visualizadores. Essas assinaturas listadas em branco terão acesso à sua oferta, enquanto estiverem disponíveis na visualização depois de publicadas, antes de entrar em tempo real.          |
| **Links úteis**    | Opcionalmente, você pode fornecer links para vários recursos para os usuários de sua oferta, como suporte, documentação, fóruns, etc.  É recomendável que você adicione pelo menos um link à sua documentação.            |
| **Categorias sugeridas (máx. 5)\*** | Selecione uma a cinco categorias. As categorias selecionadas são usadas para mapear a oferta para as categorias de produto disponíveis no Azure Marketplace e no portal do Azure. Elas serão mostradas nas páginas de navegação e na página de detalhes do produto. |
|  |  |


## <a name="marketing-artifacts"></a>Artefatos de marketing

O formulário Artefatos de Marketing tem os campos obrigatórios e opcionais mostrados na próxima captura de tela. Os campos obrigatórios são indicados por um asterisco (*).

![Formulário Artefatos de Marketing](./media/azureapp-marketplace-artifacts.png)

A tabela a seguir descreve os artefatos de marketing.

|      Campo         |    DESCRIÇÃO    |
|  ---------------   |  ---------------  |
| **Pequeno\***        | Logotipo pequeno: 40x40 pixels no formato PNG     |
| **Médio\***       | Logotipo médio: 90x90 pixels no formato PNG    |
| **Grande\***        | Logotipo grande: 115x115 pixels no formato PNG   |
| **\* largo**         | Logotipo amplo: 255x115 pixels no formato PNG    |
| **Hero**           | Logotipo opcional do Hero: 815x290 pixels no formato PNG. **Observação:** O ícone Hero não pode ser excluído após ser carregado. |
| **Capturas de tela (máximo de 5)** |        Capturas de tela são exibidas na sua página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que o aplicativo faz e como ele funciona. Por exemplo, você pode mostrar diagramas de arquitetura ou usar ilustrações de casos. As capturas de tela são opcionais e limitadas a 5 por SKU. Para adicionar uma captura de tela:<ul><li>Selecione **+ Adicionar captura de tela** para abrir a janela Captura de Tela</li><li>**Nome** – Insira um nome/título (tamanho máximo de 100 caracteres).</li><li>**Upload** – Carregue a imagem. Ela precisa estar no formato PNG e o tamanho é 533x324 pixels.</li></ul>           |
| **Adicionar vídeo**      | Opcional, os vídeos são exibidos na página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que o aplicativo faz e como ele funciona. Para adicionar um vídeo: <ul><li>Selecione **+ Adicionar vídeo** para abrir a janela Vídeo</li><li>**Nome** – Insira um nome/título (tamanho máximo de 100 caracteres).</li><li>**Link** -Insira a URL para o site que está hospedando o vídeo (YouTube ou Vimeo)</li><li>**Miniatura** – carregar uma miniatura. Ela precisa estar no formato PNG e o tamanho é 533x324 pixels.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Exemplos de artefato no Azure Marketplace

A próxima captura de tela mostra um exemplo de resultado de pesquisa do Marketplace.

![Resultado da pesquisa de ofertas do Marketplace](./media/azureapp-marketplace-example-browse.png)

A imagem a seguir mostra como a oferta é exibida no Marketplace depois que um cliente clica no bloco da oferta no resultado da pesquisa.

![Detalhes do resultado da pesquisa de ofertas de Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Exemplos de artefato no portal do Azure

As capturas de tela a seguir mostram como uma oferta é exibida no portal do Azure. A oferta do aplicativo neste exemplo é encontrada navegando para **Marketplace > Tudo > Desenvolvimento + Teste > Jenkins**. A oferta do Jenkins mostra um logotipo, um título e o nome de exibição do publicador.

![Procurar ofertas no portal do Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A próxima captura de tela mostra informações detalhadas sobre o aplicativo quando um usuário seleciona o Jenkins.

![Detalhes da oferta no portal do Azure](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Diretrizes de logotipo

Todos os logotipos enviados para o Cloud Partner Portal devem seguir as diretrizes:

- O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.
- As cores de tema do Portal do Azure são branco e preto. Evite usar essas cores como a cor de fundo para seus logotipos. Use uma cor que torne seus logotipos proeminentes no portal do Azure. É recomendável usar cores primárias simples. Se você estiver usando um plano de fundo transparente, verifique se os logotipos / texto não são branco, preto ou azul.
- Não use um plano de fundo gradiente no seu logotipo.
- Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo. A aparência do seu logotipo deve ser "plana" e deve evitar gradientes.
- Não se estendem o logotipo.


#### <a name="hero-logo"></a>Logotipo Hero

O logotipo Hero é opcional.

>[!IMPORTANT]
>Não é possível excluir o logotipo Hero depois que ele é carregado.

Use as seguintes diretrizes para um logotipo do Herói:

- Fundos pretos, brancos e transparentes não são permitidos.
- Evite usar qualquer cor clara como plano de fundo para o logotipo. O nome de exibição do publicador, o título do plano e o resumo longo da oferta são exibidos na cor de fonte branca e precisam se destacar em relação à tela de fundo.
- Evite usar a maioria dos textos quando estiver criando o logotipo. O nome do editor, o título do plano, o resumo longo da oferta e um botão de criação são incorporados programaticamente dentro do logotipo quando a oferta é listada.
- Inclua um espaço retangular não utilizado no lado direito do logotipo da imagem Hero. Esse espaço em branco tem 415x100 pixels e é deslocado da esquerda em 370 pixels.


## <a name="lead-management"></a>Gerenciamento de leads

O formulário Gerenciamento de Clientes Potenciais tem um campo opcional para configurar o gerenciamento de clientes potenciais. Para configurar o gerenciamento de clientes potenciais, selecione o destino Clientes Potenciais na lista suspensa. A próxima captura de tela mostra os destinos disponíveis.

![Selecionar destino do gerenciamento de clientes potenciais](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Selecione o ícone de informações para ver esta mensagem: "Selecione o sistema no qual seus leads serão armazenados. Saiba como se conectar ao sistema CRM [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "

Para obter mais informações, confira [Configurar clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Legal

Use o formulário Jurídico para fornecer a documentação legal necessária para cada oferta.

Forneça as seguintes informações:

- **URL da política de privacidade\*** -Insira um link para a política de privacidade do aplicativo.
- **Termos de uso\*** -Insira os termos de uso do seu aplicativo. Os clientes precisam aceitar esses termos antes de poderem testar o aplicativo.

![Formulário Jurídico](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Próximas etapas

[Guia Suporte](./cpp-support-tab.md)
