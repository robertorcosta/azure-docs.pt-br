---
title: Configure um serviço QnA Maker - QnA Maker
description: Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 8ec57f441ba58227e45398c35c7931dc75fa658f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131723"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar os recursos do QnA Maker

Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.

Uma compreensão sólida dos seguintes conceitos é útil antes de criar seu recurso:

* [Recursos do QnA Maker](../Concepts/azure-resources.md)
* [Chaves de autoria e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço do QnA Maker

Esse procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de conhecimento. Depois de concluir essas etapas, você encontrará as chaves de _assinatura_ na página **Chaves** para o recurso no portal Azure.

1. Faça login no portal Azure e crie um recurso [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecione **Criar** depois de ler os termos e condições:

    ![Criar um novo serviço do QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker,** selecione os níveis e regiões apropriados:

    ![Criar um serviço do QnA Maker – tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No **campo Nome,** digite um nome único para identificar este serviço QnA Maker. Esse nome também identifica o ponto final do QnA Maker com o qual suas bases de conhecimento serão associadas.
    * Escolha a **Assinatura** sob a qual o recurso QnA Maker será implantado.
    * Selecione o **nível de preços** para os serviços de gerenciamento QnA Maker (APIs portal e gerenciamento). Veja [mais detalhes sobre os preços do SKU](https://aka.ms/qnamaker-pricing).
    * Crie um novo **grupo de recursos** (recomendado) ou use um já existente para implantar esse recurso do QnA Maker. O QnA Maker cria vários recursos do Azure. Quando você cria um grupo de recursos para reter esses recursos, você pode facilmente encontrar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Selecione um **local de grupo de recursos**.
    * Escolha a camada de preços de **pesquisa** do serviço de pesquisa cognitiva Do Azure. Se a opção de nível Livre não estiver disponível (aparece escurecida), significa que você já tem um serviço gratuito implantado através de sua assinatura. Nesse caso, você precisará começar com o nível Básico. Veja [os detalhes dos preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde deseja que os índices de pesquisa cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local escolhido para a Pesquisa Cognitiva do Azure.
    * No campo Nome do **aplicativo,** digite um nome para a instância do Serviço de Aplicativos do Azure.
    * Por padrão, o App Service é padrão para o nível padrão (S1). Você pode alterar o plano após a criação. Saiba mais sobre [os preços do App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **local do site** onde o Serviço de Aplicativo será implantado.

        > [!NOTE]
        > O **local de pesquisa** pode diferir da localização do **site.**

    * Escolha se deseja ou não ativar o **Application Insights**. Se o **Application Insights** estiver habilitado, o QnA Maker coletará a telemetria em tráfego, logs de chat e erros.
    * Escolha o **local de insights** do aplicativo onde o recurso Application Insights será implantado.
    * Como medida de economia de custo, você pode [compartilhar](#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos do Azure criados para o QnA Maker.

1. Depois que todos os campos forem validados, selecione **Criar**. O processo pode levar alguns minutos para ser concluído.

1. Depois que a implantação for concluída, você verá os seguintes recursos criados em sua assinatura:

   ![O recurso criou um serviço do QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo _Serviços Cognitivos_ tem suas chaves _de assinatura._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Encontre chaves de assinatura no portal Azure

Você pode visualizar e redefinir suas chaves de assinatura a partir do portal Azure, onde você criou o recurso QnA Maker.

1. Vá para o recurso QnA Maker no portal Azure e selecione o recurso que tem o tipo _serviços cognitivos:_

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá para **Keys**:

    ![Chave de assinatura](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Encontre chaves de ponto final no portal QnA Maker

O ponto final está na mesma região do recurso porque as teclas de ponto final são usadas para fazer uma chamada para a base de conhecimento.

As chaves de ponto de extremidade podem ser gerenciadas a partir do [portal do QnA Maker](https://qnamaker.ai).

1. Faça login no [portal QnA Maker,](https://qnamaker.ai)vá para o seu perfil e, em seguida, selecione **configurações de serviço:**

    ![Chave do ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves:

    > [!div class="mx-imgBorder"]
    > ![Gerente-chave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize suas chaves se acha que foram comprometidas. Isso pode exigir que sejam feitas as alterações correspondentes no seu aplicativo cliente ou código bot.

### <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Quando você quiser ter mais perguntas e respostas em sua base de conhecimento, além do seu nível atual, atualize seu nível de preços de serviço do QnA Maker.

Para fazer upgrade da SKU de gerenciamento do QnA Maker:

1. Vá para o recurso QnA Maker no portal do Azure e selecione **Camada de preços**.

    ![Recurso do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e pressione **Selecionar**.

    ![Preços do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

 Quando sua base de conhecimento precisar atender a mais solicitações do seu aplicativo cliente, atualize seu nível de preços do App Service.

Você pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou dimensionar o Serviço de Aplicativos.

Vá para o recurso App Service no portal Azure e selecione a opção **Escalar** ou **Dimensionar** conforme necessário.

![Escala de serviço de aplicativo do fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualize o serviço de pesquisa cognitiva do Azure

Se você planeja ter muitas bases de conhecimento, atualize seu nível de preços do serviço de pesquisa cognitiva do Azure.

Atualmente, você não pode executar uma atualização no local do SKU de pesquisa do Azure. No entanto, pode criar um novo recurso de pesquisa do Azure com o SKU desejado, restaurar os dados para o novo recurso e vinculá-lo com a pilha do QnA Maker. Para fazer isso, siga estas etapas:

1. Crie um novo recurso de pesquisa do Azure no portal Azure e selecione o SKU desejado.

    ![Recurso de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices de seu recurso original da pesquisa do Azure para o novo. Consulte o código de [amostra de restauração de backup](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois que os dados forem restaurados, vá para o novo recurso de pesquisa do Azure, selecione **Chaves**e anote a **tecla Nome** e a **tecla Administrador:**

    ![Chaves de pesquisa do QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular o novo recurso de pesquisa do Azure à pilha QnA Maker, vá para a instância QnA Maker App Service.

    ![Exemplo de serviço de aplicativo do fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **as configurações do aplicativo** e modifique as configurações nos campos **AzureSearchName** e **AzureSearchAdminKey** a partir da etapa 3.

    ![Configuração do serviço de aplicativo do fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do Serviço de Aplicativo.

    ![Reinicialização da instância do Serviço de Aplicativo do Criador de QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Receba as últimas atualizações de tempo de execução

O tempo de execução do QnAMaker faz parte da instância do Azure App Service que é implantada quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal Azure. Atualizações são feitas periodicamente para o runtime. A instância qnA Maker App Service está no modo de atualização automática após a liberação de extensão do site de abril de 2019 (versão 5+). Esta atualização foi projetada para cuidar do tempo de inatividade ZERO durante as atualizações.

Você pode verificar sua https://www.qnamaker.ai/UserSettingsversão atual em . Se sua versão for mais antiga que a versão 5.x, você deve reiniciar o App Service para aplicar as últimas atualizações:

1. Vá para o seu serviço QnAMaker (grupo de recursos) no [portal Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância serviço do aplicativo e abra a seção **Visão geral.**

    > [!div class="mx-imgBorder"]
    > ![Exemplo de serviço de aplicativo QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reiniciar o serviço de aplicativos. O processo de atualização deve terminar em alguns segundos. Quaisquer aplicativos ou bots dependentes que usam este serviço QnAMaker não estarão disponíveis para os usuários finais durante este período de reinicialização.

    ![Reinicialização da instância qnAMaker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configure o QnA Maker para usar diferentes recursos de pesquisa cognitiva

Se você criar um serviço de QnA e suas dependências (como pesquisa) através do portal, um serviço de pesquisa será criado para você e vinculado ao serviço QnA Maker. Depois que esses recursos forem criados, você pode atualizar a configuração do Serviço de Aplicativo para usar um serviço de pesquisa anteriormente existente e remover o que você acabou de criar.

O recurso QnA Maker's **App Service** usa o recurso de Pesquisa Cognitiva. Para alterar o recurso de Pesquisa Cognitiva usado pelo QnA Maker, você precisa alterar a configuração no portal Azure.

1. Obtenha a **chave de admin** e o **nome** do recurso de pesquisa cognitiva que você deseja que o QnA Maker use.

1. Faça login no [portal Do Zure](https://portal.azure.com) e encontre o **Serviço de Aplicativo** associado ao seu recurso QnA Maker. Ambos têm o mesmo nome.

1. Selecione **Configurações**e, em seguida, **Configuração**. Isso exibirá todas as configurações existentes para o Serviço de Aplicativo do Fabricante de QnA.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do portal Azure mostrando configurações de configuração do App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Alterar os valores para as seguintes teclas:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para usar as novas configurações, você precisa reiniciar o serviço do Aplicativo. Selecione **Visão geral**e selecione **Reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do portal Azure reiniciando o Serviço de Aplicativos após a alteração das configurações](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se você criar um serviço de QnA através de modelos do Azure Resource Manager, poderá criar todos os recursos e controlar a criação do App Service para usar um serviço de pesquisa existente.

Saiba mais sobre como configurar as [configurações](../../../app-service/configure-common.md#configure-app-settings)do Aplicativo de Serviço do Aplicativo .

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configure a configuração ociosa do serviço do aplicativo para evitar o tempo de intervalo

O serviço de aplicativo, que serve o tempo de execução da previsão do QnA Maker para uma base de conhecimento publicada, tem uma configuração de tempo de tempo parado, que é padrão para o tempo de tempo automático se o serviço estiver ocioso. Para o QnA Maker, isso significa que o tempo de execução da previsão gera a API do Answer ocasionalmente após períodos sem tráfego.

Para manter o aplicativo de ponto final de previsão carregado mesmo quando não houver tráfego, defina o ocioso para sempre ligado.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Procure e selecione o serviço de aplicativo do seu recurso QnA Maker. Ele terá o mesmo nome do recurso QnA Maker, mas terá um **tipo** diferente de Serviço de Aplicativo.
1. Encontrar **Configurações** e selecionar **Configuração**.
1. No painel Configuração, selecione **Configurações Gerais,** em seguida, encontre **Always on**e selecione **On** como o valor.

    > [!div class="mx-imgBorder"]
    > ![No painel Configuração, selecione **Configurações gerais**, em seguida, encontre **Always on**, e selecione **On** como o valor.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selecione **Salvar** para salvar a configuração.
1. Você é perguntado se deseja reiniciar o aplicativo para usar a nova configuração. Selecione **Continuar**.

Saiba mais sobre como configurar as configurações gerais de serviço [saqueado](../../../app-service/configure-common.md#configure-general-settings).

## <a name="delete-azure-resources"></a>Excluir recursos do Azure

Se você excluir qualquer um dos recursos do Azure usados em suas bases de conhecimento do QnA Maker, as bases de conhecimento não funcionarão mais. Antes de excluir qualquer recurso, exporte suas bases de conhecimento da página **Configurações**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [serviço de App](../../../app-service/index.yml) e Serviço de [Busca](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Criar e publicar uma base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md)