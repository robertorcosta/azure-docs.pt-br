---
title: Conectar uma conta do Azure Purview 
description: Conecte uma conta do Azure Purview a um workspace do Azure Synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918456"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Início Rápido: Conectar uma conta do Azure Purview a um workspace do Azure Synapse 

> [!IMPORTANT]
> A integração entre o Azure Synapse Analytics e o Azure Purview está atualmente em versão prévia. Se você tiver interesse em experimentar o Azure Purview no Azure Synapse, fale com seu representante de vendas da Microsoft.

Neste guia de início rápido, você registrará uma conta do Azure Purview em um workspace do Azure Synapse. Essa conexão permite que você descubra ativos do Azure Purview e interaja com eles por meio de funcionalidades do Azure Synapse. 

Você pode executar as seguintes tarefas no Azure Synapse: 
- Use a caixa de pesquisa na parte superior para localizar ativos do Purview com base em palavras-chave 
- Entender os dados com base em metadados, linhagem, anotações 
- Conectar esses dados ao seu workspace com serviços vinculados ou conjuntos de dados de integração 
- Analisar esses conjuntos de dados com Synapse Apache Spark, SQL do Synapse e Fluxo de Dados 

## <a name="prerequisites"></a>Pré-requisitos 
- [Conta do Azure Purview](../../purview/create-catalog-portal.md) 
- [Workspace do Azure Synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Entrar um workspace do Azure Synapse 

Acesse https://web.azuresynapse.net e entre no seu workspace. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Permissões para conectar uma conta do Azure Purview 

- Para conectar uma conta do Azure Purview a um workspace do Azure Synapse, você precisa de uma função **Colaborador** no workspace do Azure Synapse do IAM do portal do Azure e precisa ter acesso a essa conta do Azure Purview.

## <a name="connect-an-azure-purview-account"></a>Conectar uma conta do Azure Purview  

- No workspace do Azure Synapse, vá para **Gerenciar** -> **Azure Purview**. Selecione **Conectar-se a uma conta do Purview**. 
- Escolha **Da assinatura do Azure** ou **Inserir manualmente**. Em **Da assinatura do Azure**, é possível selecionar a conta à qual você tem acesso. 
- Depois de se conectar, você deve ser capaz de ver o nome da conta do Purview na guia **Conta do Azure Purview**. 
- É possível usar a barra de pesquisa no centro superior do workspace do Azure Synapse para pesquisar dados. 

## <a name="nextsteps"></a>Próximas etapas 

[Registrar e examinar ativos do Azure Synapse no Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Descobrir, conectar e explorar dados no Synapse usando o Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   