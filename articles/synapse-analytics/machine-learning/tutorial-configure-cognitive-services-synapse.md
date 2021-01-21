---
title: 'Tutorial: Pré-requisitos para Serviços Cognitivos no Azure Synapse'
description: Tutorial para como configurar os pré-requisitos para usar os Serviços Cognitivos no Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222166"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Tutorial: Pré-requisitos para usar Serviços Cognitivos no Azure Synapse

Neste tutorial, você aprenderá como configurar os pré-requisitos para aproveitar com segurança os Serviços Cognitivos no Azure Synapse.

Este tutorial abrange:
> [!div class="checklist"]
> - Criar um recursos dos Serviços Cognitivos. Por exemplo Análise de Texto ou Detector de Anomalias.
> - Armazene a chave de autenticação para os recursos dos Serviços Cognitivos como segredos no Azure Key Vault e configure o acesso para o workspace do Azure Synapse.
> - Crie um serviço vinculado do Azure Key Vault em seu workspace do Azure Synapse Analytics.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Criar um Recurso dos Serviços Cognitivos

Os [Serviços Cognitivos do Azure](../../cognitive-services/index.yml) incluem muitos tipos diferentes de serviços. Veja abaixo alguns exemplos que são usados nos tutoriais do Azure Synapse.

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias
Criar um [Detector de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal do Azure.

![Criar detector de anomalias](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Criar um recurso de Análise de Texto
Crie um recurso de [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal do Azure.

![Criar análise de texto](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Criar Key Vault e configurar segredos e acesso

1. Crie um [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) no portal do Azure.
2. Acesse **Key Vault -> Políticas de acesso** e conceda as permissões [MSI do workspace do Azure Synapse](../security/synapse-workspace-managed-identity.md) para ler os segredos do Azure Key Vault.

>Verifique se as alterações da política foram salvas. É fácil deixar essa etapa passar.

![Adicionar política de acesso](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Acesse seu recurso do Serviço Cognitivo, por exemplo, **Detector de Anomalias -> Chaves e Ponto de Extremidade** e copie uma das duas chaves para a área de transferência.

4. Acesse **Key Vault -> Segredo** para criar um segredo. Especifique o nome do segredo e cole a chave da etapa anterior no campo "Valor". Por fim, clique em **Criar**.

![Criar um segredo](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Você precisará se lembrar do nome do segredo ou anotá-lo. Você o usará posteriormente quando se conectar aos Serviços Cognitivas do Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Criar o Serviço Vinculado do Azure Key Vault no Azure Synapse

1. Abra o workspace no Azure Synapse Studio. Navegue até **Gerenciar -> Serviços Vinculados**. Crie um serviço vinculado do "Azure Key Vault" apontando para o Key Vault que acabamos de criar. Em seguida, verifique se a conexão clicando no botão "Testar conexão" e verificando se ele está verde. Se algo funcionar bem, clique em "Criar" primeiro e, depois, em "Publicar tudo" para salvar sua alteração.
![Serviço vinculado](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Agora você está pronto para continuar com um dos tutoriais para usar a experiência dos Serviços Cognitivos do Azure no Azure Synapse Studio.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Análise de sentimento com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Detecção de anomalias com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em Pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)