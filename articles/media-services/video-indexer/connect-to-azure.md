---
title: Criar uma conta do Video Indexer no portal do Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como criar uma conta do Video Indexer no portal do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: dc844392d64178cb9000db15af39f923521efc19
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838389"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Crie uma conta do Video Indexer conectada ao Azure

Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Video Indexer que está conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure. Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia. 

Este artigo mostra como criar uma conta do Video Indexer vinculada a uma assinatura do Azure e uma conta dos Serviços de Mídia do Azure. O tópico fornece etapas para conectar o Azure usando o fluxo automático (padrão). Também mostra como conectar o Azure manualmente (avançado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

    Se você ainda não tem uma assinatura do Azure, inscreva-se na [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio do Azure Active Directory (AD).

    Se você não tiver um domínio do Azure AD, crie esse domínio com sua assinatura do Azure. Para obter mais informações, consulte [Gerenciando nomes de domínio personalizados no Active Directory do Azure](../../active-directory/users-groups-roles/domains-manage.md)

* Um usuário em seu domínio do Azure AD com uma função de **administrador de aplicativos** . Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Esse usuário deve ser um usuário do Azure AD com uma conta corporativa ou de estudante, não uma conta pessoal como outlook.com, live.com ou hotmail.com.

    ![todos os usuários do AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para fluxo automático

Um usuário e um membro em seu domínio do AD do Azure. Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

Esse usuário deve ser um membro da assinatura do Azure com uma função **Proprietário**, ou ambos **Colaborador** e **Administrador de Acesso do Usuário**. Um usuário pode ser adicionado duas vezes, com 2 funções. Uma vez com o Contributor e uma vez com o User Access Administrator.

![Controle de acesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para fluxo manual

Registre o provedor de recursos do EventGrid usando o portal do Azure.

No [portal do Azure](https://portal.azure.com/), vá para **Assinaturas**->[assinatura]->**ResourceProviders**. 

Pesquise **Microsoft.Media** e **Microsoft.EventGrid**. Se não estiver no estado "Registrado", clique em **registrar**. Demora alguns minutos para se registrar.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Conecte-se ao Azure

> [!NOTE]
> Se sua assinatura do Azure usa a autenticação multifator baseada em certificado, é crucial que você execute as etapas a seguir em um dispositivo que tenha os certificados necessários instalados.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.

2. Clique no botão **criar nova conta** :

    ![Conectar-se para o Azure](./media/create-account/connect-to-azure.png)

3. Quando a lista de assinaturas aparecer, selecione a assinatura que você deseja usar.

    ![conectar-se o indexador de vídeo para o Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure nos locais com suporte: oeste dos EUA 2, norte da Europa ou leste da Ásia.
5. Sob **conta de serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta do Media Services, selecione **Criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta na sua assinatura, incluindo uma nova conta do Armazenamento do Azure. Sua nova conta de serviços de mídia tem uma configuração inicial padrão com um ponto de extremidade de fluxo contínuo e 10 unidades reservadas do S3.
    * Para usar uma conta de serviços de mídia existente, selecione **usar o recurso existente**. Na lista de contas, selecione sua conta.

        Sua conta de Serviços de Mídia deve ter a mesma região da sua conta do Indexador de Vídeo. 

        > [!NOTE]
        > Para minimizar a duração da indexação e o baixo rendimento, é altamente recomendável ajustar o tipo e o número de [Unidades Reservadas](../previous/media-services-scale-media-processing-overview.md ) para **Unidades Reservadas 10 S3** na conta dos Serviços de Mídia. Consulte [Usar o portal para alterar as Unidades Reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente a conexão, clique no link **Alternar para configuração manual**.

        Para obter informações detalhadas, consulte a seção [Conectar o Azure manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) a seguir.
6. Quando terminar, escolha **Connect**. Essa operação poderá demorar alguns minutos. 

    Depois que você estiver conectado ao Azure, sua nova conta do Video Indexer será exibida na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue até sua nova conta

## <a name="connect-to-azure-manually-advanced-option"></a>Conectar o Azure manualmente (opção avançada)

Se a conexão com o Azure falhar, você poderá tentar solucionar o problema, conectando manualmente.

> [!NOTE]
> É altamente recomendável ter as seguintes três contas na mesma região: a conta do Video Indexer que você está conectando com a conta dos Serviços de Mídia, bem como a conta de armazenamento do Azure conectada à mesma conta dos Serviços de Mídia.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de Serviços de Mídia

1. Use o portal do [Azure](https://portal.azure.com/) para criar uma conta de Serviços de Mídia do Azure, conforme descrito em [Criar uma conta](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para sua conta de serviços de mídia, selecione **StorageV2** para os campos tipo de conta e **REDUNDÂNCIA geográfica (GRS)** para replicação.

    ![nova conta de AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Certifique-se de anotar os nomes de contas e recursos de Serviços de Mídia. Essas informações serão necessárias para as etapas da próxima seção.

2. Ajuste o tipo e o número de [Unidades Reservadas](../previous/media-services-scale-media-processing-overview.md ) para **Unidades Reservadas 10 S3** na conta de Serviços de Mídia que você criou. Consulte [Usar o portal para alterar as Unidades Reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de poder reproduzir os vídeos no aplicativo Web do Video Indexer, será necessário iniciar o **Ponto de Extremidade de Streaming** da nova conta de Serviços de Mídia.

    Na nova conta de Serviços de Mídia, clique em **Pontos de extremidade de streaming**. Selecione o ponto de extremidade de streaming e pressione iniciar.

    ![nova conta de AMS](./media/create-account/create-ams-account2.png)

4. Para o Video Indexer autenticar com a API de Serviços de Mídia, é necessário criar um aplicativo do AD. As etapas a seguir irão orientá-lo pelo processo de autenticação do Azure AD descrito em [Introdução à autenticação do Azure AD usando o portal do Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nova conta de Serviços de Mídia, selecione **Acesso à API**.
    2. Selecione [Método de autenticação de entidade de serviço](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Obtenha a ID do cliente e o segredo do cliente, conforme descrito na seção [Obter a ID do cliente e o segredo do cliente](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret).

        Após selecionar **Configurações**->**Chaves**, adicione **Descrição**, pressione **Salvar**, o valor da chave será preenchido.

        Se a chave expirar, o proprietário da conta deverá contatar o suporte do Video Indexer para renovar a chave.

        > [!NOTE]
        > Certifique-se de anotar o valor da chave e a ID do aplicativo. Essas informações serão necessárias para as etapas da próxima seção.

### <a name="connect-manually"></a>Conectar manualmente

Na caixa de diálogo **Conectar o Video Indexer a uma assinatura do Azure** da página do [Video Indexer](https://www.videoindexer.ai/), selecione o link **Alternar para configuração manual**.

Na caixa de diálogo, forneça as informações a seguir:

|Configuração|DESCRIÇÃO|
|---|---|
|Região da conta do Indexador de Vídeo|O nome da região da conta do Video Indexer. Para um melhor desempenho e custos mais baixos, é altamente recomendável especificar o nome da região em que o recurso Serviços de Mídia do Azure e a conta do Armazenamento do Azure estão localizados. |
|Locatário do Azure Active Directory (AAD)|O nome do locatário do Azure AD, por exemplo "contoso.onmicrosoft.com". As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito. Localize o nome à direita de **Domínio**.|
|ID da assinatura|A assinatura do Azure na qual essa conexão deve ser criada. O ID da assinatura pode ser recuperado no portal do Azure. Clique em **Todos os serviços** no painel esquerdo e procure por "inscrições". Selecione **Assinaturas** e escolha a ID desejada na lista das assinaturas.|
|Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual você criou a conta de Serviços de Mídia.|
|Nome do recurso de serviço de mídia|O nome da conta de Serviços de Mídia do Azure que você criou na seção anterior.|
|ID do aplicativo|O ID do aplicativo do Azure AD (com permissões para a conta de Serviços de Mídia especificada) que você criou na seção anterior.|
|Chave do aplicativo|A chave de aplicativo do Azure AD que você criou na seção anterior. |

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos Serviços de Mídia do Azure se aplicam:

* Se conectar automaticamente, verá uma nova conta do Grupo de Recursos, Serviços de Mídia e uma conta de Armazenamento na assinatura do Azure.
* Se conectar automaticamente, o Video Indexer definirá a mídia **Unidades Reservadas** para unidades 10 S3:

    ![Unidades reservadas para os serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se conectar a uma conta existente de Serviços de Mídia, o Video Indexer não alterará a configuração da configuração **Unidades Reservadas**.

   Talvez seja necessário ajustar o tipo e o número de Unidades Reservadas de Mídia, de acordo com a carga planejada. Lembre-se de que se a carga for alta e você não tiver unidades ou velocidade suficientes, o processamento dos vídeos poderá resultar em falhas de tempo limite.

* Se você conectar uma nova conta de Serviços de Mídia, o Indexador de Vídeo iniciará automaticamente o **Ponto de extremidade de streaming** padrão:

    ![Ponto de extremidade de streaming dos Serviços de Mídia](./media/create-account/ams-streaming-endpoint.png)

    Os pontos de extremidade de streaming têm um tempo de inicialização considerável. Portanto, pode demorar vários minutos desde o momento em que você conectou a conta ao Azure, até que os vídeos possam ser transmitidos e assistidos no aplicativo Web do Video Indexer.

* Se você conectar uma conta existente de Serviços de Mídias, o Video Indexer não alterará a configuração padrão do Ponto de Extremidade de Streaming. Se não houver **Streaming Endpoint** em execução, você não poderá assistir a vídeos dessa conta de Serviços de Mídia ou ao Video Indexer.

## <a name="next-steps"></a>Próximas etapas

Você pode interagir programaticamente com sua conta de avaliação e / ou com suas contas do Video Indexer conectadas ao azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.


