---
title: Recomendações de armazenamento de & de dados - Azure Security Center
description: Este documento aborda as recomendações na Central de Segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure, bem como a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552858"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteger os serviços de armazenamento e dados do Azure
Quando o Azure Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que o guiam durante o processo de configuração dos controles necessários para endurecer e proteger seus recursos.

Este artigo explica a **página de Segurança** de Dados da seção de segurança de recursos do Security Center.

Para obter uma lista completa das recomendações que você pode ver nesta página, consulte [as recomendações de Dados e Armazenamento](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Veja suas informações de segurança de dados

1. Na seção **de higiene de segurança de recursos,** clique em Dados e recursos de **armazenamento**.

    ![Recursos de armazenamento de & de dados](./media/security-center-monitoring/click-data.png)

    A página **de segurança de dados** é aberta com recomendações para recursos de dados.

    [![Recursos de dados](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Nesta página, você pode:

    * Clique na **guia Visão geral** lista todas as recomendações de recursos de dados a serem corrigidas. 
    * Clique em cada guia e visualize as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre criptografia de armazenamento, consulte [a criptografia do Azure Storage para obter dados em repouso](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Remediar uma recomendação sobre um recurso de dados

1. Em qualquer uma das guias de recursos, clique em um recurso. A página de informações abre listando as recomendações a serem corrigidas.

    ![Informações sobre recursos](./media/security-center-monitoring/sql-recommendations.png)

2. Clique em uma recomendação. A página Recomendação abre e exibe as **etapas de Remediação** para implementar a recomendação.

   ![Etapas de remediação](./media/security-center-monitoring/remediate1.png)

3. Clique **em Tomar medidas**. A página configurações de recursos é exibida.

    ![Habilitar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga as **etapas de Remediação** e clique **em Salvar**.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre recomendações aplicáveis a outros tipos de recursos do Azure, consulte os seguintes tópicos:

* [Lista completa de referência das recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Protegendo suas máquinas e aplicativos na Central de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)