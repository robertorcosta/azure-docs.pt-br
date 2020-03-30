---
title: Acesse e personalize o portal de desenvolvedores gerenciados - Azure API Management | Microsoft Docs
description: Saiba como usar a versão gerenciada do portal do desenvolvedor no Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244089"
---
# <a name="access-and-customize-developer-portal"></a>Acesse e personalize o portal do desenvolvedor

O portal do desenvolvedor é um site gerado automaticamente e totalmente personalizável com a documentação de suas APIs. É onde os consumidores de API podem descobrir suas APIs, aprender como usá-las e solicitar acesso.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Acesse a versão gerenciada do portal do desenvolvedor
> * Navegue por sua interface administrativa
> * Personalize o conteúdo
> * Publique as alterações
> * Ver o portal publicado

Você pode encontrar mais detalhes no portal do desenvolvedor no portal de desenvolvedores do [Azure API Management](api-management-howto-developer-portal.md).

![Portal de desenvolvedores de Gerenciamento de API - modo de administração](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md)
- Importar e publicar uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [Importar e publicar](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Acesse o portal como administrador

Siga os passos abaixo para acessar a versão gerenciada do portal.

1. Vá para a sua instância de serviço de gerenciamento de API no portal Azure.
1. Clique no botão **do portal Desenvolvedor** na barra de navegação superior. Uma nova guia do navegador com uma versão administrativa do portal será aberta.

## <a name="understand-the-portals-administrative-interface"></a>Entenda a interface administrativa do portal

### <a name="default-content"></a>Conteúdo padrão 

Se você estiver acessando o portal pela primeira vez, o conteúdo padrão será provisionado automaticamente em segundo plano. O conteúdo padrão foi projetado para mostrar os recursos do portal e minimizar a quantidade de personalizações necessárias para personalizar seu portal. Você pode saber mais sobre o que está incluído no conteúdo do portal no portal de desenvolvedores do [Azure API Management](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Editor visual

Você pode personalizar o conteúdo do portal com o editor visual. As seções de menu à esquerda permitem criar ou modificar páginas, mídia, layouts, menus, estilos ou configurações do site. Os itens do menu na parte inferior permitem alternar entre portas de exibição (por exemplo, mobile ou desktop), visualizar os elementos do portal visíveis para usuários autenticados ou anônimos ou salvar ou desfazer ações.

Você pode adicionar linhas a uma página clicando em um ícone azul com um sinal de mais. Widgets (por exemplo, lista de texto, imagens ou APIs) podem ser adicionados pressionando um ícone cinza com um sinal de mais. Você pode reorganizar itens em uma página com a interação arrastar e soltar. 

### <a name="layouts-and-pages"></a>Layouts e páginas

![Páginas e layouts](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Os layouts definem como as páginas são exibidas. Por exemplo, no conteúdo padrão, há dois layouts - um se aplica à página inicial e o outro a todas as páginas restantes.

Um layout é aplicado a uma página combinando seu modelo de URL com a URL da página. Por exemplo, o layout `/wiki/*` com um modelo de URL `/wiki/` de será `/wiki/getting-started`aplicado `/wiki/styles`a cada página com o segmento na URL: , , etc.

Na imagem acima, o conteúdo pertencente ao layout é marcado em azul, enquanto a página está marcada em vermelho. As seções do menu estão marcadas, respectivamente.

### <a name="styling-guide"></a>Guia de styling

![Guia de styling](media/api-management-howto-developer-portal-customize/styling-guide.png)

Guia de styling é um painel criado com designers em mente. Permite supervisionar e estilizar todos os elementos visuais do seu portal. O estilo é hierárquico - muitos elementos herdam propriedades de outros elementos. Por exemplo, os elementos do botão usam cores para texto e fundo. Para alterar a cor de um botão, você precisa alterar a variante de cor original.

Para editar uma variante, clique nela e selecione o ícone de lápis que aparece em cima dela. Depois de fazer as alterações na janela pop-up, feche-a.

### <a name="save-button"></a>Botão Salvar

![Botão Salvar](media/api-management-howto-developer-portal-customize/save-button.png)

Sempre que você fizer uma alteração no portal, você precisa salvá-lo manualmente pressionando o botão **Salvar** no menu na parte inferior. Quando você salva suas alterações, o conteúdo modificado é carregado automaticamente no serviço de gerenciamento de API.

## <a name="customize-the-portals-content"></a>Personalize o conteúdo do portal

Antes de disponibilizar seu portal aos visitantes, você deve personalizar o conteúdo gerado automaticamente. As alterações recomendadas incluem os layouts, estilos e o conteúdo da página inicial.

> [!NOTE]
> Devido às considerações de integração, as seguintes páginas não `/404` `/500`podem `/captcha` `/change-password`ser `/config.json` `/confirm/invitation`removidas ou movidas uma URL diferente:,,, , `/confirm-v2/identities/basic/signup`, , `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, , `/signin`,,,,,,,, . `/signin-sso` `/signup`

### <a name="home-page"></a>Página inicial

A **página inicial** padrão está cheia de conteúdo de boneco. Você pode remover todas as seções com o conteúdo ou manter a estrutura e ajustar os elementos um a um. Substitua o texto e as imagens gerados pelo seu e certifique-se de que os links apontam para os locais desejados.

### <a name="layouts"></a>Layouts

Substitua o logotipo gerado automaticamente na barra de navegação por sua própria imagem.

### <a name="styling"></a>Estilo

Embora você não precise ajustar nenhum estilo, você pode considerar ajustar elementos específicos. Por exemplo, altere a cor principal para combinar com a cor da sua marca.

### <a name="customization-example"></a>Exemplo de personalização

No vídeo abaixo demonstramos como editar o conteúdo do portal, personalizar o visual do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publique o portal

Para disponibilizar seu portal e suas últimas alterações aos visitantes, você precisa publicá-lo.

1. Certifique-se de salvar suas alterações clicando no ícone **Salvar.**
1. Clique em **Publicar site** na seção **Operações** do menu. Esta operação pode levar alguns minutos.  
    ![Publicar portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> O portal precisa ser republicado após as alterações na configuração do serviço de gerenciamento de API, como atribuir um domínio personalizado, atualizar os provedores de identidade, definir a delegação, especificar termos de login e produto e muito mais.

## <a name="visit-the-published-portal"></a>Visite o portal publicado

Depois de publicar o portal, você pode acessá-lo `https://contoso-api.developer.azure-api.net`na mesma URL do painel administrativo, por exemplo. Visualizá-lo em uma sessão separada do navegador (modo de navegação anônima/privada) como visitante externo.

## <a name="apply-the-cors-policy-on-apis"></a>Aplique a política do CORS em APIs

Você precisa habilitar o CORS (compartilhamento de recursos de origem cruzada) em suas APIs para permitir que os visitantes do seu portal testem as APIs através do console interativo incorporado. Consulte [este artigo de documentação](api-management-howto-developer-portal.md#cors) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o portal do desenvolvedor:

- [Visão Geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md)
