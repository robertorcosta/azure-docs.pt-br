---
title: Como evitar configurações incorretas com a central de segurança do Azure
description: Saiba como usar as opções ' impor ' e ' negar ' da central de segurança nas páginas de detalhes de recomendações
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906390"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Evitar configurações incorretas com recomendações de aplicação/negação

As configurações incorretas de segurança são uma grande causa de incidentes de segurança. A central de segurança agora tem a capacidade de ajudar a *evitar* configurações incorretas de novos recursos em relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua pontuação segura.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito de **negação** de Azure Policy, você pode interromper a criação de recursos não íntegros
- Usando a opção **impor** , você pode aproveitar o efeito de **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos sem conformidade após a criação

Isso pode ser encontrado na parte superior da página de detalhes do recurso para obter as recomendações de segurança selecionadas (consulte [recomendações com as opções Deny/Force](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedir a criação de recursos

1. Abra a recomendação que os novos recursos devem satisfazer e selecione o botão **negar** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com o botão negar realçado":::

    O painel de configuração é aberto listando as opções de escopo. 

1. Defina o escopo selecionando a assinatura ou o grupo de gerenciamento relevante.

    > [!TIP]
    > Você pode usar os três pontos no final da linha para alterar uma única assinatura ou usar as caixas de seleção para selecionar várias assinaturas ou grupos e, em seguida, selecionar **alterar para negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Definindo o escopo para Azure Policy negar":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação na qual você implantará uma implantação de modelo se novos recursos não atenderem e selecione o botão **impor** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com o botão impor realçado":::

    O painel de configuração é aberto com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Impor opções de configuração":::

1. Defina o escopo, o nome da atribuição e outras opções relevantes.

1. Selecione **Examinar + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções Deny/Force

Essas recomendações podem ser usadas com a opção **Deny** :

- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- Todas as regras de autorização, exceto a RootManageSharedAccessKey, devem ser removidas do namespace do Hub de Eventos
- Todas as regras de autorização, exceto a RootManageSharedAccessKey, devem ser removidas do namespace do Barramento de Serviço
- A transferência segura para contas de armazenamento deve ser habilitada
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- As variáveis da conta de automação devem ser criptografadas
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- Auditar o acesso irrestrito à rede para contas de armazenamento


Essas recomendações podem ser usadas com a opção **impor** :

- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados
- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados
- Os logs de diagnóstico no Hub IoT devem ser habilitados
- Os logs de diagnóstico em contas do Lote devem ser habilitados
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados
- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados
- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados
- Os logs de diagnóstico no Key Vault deve estar habilitados
- A auditoria no SQL Server deve ser habilitada
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL



