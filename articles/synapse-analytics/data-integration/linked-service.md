---
title: Proteger um serviço vinculado
description: Saiba como provisionar e proteger um serviço vinculado com vnet gerenciada
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c7cf8ec7297b3c0ef855936becc3c06a5120a38
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496070"
---
# <a name="securing-a-linked-service-with-private-links"></a>Protegendo um serviço vinculado com links privados 

Neste artigo, você aprenderá a proteger um serviço vinculado no Synapse com um ponto de extremidade privado.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o Azure Data Lake Gen 2 como uma *fonte* de armazenamento de dados. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento do Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md) para obter as etapas para criar uma. Verifique se a conta de armazenamento tem a filtragem de IP do Synapse Studio para acessá-la e se você só permite que as **redes selecionadas** acessem a conta de armazenamento. A configuração na folha **firewalls e redes virtuais** deve ser parecida com a imagem abaixo.

![Conta de armazenamento protegida](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Criar um serviço vinculado com links privados

No Azure Synapse Analytics, um serviço vinculado é onde você define as informações de conexão com outros serviços. Nesta seção, você adicionará o Azure Synapse Analytics e o Azure Data Lake Gen 2 como serviços vinculados.

1. Abra o Azure Synapse Studio e vá para a guia **gerenciar** .
1. Em **Conexões externas**, selecione **Serviços vinculados**.
1. Para adicionar um serviço vinculado, clique em **Novo**.
1. Selecione a peça do Azure Data Lake Storage Gen2 na lista e clique em **Continuar**.
1. Certifique-se de habilitar a **criação interativa**. Pode levar cerca de 1 minuto para ser habilitado. 
1. Insira as credenciais de autenticação. A chave de conta, a entidade de serviço e a identidade gerenciada são tipos de autenticação atualmente suportados. Clique em testar conexão para verificar se suas credenciais estão corretas.
1. Selecione **testar conexão**, ele deve falhar porque a conta de armazenamento não permite o acesso a ela sem a criação e a aprovação de um ponto de extremidade privado. Na mensagem de erro, você deve ver um link para criar um **ponto de extremidade privado** que você pode seguir para ir para a próxima parte. Se você seguir esse link, ignore a próxima parte.
1. Selecione **Criar** quando terminar.

## <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Caso você não tenha clicado no hiperlink ao testar a conexão acima, siga o caminho a seguir. Agora você precisa criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado criado acima.

1. Vá para a guia **gerenciar** .
1. Vá para a seção **redes virtuais gerenciadas** .
1. Selecione **+ novo** em ponto de extremidade privado gerenciado.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e selecione **continuar**.
1. Insira o nome da conta de armazenamento que você criou acima.
1. Escolha **Criar**
1. Você deve ver depois de aguardar alguns segundos que o link privado criado precisa de uma aprovação.

## <a name="approval-of-a-private-link"></a>Aprovação de um link privado
1. Selecione o ponto de extremidade privado que você criou acima. Você pode ver um hiperlink que permitirá aprovar o ponto de extremidade privado no nível da conta de armazenamento. *Uma alternativa é ir diretamente para a conta de armazenamento portal do Azure e ir para a folha **conexões de ponto de extremidade privado** .*
1. Marque o ponto de extremidade privado que você criou no estúdio e selecione **aprovar**.
1. Adicione uma descrição e clique em **Sim**
1. Volte para o Synapse Studio na seção **redes virtuais gerenciadas** da guia **gerenciar** .
1. Deve levar cerca de 1 minuto para que a aprovação seja refletida para seu ponto de extremidade particular.

## <a name="check-the-connection-works"></a>Verificar a conexão funciona
1. Vá para a guia **gerenciar** e selecione o serviço vinculado que você criou.
1. Verifique se a **criação interativa** está ativa.
1. Selecione **Testar conexão**. Você verá que a conexão foi bem-sucedida.

Agora você estabeleceu uma conexão segura e privada entre o Synapse e o serviço vinculado!

## <a name="next-steps"></a>Próximas etapas

Para desenvolver mais conhecimento do ponto de extremidade privado gerenciado no Synapse Analytics, consulte o artigo [conceito sobre o ponto de extremidade particular gerenciado Synapse](data-integration-data-lake.md) .

Para obter mais informações sobre a integração de dados para o Synapse Analytics, consulte o artigo [ingerir dados em um data Lake](data-integration-data-lake.md) .
