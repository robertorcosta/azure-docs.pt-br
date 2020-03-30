---
title: Suporte ao mapa de origem para aplicativos JavaScript - Azure Monitor Application Insights
description: Saiba como carregar mapas de origem para sua própria conta de armazenamento Contêiner Blob usando Insights de aplicativo.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474876"
---
# <a name="source-map-support-for-javascript-applications"></a>Suporte ao mapa de origem para aplicativos JavaScript

O Application Insights suporta o upload de mapas de origem para o seu próprio contêiner blob da conta de armazenamento.
Os mapas de origem podem ser usados para desminificar pilhas de chamadas encontradas na página de detalhes finais da transação. Qualquer exceção enviada pelo [JavaScript SDK][ApplicationInsights-JS] ou pelo [Node.js SDK][ApplicationInsights-Node.js] pode ser desminibitada com mapas de origem.

![Desminirinar uma pilha de chamadas vinculando-se a uma conta de armazenamento](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Crie uma nova conta de armazenamento e um contêiner Blob

Se você já tem uma conta de armazenamento existente ou um recipiente blob, você pode pular essa etapa.

1. [Criar uma nova conta de armazenamento][create storage account]
2. [Crie um recipiente blob][create blob container] dentro de sua conta de armazenamento. Certifique-se de definir o "nível de acesso público" para `Private`garantir que seus mapas de origem não sejam acessíveis publicamente.

> [!div class="mx-imgBorder"]
>![O nível de acesso ao contêiner deve ser definido como Privado](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Empurre seus mapas de origem para o recipiente Blob

Você deve integrar seu pipeline de implantação contínua com sua conta de armazenamento, configurando-o para carregar automaticamente seus mapas de origem para o contêiner Blob configurado. Você não deve carregar seus mapas de origem para uma subpasta no contêiner Blob; atualmente, o mapa de origem só será obtido a partir da pasta raiz.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Enviar mapas de origem via Azure Pipelines (recomendado)

Se você estiver usando o Azure Pipelines para construir e implantar continuamente seu aplicativo, adicione uma tarefa [azure File Copy][azure file copy] ao seu pipeline para carregar automaticamente seus mapas de origem.

> [!div class="mx-imgBorder"]
> ![Adicione uma tarefa de cópia de arquivos do Azure ao seu Pipeline para carregar seus mapas de origem no Armazenamento Azure Blob](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configure seu recurso Application Insights com uma conta de armazenamento do Source Map

### <a name="from-the-end-to-end-transaction-details-page"></a>A partir da página de detalhes de transação de ponta a ponta

Na guia de detalhes de transação de ponta a ponta, você pode clicar em *'Desminividar'* e exibirá um prompt para configurar se seu recurso estiver desconfigurado.

1. No Portal, veja os detalhes de uma exceção que é minificada.
2. Clique em *Unminify*
3. Se o recurso não tiver sido configurado, uma mensagem será exibida, solicitando que você configure.

### <a name="from-the-properties-page"></a>A partir da página propriedades

Se você quiser configurar ou alterar a conta de armazenamento ou o contêiner Blob vinculado ao recurso Do aplicativo Insights, você pode fazê-lo visualizando a guia *Propriedades* do recurso Application Insights.

1. Navegue até a guia *Propriedades* do recurso Application Insights.
2. Clique em *Alterar recipiente de bolha do mapa de origem*.
3. Selecione um recipiente Blob diferente como seu contêiner de mapas de origem.
4. Clique em `Apply`.

> [!div class="mx-imgBorder"]
> ![Reconfigure o contêiner Azure Blob selecionado navegando até a lâmina de propriedades](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Configurações necessárias de controle de acesso baseado em função (RBAC) no contêiner Blob

Qualquer usuário no Portal que use esse recurso deve ser atribuído pelo menos como um leitor de [dados blob de armazenamento][storage blob data reader] ao seu contêiner Blob. Você deve atribuir essa função a qualquer outra pessoa que esteja usando os mapas de origem através deste recurso.

> [!NOTE]
> Dependendo de como o contêiner foi criado, isso pode não ter sido automaticamente atribuído a você ou à sua equipe.

### <a name="source-map-not-found"></a>Mapa de origem não encontrado

1. Verifique se o mapa de origem correspondente está carregado no recipiente de bolha correto
2. Verifique se o arquivo do mapa de origem tem o nome `.map`do arquivo JavaScript para o quais ele mapeia, sufixado com .
    - Por exemplo, `/static/js/main.4e2ca5fa.chunk.js` vai procurar a bolha chamada`main.4e2ca5fa.chunk.js.map`
3. Verifique o console do seu navegador para ver se algum erro está sendo registrado. Inclua isso em qualquer bilhete de suporte.

## <a name="next-steps"></a>Próximas etapas

* [Tarefa de cópia de arquivos do Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme