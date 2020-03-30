---
title: Crie uma conta do Video Indexer conectada ao Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta do Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499923"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Crie uma conta do Video Indexer conectada ao Azure

Ao criar uma conta do Indexador de Vídeo, você pode escolher uma conta de avaliação gratuita (onde você recebe um certo número de minutos de indexação grátis) ou uma opção paga (onde você não está limitado pela cota). Com uma avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários de sites e até 2400 minutos de indexação gratuita para usuários de API. Com a opção paga, você cria uma conta do Video Indexer que está conectada à sua assinatura do Azure e a uma conta do Azure Media Services. Você paga por minutos indexados, bem como as taxas relacionadas à conta de mídia.

Este artigo mostra como criar uma conta do Video Indexer vinculada a uma assinatura do Azure e uma conta dos Serviços de Mídia do Azure. O tópico fornece etapas para conectar o Azure usando o fluxo automático (padrão). Também mostra como conectar o Azure manualmente (avançado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

    Se você ainda não tem uma assinatura do Azure, inscreva-se na [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio do Azure Active Directory (Azure AD).

    Se você não tiver um domínio do Azure AD, crie esse domínio com sua assinatura do Azure. Para obter mais informações, consulte [Gerenciar nomes de domínio personalizados em seu Azure AD](../../active-directory/users-groups-roles/domains-manage.md)

* Um usuário em seu domínio Azure AD com uma função **de administrador de aplicativos.** Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Este usuário deve ser um usuário Azure AD com uma conta de trabalho ou escola. Não use uma conta pessoal, como outlook.com, live.com ou hotmail.com.

    ![todos os usuários do AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para fluxo automático

* Um usuário e um membro em seu domínio do AD do Azure.

    Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Esse usuário deve ser um membro da assinatura do Azure com uma função **Proprietário**, ou ambos **Colaborador** e **Administrador de Acesso do Usuário**. Um usuário pode ser adicionado duas vezes, com duas funções. Uma vez com o Contributor e uma vez com o User Access Administrator.

    ![Controle de acesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para fluxo manual

* Registre o provedor de recursos do EventGrid usando o portal do Azure.

    No [portal do Azure](https://portal.azure.com/), vá para **Assinaturas**->[assinatura]->**ResourceProviders**.

    Pesquise **Microsoft.Media** e **Microsoft.EventGrid**. Se não estiver no estado "Registrado", clique em **registrar**. Demora alguns minutos para se registrar.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Conectar-se ao Azure

> [!NOTE]
> Se sua assinatura do Azure usa autenticação multifatorial baseada em certificados, é crucial que você execute as seguintes etapas em um dispositivo que tenha os certificados necessários instalados.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.

2. Selecione o botão **Criar nova conta:**

    ![Criar nova conta do Indexador de Vídeo](./media/create-account/connect-to-azure.png)

3. Quando a lista de assinaturas aparecer, selecione a assinatura que você deseja usar.

    ![Conecte o indexador de vídeo ao Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure nos locais com suporte: oeste dos EUA 2, norte da Europa ou leste da Ásia.
5. Sob **conta de serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta do Media Services, selecione **Criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta na sua assinatura, incluindo uma nova conta do Armazenamento do Azure. Sua nova conta de Serviços de Mídia tem uma configuração inicial padrão com um ponto final de streaming e 10 unidades reservadas s3.
    * Para usar uma conta de serviços de mídia existente, selecione **usar o recurso existente**. Na lista de contas, selecione sua conta.

        Sua conta de Serviços de Mídia deve ter a mesma região da sua conta do Indexador de Vídeo.

        > [!NOTE]
        > Para minimizar a duração da indexação e o baixo consumo, é altamente recomendável ajustar o tipo e o número de [Unidades Reservadas](../previous/media-services-scale-media-processing-overview.md ) em sua conta de Serviços de Mídia para **10 Unidades Reservadas S3**. Consulte [Usar portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente sua conexão, selecione o **link De configuração manual do Switch para o Manual.**

        Para obter informações detalhadas, consulte a seção [Conectar o Azure manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) a seguir.
6. Quando terminar, escolha **Connect**. Essa operação poderá demorar alguns minutos.

    Depois que você estiver conectado ao Azure, sua nova conta do Video Indexer será exibida na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue até sua nova conta.

## <a name="connect-to-azure-manually-advanced-option"></a>Conectar o Azure manualmente (opção avançada)

Se a conexão com o Azure falhar, você poderá tentar solucionar o problema, conectando manualmente.

> [!NOTE]
> É altamente recomendável ter as seguintes três contas na mesma região: a conta do Video Indexer que você está conectando com a conta de Serviços de Mídia, bem como a conta de armazenamento Do Zure conectada à mesma conta do Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de Serviços de Mídia

1. Use o portal do [Azure](https://portal.azure.com/) para criar uma conta de Serviços de Mídia do Azure, conforme descrito em [Criar uma conta](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para sua conta de Serviços de Mídia, selecione **StorageV2** para tipo de conta e **GRS (Geo-redundante)** para os campos de replicação.

    ![Nova conta AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Certifique-se de anotar os nomes de contas e recursos de Serviços de Mídia. Você vai precisar deles para os passos na próxima seção.

2. Ajuste o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) para **10 Unidades Reservadas S3** na conta de Serviços de Mídia que você criou. Consulte [Usar portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de reproduzir seus vídeos no aplicativo web Do Indexador de Vídeo, você deve iniciar o **ponto final** de streaming padrão da nova conta do Media Services.

    Na nova conta de Serviços de Mídia, selecione **Pontos finais de streaming**. Em seguida, selecione o ponto final de streaming e pressione iniciar.

    ![Nova conta AMS](./media/create-account/create-ams-account2.png)

4. Para que o Video Indexer seja autenticado com a API do Media Services, um aplicativo AD precisa ser criado. As etapas a seguir irão orientá-lo pelo processo de autenticação do Azure AD descrito em [Introdução à autenticação do Azure AD usando o portal do Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nova conta de Serviços de Mídia, selecione **Acesso à API**.
    2. Selecione [Método de autenticação de entidade de serviço](../previous/media-services-portal-get-started-with-aad.md).
    3. Obtenha o ID do cliente e o segredo do cliente

        Depois de selecionar **Teclas de configuração,**->**Keys**adicione **Descrição,** **pressione Salvar**e o valor-chave será preenchido.

        Se a chave expirar, o proprietário da conta terá que entrar em contato com o suporte do Indexador de Vídeo para renovar a chave.

        > [!NOTE]
        > Certifique-se de anotar o valor da chave e a ID do aplicativo. Você vai precisar dele para os passos na próxima seção.

### <a name="connect-manually"></a>Conectar manualmente

Na caixa de diálogo **Conectar o Video Indexer a uma assinatura do Azure** da página do [Video Indexer](https://www.videoindexer.ai/), selecione o link **Alternar para configuração manual**.

Na caixa de diálogo, forneça as informações a seguir:

|Configuração|Descrição|
|---|---|
|Região da conta do Indexador de Vídeo|O nome da região da conta do Video Indexer. Para um melhor desempenho e custos mais baixos, é altamente recomendável especificar o nome da região onde estão localizados os recursos do Azure Media Services e da conta Azure Storage. |
|Locatário do Azure AD|O nome do locatário do Azure AD, por exemplo "contoso.onmicrosoft.com". As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito. Localize o nome à direita de **Domínio**.|
|ID da assinatura|A assinatura do Azure na qual essa conexão deve ser criada. O ID da assinatura pode ser recuperado no portal do Azure. Selecione **Todos os serviços** no painel esquerdo e procure por "assinaturas". Selecione **Assinaturas** e escolha a ID desejada na lista das assinaturas.|
|Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual você criou a conta de Serviços de Mídia.|
|Nome do recurso de serviço de mídia|O nome da conta de Serviços de Mídia do Azure que você criou na seção anterior.|
|ID do aplicativo|O ID do aplicativo do Azure AD (com permissões para a conta de Serviços de Mídia especificada) que você criou na seção anterior.|
|Chave do aplicativo|A chave de aplicativo do Azure AD que você criou na seção anterior. |

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos Serviços de Mídia do Azure se aplicam:

* Se conectar automaticamente, verá uma nova conta do Grupo de Recursos, Serviços de Mídia e uma conta de Armazenamento na assinatura do Azure.
* Se conectar automaticamente, o Video Indexer definirá a mídia **Unidades Reservadas** para unidades 10 S3:

    ![Unidades reservadas para os serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se você se conectar a uma conta de Serviços de Mídia existentes, o Video Indexer não mudará a configuração de **Unidades Reservadas** de mídia existentes.

   Você pode precisar ajustar o tipo e o número de Unidades Reservadas de Mídia de acordo com a carga planejada. Lembre-se de que se a carga for alta e você não tiver unidades ou velocidade suficientes, o processamento dos vídeos poderá resultar em falhas de tempo limite.

* Se você conectar uma nova conta de Serviços de Mídia, o Indexador de Vídeo iniciará automaticamente o **Ponto de extremidade de streaming** padrão:

    ![Ponto de extremidade de streaming dos Serviços de Mídia](./media/create-account/ams-streaming-endpoint.png)

    Os pontos finais de streaming têm um tempo considerável de inicialização. Portanto, pode levar vários minutos a partir do momento em que você conectou sua conta ao Azure até que seus vídeos possam ser transmitidos e assistidos no aplicativo web Video Indexer.

* Se você se conectar a uma conta de Serviços de Mídia existentes, o Video Indexer não mudará a configuração padrão do ponto final de streaming. Se não houver streaming endpoint em **execução,** você não poderá assistir a vídeos desta conta do Serviço de Mídia ou do Video Indexer.

## <a name="next-steps"></a>Próximas etapas

Você pode interagir programáticamente com sua conta de teste e/ou com suas contas de indexador de vídeo que estão conectadas ao Azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.