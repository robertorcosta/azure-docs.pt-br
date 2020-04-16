---
title: Provisão e garanta um serviço vinculado no Azure Synapse Analytics
description: Saiba como prover e garantir um serviço vinculado com o Gerenciado Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430546"
---
# <a name="securing-a-linked-service-with-private-links"></a>Garantir um serviço vinculado com links privados 

Neste artigo, você aprenderá como garantir um serviço vinculado na Sinapse com um ponto final privado.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Você usa o Azure Data Lake Gen 2 como um armazenamento de dados *de origem.* Se você não tiver uma conta de armazenamento, consulte [Criar uma conta do Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md) para obter etapas para criar uma. Certifique-se de que a Conta de Armazenamento tenha a filtragem IP do Synapse Studio para acessá-la e que você só permite que **redes selecionadas acessem** a conta armazenamento. A configuração sob os **Firewalls blade e redes virtuais** deve se parecer com a imagem abaixo.

![Conta de armazenamento protegida](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Crie um serviço vinculado com links privados

No Azure Synapse Analytics, um serviço vinculado é onde você define suas informações de conexão para outros serviços. Nesta seção, você adicionará a Azure Synapse Analytics e a Azure Data Lake Gen 2 como serviços vinculados.

1. Abra o Azure Synapse Studio e vá para a guia **Gerenciar.**
1. Em **conexões externas,** selecione **serviços vinculados**.
1. Para adicionar um serviço vinculado, clique em **Novo**.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e clique em **Continuar**.
1. Certifique-se de ativar **a criação interativa**. Pode levar cerca de 1 minuto para ser habilitado. 
1. Digite suas credenciais de autenticação. A chave da conta, o principal de serviço e a identidade gerenciada são atualmente tipos de autenticação suportados. Clique em conexão de teste para verificar se suas credenciais estão corretas.
1. Selecione **A conexão Teste**, ela deve falhar porque a Conta de armazenamento não habilita o acesso a ela sem a criação e aprovação de um Ponto Final Privado. Na mensagem de erro, você deve ver um link para criar um **ponto final privado** que você pode seguir para ir para a próxima parte. Se você seguir esse link, pule a próxima parte.
1. Selecione **Criar** quando terminar.

## <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerenciado

No caso, você não clicou no hiperlink ao testar a conexão acima, siga o seguinte caminho. Agora você precisa criar um ponto final privado gerenciado que você se conectará ao serviço vinculado criado acima.

1. Vá para a guia **Gerenciar.**
1. Vá para a seção **Redes Virtuais Gerenciadas.**
1. Selecione **+ Novo** em Managed private endpoint.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e selecione **Continuar**.
1. Digite o nome da conta de armazenamento que você criou acima.
1. Selecione **Criar**
1. Você deve ver depois de esperar alguns segundos que o link privado criado precisa de uma aprovação.

## <a name="approval-of-a-private-link"></a>Aprovação de um link privado
1. Selecione o ponto final privado que você criou acima. Você pode ver um hiperlink que permitirá que você aprove o Ponto Final Privado no nível da Conta de Armazenamento. *Uma alternativa é ir diretamente para a conta de armazenamento do portal Azure e ir para a lâmina **de conexões de ponto final privado.***
1. Marque o ponto final privado que você criou no Estúdio e selecione **Aprovar**.
1. Adicione uma descrição e clique **em sim**
1. Volte ao Synapse Studio na seção **Redes Virtuais Gerenciadas** da Guia **Gerenciar.**
1. Deve levar cerca de 1 minuto para obter a aprovação refletida para o seu ponto final privado.

## <a name="check-the-connection-works"></a>Verifique as obras de conexão
1. Vá para a guia **Gerenciar** e selecione o serviço vinculado que você criou.
1. Certifique-se de que **a autoria interativa** está ativa.
1. Selecione **Testar conexão**. Você deve ver a conexão ser bem sucedida.

Você agora estabeleceu uma conexão segura e privada entre o Sinapse e seu serviço vinculado!

## <a name="next-steps"></a>Próximas etapas

Para desenvolver uma compreensão mais aprofundada do ponto final privado gerenciado no Synapse Analytics, consulte o Conceito em torno do artigo [de ponto final privado gerenciado pela Synapse.](data-integration-data-lake.md)

Para obter mais informações sobre a integração de dados para o Synapse Analytics, consulte os [dados de ingestão em um](data-integration-data-lake.md) artigo do Data Lake.