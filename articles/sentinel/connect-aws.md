---
title: Conectar o AWS CloudTrail ao Azure sentinela | Microsoft Docs
description: Use o conector do AWS para delegar acesso ao Azure Sentinel a logs de recursos do AWS, criando uma relação de confiança entre AWS CloudTrail e sentinela.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: e80f7d26fb7ab598651d08b4c1b6478b2ae75e3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563051"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Conectar o Azure Sentinel ao AWS CloudTrail

Use o conector do AWS para transmitir seus eventos de gerenciamento do AWS CloudTrail para o Azure Sentinel. Esse processo de conexão delega o acesso para o Azure Sentinel aos logs de recursos do AWS, criando uma relação de confiança entre o AWS CloudTrail e o Azure sentinela. Isso é feito em AWS criando uma função que concede permissão ao Azure Sentinel para acessar seus logs do AWS.

> [!NOTE]
> AWS CloudTrail tem [limitações internas](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) em sua API LookupEvents. Ele não permite mais do que duas operações por segundo (TPS) por conta, e cada consulta pode retornar um máximo de 50 registros. Consequentemente, se um único locatário gerar constantemente mais de 100 registros por segundo em uma região, os registros posteriores e os atrasos na ingestão de dados ocorrerão.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.

> [!NOTE]
> O Azure Sentinel coleta eventos de gerenciamento de CloudTrail de todas as regiões. É recomendável que você não transmita eventos de uma região para outra.

## <a name="connect-aws"></a>Conectar o AWS 


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione a linha de **Amazon Web Services** na tabela e, no painel AWS à direita, clique em **abrir página de conector**.

1. Siga as instruções em **configuração** usando as etapas a seguir.
 
1.  No console do Amazon Web Services, em **segurança, identidade & conformidade**, selecione **iam**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Escolha **funções** e selecione **criar função**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Escolha **outra conta do AWS.** No campo **ID da conta** , insira a **ID da conta da Microsoft** (**123412341234**) que pode ser encontrada na página conector do AWS no portal do Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Certifique-se de que **exigir ID externa** esteja selecionado e insira a ID externa (ID do espaço de trabalho) que pode ser encontrada na página conector do AWS no portal do Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Em **anexar política de permissões** , selecione **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Insira uma marca (opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Em seguida, insira um **nome de função** e selecione o botão **criar função** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na lista funções, escolha a função que você criou.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copie a **função ARN**. No portal do Azure Sentinel, na tela do conector do Amazon Web Services, Cole-o na **função para adicionar** o campo e clique em **Adicionar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para usar o esquema relevante em Log Analytics para eventos AWS, pesquise **AWSCloudTrail**.



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o AWS CloudTrail ao Azure sentinela. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

