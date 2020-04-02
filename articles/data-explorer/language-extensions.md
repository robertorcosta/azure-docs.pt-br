---
title: Gerencie extensões de idioma no cluster Azure Data Explorer.
description: Use a extensão de idioma para integrar outros idiomas nas consultas KQL do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522466"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Gerenciar extensões de idioma no cluster Azure Data Explorer (Preview)

O recurso de extensões de idioma permite que você use plugins de extensão de idioma para integrar outros idiomas às suas consultas KQL do Azure Data Explorer. Quando você executa uma função definida pelo usuário (UDF) usando um script relevante, o script obtém dados tabulares como sua entrada e espera-se que produza saída tabular. O tempo de execução do plugin está hospedado em uma [caixa de areia,](/azure/kusto/concepts/sandboxes)um ambiente isolado e seguro, funcionando nos nós do cluster. Neste artigo, você gerencia as extensões de idioma plugin no seu cluster Azure Data Explorer dentro do portal Azure.

> [!NOTE]
> As extensões de linguagem do Azure Data Explorer que são suportadas atualmente são Python e R.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Crie [um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Habilite extensões de idioma em seu cluster

> [!WARNING]
> Por favor, revise as [limitações](#limitations) antes de habilitar uma extensão de idioma.

Faça as seguintes etapas para habilitar extensões de idioma em seu cluster:

1. No portal do Azure, acesse o cluster do Azure Data Explorer. 
1. Em **Configurações,** selecione **Configurações**. 
1. No painel **Configurações,** selecione **Ativado** para ativar uma extensão de idioma.
1. Clique em **Salvar**.
 
    ![extensão de linguagem em](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Habilitar o processo de extensão do idioma pode levar alguns minutos. Durante esse tempo, seu cluster será suspenso.
 
## <a name="run-language-extension-integrated-queries"></a>Executar consultas integradas à extensão de idioma

* Aprenda a [executar consultas KQL integradas python](/azure/kusto/query/pythonplugin).
* Aprenda a executar consultas [R integradas KQL](/azure/kusto/query/rplugin). 

## <a name="disable-language-extensions-on-your-cluster"></a>Desativar extensões de idioma em seu cluster

> [!NOTE]
> Desativar extensões de linguagem pode levar alguns minutos.

Faça as seguintes etapas para desativar as extensões de idioma em seu cluster:

1. No portal do Azure, acesse o cluster do Azure Data Explorer. 
1. Em **Configurações,** selecione **Configurações**. 
1. No painel **Configurações,** selecione **Desativar** para desativar uma extensão de idioma.
1. Clique em **Salvar**.

    ![Extensão de linguagem fora](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Limitações

* O recurso de extensões de idioma não suporta [criptografia de disco](manage-cluster-security.md). 
* As extensões de idioma do runtime sandbox alocam espaço em disco mesmo que nenhuma consulta seja executada no escopo do idioma relevante.

