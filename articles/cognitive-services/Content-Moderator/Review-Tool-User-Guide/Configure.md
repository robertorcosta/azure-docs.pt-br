---
title: Configurar configurações da ferramenta de revisão - Moderador de conteúdo
titleSuffix: Azure Cognitive Services
description: Use a ferramenta Revisão para configurar ou recuperar sua equipe, tags, conectores, fluxos de trabalho e credenciais para Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220122"
---
# <a name="configure-the-review-tool"></a>Configurar a ferramenta de análise

A [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com) tem vários recursos importantes que você pode acessar através do menu **Configurações** no painel.

![Menu de configurações do Moderador de Conteúdo também](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerenciar equipes e subequipes

A guia **Equipe** permite que você&mdash;gerencie sua equipe e grupos de subtimes de usuários que podem ser notificados quando [determinadas avaliações humanas](../review-api.md#reviews) forem iniciadas. Você só pode ter uma equipe (que você cria quando se inscreve com a ferramenta Revisão), mas você pode criar várias subequipes. O administrador da equipe pode convidar os membros, definir suas permissões e atribuí-las a diferentes subequipes.

![Revisar as configurações da equipe da ferramenta](images/settings-2-team.png)

Subequipes são úteis para a criação de equipes de escalonamento ou equipes dedicadas para revisar categorias específicas de conteúdo. Por exemplo, você pode enviar conteúdo adulto para uma equipe separada para revisão posterior.

Esta seção explica como criar subequipes e atribuir rapidamente avaliações em tempo real. No entanto, você pode usar [Fluxos de trabalho](workflows.md) para atribuir revisões com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar uma subequipe

Vá para a seção **Subtimes** e clique **em Adicionar Subequipe**. Digite o nome do seu subtime na caixa de diálogo e clique **em Salvar**.

![Nome da subequipe](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas de equipe

Você não pode atribuir alguém a uma subequipe se ele ainda não for um membro da equipe padrão, então você precisa adicionar revisores à equipe padrão primeiro. Clique em **Convidar** na guia **Equipe.**

![Convidar usuários](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Designar colegas de equipe para subequipe

Clique no botão **Adicionar membro** para atribuir membros de sua equipe padrão a uma ou mais subequipes. Você só pode adicionar usuários existentes para uma subequipe. Para adicionar novos usuários que não estão na ferramenta de análise, convidá-los usando o botão "Convidar" na página de Configurações de equipe.

![Atribuir membros da subequipe](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuir avaliações a subtimes

Depois de criar suas subequipes e membros [designados,](../review-api.md#reviews) você pode começar a atribuir avaliações de conteúdo a essas subequipes. Isso é feito a partir da guia **Revisão** do site.
Para atribuir conteúdo a uma subequipe, clique nas elipses no canto superior direito, selecione **Mover para**e selecione uma subequipe.

![Atribuir a revisão de imagem à subequipe](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subequipes

Se você é membro de mais de um subtime, você pode alternar entre essas subequipes para alterar quais avaliações de conteúdo são exibidas para você. Na guia **'''Revisão',** selecione o menu suspenso', identificado **como Padrão** e selecione **'''''''''''''''''''''''''''''''''''''''''** Você pode visualizar as avaliações de conteúdo para diferentes subtimes, mas apenas aquelas das quais você é membro.

![Alternar entre subequipes](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Marcas

A guia **Tags** permite definir tags de moderação personalizadas, além das duas tags&mdash;de moderação padrão **(a)** e **isracy** **(r).****isadult** Quando você cria uma tag personalizada, ela fica disponível em avaliações ao lado das tags padrão. Você pode alterar quais tags aparecem nas avaliações alterando suas configurações de visibilidade.

![Exibição de tags, incluindo as caixas de seleção "É visível"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar tags personalizadas

Para criar uma nova tag, você deve inserir um código curto, nome e descrição nos respectivos campos.

- **Código curto**: Digite um código de duas letras para sua tag. Exemplo: **cb**
- **Nome**: Digite um nome de tag curto e descritivo em minúsculas sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) Digite uma descrição do tipo de conteúdo que sua tag tem como alvo. Exemplo: **Representações ou instâncias de cyber bullying**.

Clique **em Adicionar** para adicionar uma tag e clique em **Salvar** quando terminar de criar tags.

![Ferramenta de revisão criar nova tag dialog](images/settings-3-tags.png)

### <a name="delete-tags"></a>Excluir tags

Você pode excluir tags personalizadas selecionando o ícone de lixo ao lado de suas entradas na lista Tags, mas não é possível excluir as tags padrão.

## <a name="connectors"></a>Conectores

A guia **Conectores** permite que você gerencie seus conectores, que são plugins específicos de serviço que podem processar conteúdo de diferentes maneiras como parte dos [fluxos](../review-api.md#workflows)de trabalho de conteúdo .

O conector padrão quando você cria um fluxo de trabalho é o conector Moderador de Conteúdo, que pode marcar conteúdo como **adulto** ou **picante,** encontrar palavrões e assim por diante. No entanto, você pode usar outros conectores, listados aqui, desde que você tenha credenciais para seus respectivos serviços (para usar o conector Face, por exemplo, você precisará obter uma chave de assinatura [face).](https://docs.microsoft.com/azure/cognitive-services/face/overview)

A [ferramenta Revisão](./human-in-the-loop.md) inclui os seguintes conectores:

- Emoção
- Face
- Serviço de nuvem PhotoDNA
- Análise de texto

### <a name="add-a-connector"></a>Adicionar um conexão

Para adicionar um conector (e disponibilizá-lo para uso em [fluxos de trabalho de](../review-api.md#workflows)conteúdo), selecione o botão **Connect** apropriado. Na próxima caixa de diálogo, digite sua chave de assinatura para esse serviço. Quando terminar, seu novo conector deve aparecer na parte superior da página.

![Configurações de conectores do Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

A guia **fluxos de trabalho** permite gerenciar seus [fluxos de trabalho](../review-api.md#workflows). Os fluxos de trabalho são filtros baseados em nuvem para conteúdo, e trabalham com conectores para classificar o conteúdo de diferentes maneiras e tomar ações apropriadas. Aqui, você pode definir, editar e testar seus fluxos de trabalho. Consulte [Definir e usar fluxos de trabalho](Workflows.md) para orientação sobre como fazer isso.

![Configurações de fluxo de trabalho do Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

A guia **Credenciais** fornece acesso rápido à sua chave de assinatura do Moderador de Conteúdo, que você precisará acessar qualquer um dos serviços de moderação de uma chamada REST ou sDK cliente.

![Credenciais do Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Use credenciais externas para fluxos de trabalho

A [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços do Azure Content Moderator quando você se inscreve, mas também pode configurá-la para usar uma chave existente da sua conta do Azure. Isso é recomendado para cenários de grande escala, pois as chaves de avaliação gratuitas têm limites de uso rigorosos[(preços e limites).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Se você criou um [recurso moderador de conteúdo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) no Azure, navegue até ele no portal Azure e selecione a lâmina **Chaves.** Copie uma de suas chaves.

![Chaves do Content Moderator no portal do Azure](images/credentials-azure-portal-keys.PNG)

Na guia **'Credenciais'** da [ferramenta Revisão,](https://contentmoderator.cognitive.microsoft.com)vá para o painel **Configurações do fluxo de trabalho,** selecione **Editar**e cole sua chave no campo Chave de **Assinatura Ocp-Apim.** Agora, os fluxos de trabalho que chamam de APIs de moderação usarão sua credencial do Azure.

> [!NOTE]
> Os outros dois campos no painel **Configurações de fluxo de trabalho** são para listas de termo e imagem personalizadas. Consulte os [termos personalizados](../try-terms-list-api.md) ou guias de [imagens personalizadas](../try-image-list-api.md) para saber sobre isso.

### <a name="use-your-azure-account-with-the-review-apis"></a>Use sua conta do Azure com as APIs de revisão

Para usar sua chave Azure com as APIs de revisão, você precisa recuperar seu ID de recursos. Vá para o recurso Moderador de Conteúdo no portal Azure e selecione a lâmina **Propriedades.** Copie o valor do ID de recurso e cole-o no campo **ID(s)** de recursos listados na lista branca da guia **Credenciais** da ferramenta Revisão.

![ID de Recurso do Content Moderator no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

Se você inseriu sua chave de assinatura em ambos os lugares, a chave de teste que vem com sua conta da ferramenta Derevisão não será usada, mas permanecerá disponível.

## <a name="next-steps"></a>Próximas etapas

Siga a [ferramenta Revisar rapidamente para](../quick-start.md) começar a usar a ferramenta Revisar em cenários de moderação de conteúdo.