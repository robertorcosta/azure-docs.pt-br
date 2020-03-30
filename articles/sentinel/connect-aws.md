---
title: Conecte o AWS CloudTrail ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar os dados do AWS CloudTrail ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588647"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Conecte o Azure Sentinel ao AWS CloudTrail

Use o conector AWS para transmitir todos os seus eventos AWS CloudTrail no Azure Sentinel. Esse processo de conexão delega o acesso do Azure Sentinel aos seus registros de recursos da AWS, criando uma relação de confiança entre o AWS CloudTrail e o Azure Sentinel. Isso é feito no AWS criando uma função que dá permissão ao Azure Sentinel para acessar seus registros AWS.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.

> [!NOTE]
> O Azure Sentinel coleta eventos do CloudTrail de todas as regiões. Recomenda-se que você não transmita eventos de uma região para outra.

## <a name="connect-aws"></a>Conectar à AWS 


1. No Azure Sentinel, selecione **conectores de dados** e selecione a linha **Amazon Web Services** na tabela e no painel AWS à direita, clique em Abrir página de **conector**.

1. Siga as instruções em **Configuração** usando as seguintes etapas.
 
1.  No console Amazon Web Services, em **Security, Identity & Compliance**, selecione **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Escolha **Funções** e selecione **Criar funções**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Escolha **outra conta AWS.** No campo **ID** da conta, digite o **ID da Conta Microsoft** (**123412341234)** que pode ser encontrado na página de conector AWS no portal Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Certifique-se **de que o ID externo** esteja selecionado e, em seguida, digite o ID externo (ID do espaço de trabalho) que pode ser encontrado na página do conector AWS no portal Do Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Em **'Anexar permissões' selecione** **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Digite uma tag (opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Em seguida, digite um **nome de função** e selecione o botão Criar **função.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na lista De funções, escolha a função criada.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copiar a **função ARN**. No portal Azure Sentinel, na tela do conector Amazon Web Services, cole-o na **Função para adicionar** campo e clique em **Adicionar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para usar o esquema relevante no Log Analytics para eventos AWS, procure **o AWSCloudTrail**.



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o AWS CloudTrail ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

