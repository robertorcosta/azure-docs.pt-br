---
title: Gerenciar uma conta do Video Indexer
titleSuffix: Azure Media Services
description: Saiba como gerenciar uma conta do Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499658"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerenciar uma conta do Video Indexer conectada ao Azure

Este artigo demonstra como gerenciar uma conta do Video Indexer que está conectada à sua assinatura do Azure e a uma conta do Azure Media Services.

> [!NOTE]
> Você precisa ser o proprietário da conta do Video Indexer para fazer os ajustes na configuração da conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Conecte sua conta do Video Indexer ao Azure, conforme descrito em [Conectado ao Azure](connect-to-azure.md).

Certifique-se de seguir os [Pré-requisitos](connect-to-azure.md#prerequisites) e veja as [Considerações](connect-to-azure.md#considerations) neste artigo.

## <a name="examine-account-settings"></a>Examinar as configurações de conta

Esta seção examina as configurações da sua conta do Video Indexer.

Para exibir as configurações:

1. Clique no ícone do usuário no canto superior direito e selecione **Configurações**.

    ![Configurações no indexador de vídeo](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **Configurações**, selecione a guia **Conta**.

Se sua conta do Indexador de vídeos estiver conectada ao Azure, você verá as seguintes coisas:

* O nome da conta de Serviços de Mídia do Azure subjacente.
* O número de trabalhos de indexação em execução e na fila.
* O número e o tipo de unidades reservadas alocadas.

Se sua conta precisar de alguns ajustes, você verá erros e avisos relevantes sobre a configuração da sua conta na página **Configurações.** As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Para obter mais informações, consulte a seção de [erros e avisos](#errors-and-warnings) a seguir.

## <a name="repair-the-connection-to-azure"></a>Reparaa a conexão com o Azure

Na conexão Atualização com a caixa de diálogo **Azure Media Services** da página do [Indexador](https://www.videoindexer.ai/) de vídeo, é solicitado que você forneça valores para as seguintes configurações:

|Configuração|Descrição|
|---|---|
|ID de assinatura do Azure|O ID da assinatura pode ser recuperado no portal do Azure. Clique em **Todos os serviços** no painel esquerdo e procure por "assinaturas". Selecione **Assinaturas** e escolha a ID desejada na lista das assinaturas.|
|Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual você criou a conta de Serviços de Mídia.|
|ID do aplicativo|O ID do aplicativo Azure AD (com permissões para a conta especificada do Media Services) que você criou para esta conta do Video Indexer. <br/><br/>Para obter o ID do aplicativo, navegue até o portal Azure. Na conta Serviços de mídia, escolha sua conta e vá para **a API Access**. Selecione **Conectar-se à API de serviços de mídia com o** -> **aplicativo Azure AD**principal do serviço. Copie os parâmetros relevantes.|
|Chave do aplicativo|A chave de aplicativo Azure AD associada à sua conta de Serviços de Mídia que você especificou acima. <br/><br/>Para obter a chave do aplicativo, navegue até o portal Azure. Na conta Serviços de mídia, escolha sua conta e vá para **a API Access**. Selecione **Conectar-se à API de serviços de mídia com o principal serviço** -> Gerenciar**os & segredos**do**aplicativo** -> . Copie os parâmetros relevantes.|

## <a name="autoscale-reserved-units"></a>Unidades reservadas em escala automática

A página Configurações permite definir o autoscaling de unidades **reservadas** de mídia (RU). Se a opção estiver **Ativa**, você poderá alocar o número máximo de RUs e garantir que o Video Indexer iniciará/parará RUs automaticamente. Com esta opção, você não paga dinheiro extra por tempo indomparado, mas também não espera que os trabalhos de indexação completem um longo período quando a carga de indexação é alta.

A escala automática não é dimensionada abaixo de 1 RU ou acima do limite padrão da conta dos Serviços de Mídia. Para aumentar o limite, crie uma solicitação de serviço. Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](../../media-services/previous/media-services-quotas-and-limitations.md).

![Indexador de vídeo de unidades reservadas em escala automática](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se sua conta precisar de alguns ajustes, você verá avisos e erros relevantes sobre a configuração da sua conta na página **Configurações**. As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Esta seção fornece mais detalhes sobre o erro e mensagens de aviso.

* EventGrid

    Você precisa registrar o provedor de recursos do EventGrid usando o portal do Azure. No [portal Azure,](https://portal.azure.com/)vá para **assinaturas** > [assinatura] > **ResourceProviders** > **Microsoft.EventGrid**. Se não estiver no estado **registrado,** selecione **Registrar**. Demora alguns minutos para se registrar.

* ponto de extremidade de streaming

    Verifique se a conta de Serviços de Mídia subjacente tem o padrão **Ponto de Extremidade de Streaming** em um estado iniciado. Caso contrário, você não pode assistir vídeos desta conta do Media Services ou no Video Indexer.

* Unidades reservadas pela mídia

    Você deve alocar unidades reservadas de mídia em seu recurso de serviço de mídia para indexar vídeos. Para obter o desempenho ideal da indexação, é recomendável alocar pelo menos 10 Unidades Reservadas S3. Para obter informações de preço, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Próximas etapas

Você pode interagir programáticamente com sua conta de teste ou contas do Video Indexer que estão conectadas ao Azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Use o mesmo usuário Azure AD que você usou ao se conectar ao Azure.
