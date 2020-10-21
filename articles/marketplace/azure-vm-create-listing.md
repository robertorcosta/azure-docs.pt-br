---
title: Configurar detalhes de listagem da oferta de máquina virtual no Azure Marketplace
description: Saiba como configurar detalhes de listagem da oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283566"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Como configurar detalhes de listagem de oferta de máquina virtual

Na página **Listagem de ofertas**, você define os detalhes da oferta, como nome da oferta, descrição, links e contatos.

> [!NOTE]
> O conteúdo de listagem de sua oferta, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "este aplicativo está disponível apenas no \<non-English language> ." Você também pode fornecer uma URL para vincular a um site que oferece conteúdo em um idioma diferente daquele usado no conteúdo da listagem de ofertas.

## <a name="marketplace-details"></a>Detalhes do Marketplace

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** quando você criou a oferta. Você pode alterar esse nome posteriormente. O nome:

- Pode ser registrado como marca. Pode ter símbolos de marca registrada e de direitos autorais incluídos por você.
- Não pode conter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 100 caracteres.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 256 caracteres.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Use marcas HTML para formatar sua descrição para que ela seja mais atraente. Para obter uma lista de marcas permitidas, consulte [marcas HTML com suporte](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira o endereço web (URL) da política de privacidade da sua organização. Verifique se a sua oferta está em conformidade com as leis e regulamentos de privacidade. Você também precisa postar uma política de privacidade válida em seu site.

## <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Para adicionar um link, selecione **Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Name**: os clientes verão o nome na página de detalhes.
- **Link (URL)** : insira um link que permita aos clientes ver seu documento online.

## <a name="customer-support-links"></a>Links de atendimento ao cliente

Forneça o site de suporte em que os clientes podem entrar em contato com sua equipe de suporte.

- Site de suporte do Azure Global
- Site de suporte do Azure Government

## <a name="partner-support-contact"></a>Contato de suporte para parceiros

Forneça informações de contato para os parceiros da Microsoft usarem quando os clientes abrirem um tíquete de suporte. Essas informações não estão listadas no Azure Marketplace.

- Nome
- Email
- Telefone

## <a name="engineering-contact"></a>Contato de engenharia

Forneça informações de contato da Microsoft para usar quando houver problemas com sua oferta, incluindo problemas com a certificação. Essas informações não estão listadas no Azure Marketplace.

- Nome
- Email
- Telefone

## <a name="azure-marketplace-media"></a>Mídia do Azure Marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Se você estiver enfrentando um problema ao carregar arquivos, verifique se a sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

### <a name="azure-marketplace-logos"></a>Logotipos do Azure Marketplace

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela precisa ter 1280 x 720 pixels de tamanho e estar no formato PNG. Cada captura de tela precisa incluir uma legenda.

### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstrem sua oferta. Os vídeos devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço Web e uma imagem PNG em miniatura do vídeo em 1280 x 720 pixels.

Para obter recursos adicionais de listagem do Marketplace, confira [Práticas recomendadas para listagens de ofertas do Marketplace](gtm-offer-listing-best-practices.md).

Selecione **Salvar rascunho** para continuar.

## <a name="next-steps"></a>Próximas etapas

- [Criar planos](azure-vm-create-plans.md)
