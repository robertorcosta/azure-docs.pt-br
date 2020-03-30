---
title: Conecte a proteção de informações do Azure ao Azure Sentinel
description: Saiba como conectar dados do Azure Information Protection no Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588545"
---
# <a name="connect-data-from-azure-information-protection"></a>Conecte dados da Proteção de Informações do Azure

> [!IMPORTANT]
> O conector de dados azure Information Protection no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir informações de registro do [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) para o Azure Sentinel configurando o conector de dados azure Information Protection. O Azure Information Protection ajuda você a controlar e proteger seus dados confidenciais, sejam eles armazenados na nuvem ou no local.

Se [os relatórios centrais para proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip) já estão configurados para que as informações de registro deste serviço sejam armazenadas no mesmo espaço de trabalho do Log Analytics que você selecionou atualmente para o Azure Sentinel, você pode pular a configuração deste conector de dados. As informações de registro do Azure Information Protection já estão disponíveis para o Azure Sentinel.

No entanto, se as informações de registro do Azure Information Protection forem para um espaço de trabalho diferente do Log Analytics diferente do que você selecionou atualmente para o Azure Sentinel, faça um dos seguintes:

- Alterar o espaço de trabalho selecionado no Azure Sentinel.

- Altere o espaço de trabalho para o Azure Information Protection, o que você pode fazer configurando este conector de dados.
    
    Se você alterar o espaço de trabalho, novos dados de relatórios para o Azure Information Protection serão agora armazenados no espaço de trabalho que você está usando para o Azure Sentinel, e os dados históricos não estão disponíveis para o Azure Sentinel. Além disso, se o espaço de trabalho anterior estiver configurado para consultas, alertas ou APIs DE DESCANSO, estes devem ser reconfigurados para o espaço de trabalho do Azure Sentinel se você quiser continuar usando-os para proteção de informações do Azure. Nenhuma reconfiguração é necessária para clientes e serviços que usam o Azure Information Protection.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes funções de administrador do Azure AD para o seu inquilino: 
    - Administrador de Proteção de Informações do Azure
    - Administrador de segurança
    - Administrador de conformidade
    - Administrador de dados de conformidade
    - Administrador global
    
    > [!NOTE]
    > Você não pode usar a função de administrador de proteção de informações do Azure se o inquilino estiver na [plataforma de rotulagem unificada](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Essas funções de administrador são necessárias apenas para configurar o conector de proteção de informações do Azure e não são necessárias quando o Azure Sentinel está conectado ao Azure Information Protection.

- Permissões para ler e escrever no espaço de trabalho log analytics que você está usando para o Azure Sentinel e a Azure Information Protection.

- O Azure Information Protection foi adicionado ao portal Azure. Se você precisar de ajuda com esta etapa, consulte [Adicionar proteção de informações do Azure ao portal Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Conecte-se à proteção de informações do Azure

Use as seguintes instruções se você não configurou um espaço de trabalho do Log Analytics para proteção de informações do Azure ou se você precisar alterar o espaço de trabalho que armazena as informações de registro de proteção de informações do Azure.

1. No Azure Sentinel, selecione **conectores de dados**e, em seguida, **Azure Information Protection (Preview)**.

2. Selecione **Abrir a página do conector**.

3. Na lâmina **Configurar análise (Visualização),** selecione o espaço de trabalho que você está usando atualmente para o Azure Sentinel. Se você selecionar um espaço de trabalho diferente, os dados de relatórios do Azure Information Protection não estarão disponíveis para o Azure Sentinel.

4. Quando você tiver selecionado um espaço de trabalho, selecione **OK** e o status do **conector** deve agora mudar para **Conectado**.

5. Os dados de relatórios do Azure Information Protection são armazenados na tabela **InformationProtectionLogs_CL** dentro do espaço de trabalho selecionado. 
    
    Para usar o esquema relevante no Azure Monitor para esses dados de relatórios, procure **informaçõesProtectionEvents**. Para obter informações sobre essas funções de evento, consulte a [seção de referência de esquema amigável para funções](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) de eventos da documentação de Proteção de Informações do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar o Azure Information Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
