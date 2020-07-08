---
title: Crie uma conta do Video Indexer conectada ao Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 405533aad8247350d45cc53009abe6b58a511264
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83005940"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Crie uma conta do Video Indexer conectada ao Azure

Quando cria uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (em que você não está limitado pela cota). O Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API com uma conta de avaliação gratuita. Com a opção pago, você cria uma conta de Video Indexer que está conectada à sua assinatura do Azure e uma conta dos serviços de mídia do Azure. Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia.

Este artigo mostra como criar uma conta do Video Indexer vinculada a uma assinatura do Azure e uma conta dos Serviços de Mídia do Azure. O tópico fornece etapas para conectar o Azure usando o fluxo automático (padrão). Também mostra como conectar o Azure manualmente (avançado).

Se você estiver mudando de uma *versão de avaliação* para conta de video indexer *paga* , poderá optar por copiar todos os vídeos e a personalização do modelo para a nova conta, conforme discutido na seção [importar o conteúdo da conta de avaliação](#import-your-content-from-the-trial-account) .

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

    Se você ainda não tem uma assinatura do Azure, inscreva-se na [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio do Azure Active Directory (AD do Azure).

    Se você não tiver um domínio do Azure AD, crie esse domínio com sua assinatura do Azure. Para obter mais informações, consulte [Gerenciando nomes de domínio personalizados no Azure ad](../../active-directory/users-groups-roles/domains-manage.md)

* Um usuário em seu domínio do Azure AD com uma função de **administrador de aplicativos** . Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Esse usuário deve ser um usuário do Azure AD com uma conta corporativa ou de estudante. Não use uma conta pessoal, como outlook.com, live.com ou hotmail.com.

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
> Se sua assinatura do Azure usa a autenticação multifator baseada em certificado, é crucial que você execute as etapas a seguir em um dispositivo que tenha os certificados necessários instalados.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.

2. Selecione o botão **criar nova conta** :

    ![Criar nova conta de Video Indexer](./media/create-account/connect-to-azure.png)

3. Quando a lista de assinaturas aparecer, selecione a assinatura que você deseja usar.

    ![Conectar Video Indexer ao Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure nos locais com suporte: oeste dos EUA 2, norte da Europa ou leste da Ásia.
5. Sob **conta de serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta do Media Services, selecione **Criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta na sua assinatura, incluindo uma nova conta do Armazenamento do Azure. Sua nova conta de serviços de mídia tem uma configuração inicial padrão com um ponto de extremidade de streaming e 10 unidades reservadas S3.
    * Para usar uma conta de serviços de mídia existente, selecione **usar o recurso existente**. Na lista de contas, selecione sua conta.

        Sua conta de Serviços de Mídia deve ter a mesma região da sua conta do Indexador de Vídeo.

        > [!NOTE]
        > Para minimizar a duração da indexação e a baixa taxa de transferência, é altamente recomendável ajustar o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) em sua conta dos serviços de mídia para **10 unidades reservadas S3**. Consulte [usar o portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente a conexão, selecione o link **alternar para configuração manual** .

        Para obter informações detalhadas, consulte a seção [Conectar o Azure manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) a seguir.
6. Quando terminar, escolha **Connect**. Essa operação poderá demorar alguns minutos.

    Depois que você estiver conectado ao Azure, sua nova conta do Video Indexer será exibida na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue até sua nova conta.

## <a name="connect-to-azure-manually-advanced-option"></a>Conectar o Azure manualmente (opção avançada)

Se a conexão com o Azure falhar, você poderá tentar solucionar o problema, conectando manualmente.

> [!NOTE]
> É altamente recomendável ter as três contas a seguir na mesma região: a conta de Video Indexer que você está conectando com a conta dos serviços de mídia, bem como a conta de armazenamento do Azure conectada à mesma conta de serviços de mídia.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de Serviços de Mídia

1. Use o portal do [Azure](https://portal.azure.com/) para criar uma conta de Serviços de Mídia do Azure, conforme descrito em [Criar uma conta](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para sua conta de serviços de mídia, selecione **StorageV2** para os campos tipo de conta e **REDUNDÂNCIA geográfica (GRS)** para replicação.

    ![Nova conta do AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Certifique-se de anotar os nomes de contas e recursos de Serviços de Mídia. Você precisará delas para as etapas na próxima seção.

2. Ajuste o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) para **10 unidades reservadas S3** na conta dos serviços de mídia que você criou. Consulte [usar o portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de reproduzir seus vídeos no aplicativo Web Video Indexer, você deve iniciar o **ponto de extremidade de streaming** padrão da nova conta dos serviços de mídia.

    Na nova conta dos serviços de mídia, selecione **pontos de extremidade de streaming**. Em seguida, selecione o ponto de extremidade de streaming e pressione Iniciar.

    ![Nova conta do AMS](./media/create-account/create-ams-account2.png)

4. Para Video Indexer autenticar com a API dos serviços de mídia, um aplicativo do AD precisa ser criado. As etapas a seguir irão orientá-lo pelo processo de autenticação do Azure AD descrito em [Introdução à autenticação do Azure AD usando o portal do Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nova conta de Serviços de Mídia, selecione **Acesso à API**.
    2. Selecione [Método de autenticação de entidade de serviço](../previous/media-services-portal-get-started-with-aad.md).
    3. Obtenha o ID do cliente e o segredo do cliente

        Depois de selecionar **as configurações** -> **chaves**, adicionar **Descrição**, pressione **salvar**e o valor da chave será populado.

        Se a chave expirar, o proprietário da conta precisará entrar em contato com Video Indexer suporte para renovar a chave.

        > [!NOTE]
        > Certifique-se de anotar o valor da chave e a ID do aplicativo. Você precisará dela para as etapas na próxima seção.

### <a name="connect-manually"></a>Conectar manualmente

Na caixa de diálogo **Conectar o Video Indexer a uma assinatura do Azure** da página do [Video Indexer](https://www.videoindexer.ai/), selecione o link **Alternar para configuração manual**.

Na caixa de diálogo, forneça as informações a seguir:

|Setting|Descrição|
|---|---|
|Região da conta do Indexador de Vídeo|O nome da região da conta do Video Indexer. Para obter um melhor desempenho e reduzir os custos, é altamente recomendável especificar o nome da região em que o recurso dos serviços de mídia do Azure e a conta de armazenamento do Azure estão localizados. |
|Locatário do Azure AD|O nome do locatário do Azure AD, por exemplo "contoso.onmicrosoft.com". As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito. Localize o nome à direita de **Domínio**.|
|ID da assinatura|A assinatura do Azure na qual essa conexão deve ser criada. O ID da assinatura pode ser recuperado no portal do Azure. Selecione **todos os serviços** no painel esquerdo e procure "assinaturas". Selecione **Assinaturas** e escolha a ID desejada na lista das assinaturas.|
|Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual você criou a conta de Serviços de Mídia.|
|Nome do recurso de serviço de mídia|O nome da conta de Serviços de Mídia do Azure que você criou na seção anterior.|
|ID do aplicativo|O ID do aplicativo do Azure AD (com permissões para a conta de Serviços de Mídia especificada) que você criou na seção anterior.|
|Chave do aplicativo|A chave de aplicativo do Azure AD que você criou na seção anterior. |

## <a name="import-your-content-from-the-trial-account"></a>Importar seu conteúdo da conta de *avaliação*

Ao [criar uma nova conta](#connect-to-azure), você tem a opção de importar o conteúdo da conta de *avaliação* para a nova conta. Se você marcar a opção de *importação* na caixa de diálogo **criar uma nova conta em uma assinatura do Azure** , todas as personalizações do modelo de conteúdo e mídia serão copiadas da conta de *avaliação* para a nova conta.

A capacidade de importar o conteúdo é válida para abordagens automatizadas e manuais descritas acima.

> [!NOTE]
> O conteúdo só pode ser importado uma vez de cada conta.

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos Serviços de Mídia do Azure se aplicam:

* Se conectar automaticamente, verá uma nova conta do Grupo de Recursos, Serviços de Mídia e uma conta de Armazenamento na assinatura do Azure.
* Se conectar automaticamente, o Video Indexer definirá a mídia **Unidades Reservadas** para unidades 10 S3:

    ![Unidades reservadas para os serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se você se conectar a uma conta dos serviços de mídia existente, Video Indexer não alterará a configuração das **unidades reservadas** de mídia existentes.

   Talvez seja necessário ajustar o tipo e o número de unidades reservadas de mídia de acordo com a carga planejada. Lembre-se de que se a carga for alta e você não tiver unidades ou velocidade suficientes, o processamento dos vídeos poderá resultar em falhas de tempo limite.

* Se você conectar uma nova conta de Serviços de Mídia, o Indexador de Vídeo iniciará automaticamente o **Ponto de extremidade de streaming** padrão:

    ![Ponto de extremidade de streaming dos Serviços de Mídia](./media/create-account/ams-streaming-endpoint.png)

    Os pontos de extremidade de streaming têm um tempo de inicialização considerável. Portanto, pode levar vários minutos a partir do momento em que você conectou sua conta ao Azure até que seus vídeos possam ser transmitidos e observados no aplicativo Web Video Indexer.

* Se você se conectar a uma conta dos serviços de mídia existente, Video Indexer não alterará a configuração do ponto de extremidade de streaming padrão. Se não houver nenhum **ponto de extremidade de streaming**em execução, você não poderá assistir a vídeos desta conta de serviços de mídia ou em video indexer.

## <a name="next-steps"></a>Próximas etapas

Você pode interagir de forma programática com sua conta de avaliação e/ou com suas contas de Video Indexer que estão conectadas ao Azure seguindo as instruções em: [usar APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.
