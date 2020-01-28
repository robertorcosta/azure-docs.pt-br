---
title: Enviar eventos do Armazenamento de Blobs para o ponto de extremidade da Web – modelo
description: Use a Grade de Eventos do Azure e um modelo do Azure Resource Manager para criar uma conta de Armazenamento de Blobs e assinar os eventos. Envie os eventos para um webhook.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 01/15/2020
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 73f0e6cf5d7ebb3ae36d4048ce5f36f5c0547286
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122705"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Rotear os eventos do Armazenamento de Blobs para o ponto de extremidade da Web usando um modelo do Azure Resource Manager
A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usará um **modelo do Azure Resource Manager** para criar uma conta de Armazenamento de Blobs, assinará os eventos desse Armazenamento de Blobs e disparará um evento para ver o resultado. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este artigo, você enviará os eventos para um aplicativo Web que coleta e exibe as mensagens.

O [modelo do Resource Manager](../azure-resource-manager/templates/overview.md) é um arquivo JSON (JavaScript Object Notation) que define a infraestrutura e a configuração do projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. Caso deseje saber mais sobre como desenvolver modelos do Resource Manager, confira a [documentação do Resource Manager](/azure/azure-resource-manager/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites
### <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem
Antes de assinar eventos para o armazenamento de Blobs, vamos criar o ponto de extremidade para mensagens de evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

    [Implantar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

1. Você verá o site, mas nenhum evento ainda estará publicado.

   ![Exibir novo site](./media/blob-event-quickstart-portal/view-site.png)


## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Criar uma conta de armazenamento com uma assinatura da Grade de Eventos
O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage).

[!code-json[<Azure Resource Manager template create Blob strage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

Há dois recursos do Azure definidos no modelo:

* **Microsoft.Storage/storageAccounts**: crie uma conta de armazenamento do Azure.
* **"Microsoft.Storage/storageAccounts/providers/eventSubscriptions**: crie uma assinatura da Grade de Eventos do Azure para a conta de armazenamento. 

1. Selecione o link a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    [Implantar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)
2. Especifique o **ponto de extremidade**: forneça a URL do aplicativo Web e adicione `api/updates` à URL da Página Inicial.
3. Selecione **Comprar** para implantar o modelo. 

  O portal do Azure é usado para implantar o modelo. Use também o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> Encontre mais amostras de modelos da Grade de Eventos do Azure [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).

## <a name="validate-the-deployment"></a>Validar a implantação
Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

![Exibição do evento de assinatura](./media/blob-event-quickstart-portal/view-subscription-event.png)

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade.

Você pode disparar um evento para o armazenamento de Blobs carregando um arquivo. O arquivo não precisa de um conteúdo específico. Os artigos supõem que você tem um arquivo chamado testfile.txt, mas você pode usar qualquer arquivo.

Quando você carrega o arquivo para o Armazenamento de Blobs do Azure, a Grade de Eventos envia uma mensagem para o ponto de extremidade que você configurou durante a assinatura. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo a seguir, a mensagem JSON contém uma matriz com um evento. Veja seu aplicativo Web e observe que um evento criado por Blob foi recebido. 

![Exibir os resultados](./media/blob-event-quickstart-portal/view-results.png)



## <a name="clean-up-resources"></a>Limpar os recursos
Quando o grupo de recursos não for mais necessário, [exclua-o](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
). 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os modelos do Azure Resource Manager, confira os seguintes artigos:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager)
- [Definir recursos em modelos do Azure Resource Manager](/azure/templates/)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)
- [Modelos da Grade de Eventos do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
