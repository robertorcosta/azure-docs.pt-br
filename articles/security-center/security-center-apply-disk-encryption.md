---
title: Aplicar a criptografia de disco na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da central de segurança do Azure para **aplicar a criptografia de disco**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: aa4e8f165320327788255605470bda789047c347
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592490"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar a criptografia de disco na central de segurança do Azure
A central de segurança do Azure recomenda que você aplique a criptografia de disco se tiver discos de VM do Windows ou Linux que não são criptografados usando Azure Disk Encryption. A criptografia de disco permite criptografar seus discos de VM IaaS Windows e Linux.  A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.

A criptografia de disco usa o recurso padrão do setor [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux. Esses recursos fornecem sistema operacional e criptografia de dados para ajudar a proteger e proteger seus dados e atender aos compromissos de conformidade e segurança da organização. A criptografia de disco é integrada com [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do Key Vault, garantindo que todos os dados nos discos de VM sejam criptografados em repouso no [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Há suporte para o Azure Disk Encryption nos seguintes sistemas operacionais Windows Server-Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. A criptografia de disco tem suporte nos seguintes sistemas operacionais de servidor Linux-Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **recomendações** , selecione **aplicar criptografia de disco**.
2. Na folha **aplicar criptografia de disco** , você verá uma lista de VMs para as quais a criptografia de disco é recomendada.
3. Siga as instruções para aplicar a criptografia a essas VMs.

![][1]

Para criptografar as máquinas virtuais do Azure que foram identificadas pela central de segurança como necessidade de criptografia, recomendamos as seguintes etapas:

* Instalar e configurar Azure PowerShell. Isso permite que você execute os comandos do PowerShell necessários para configurar os pré-requisitos necessários para criptografar as máquinas virtuais do Azure.
* Obter e executar o script Pré-requisitos de Azure Disk Encryption do Azure PowerShell.
* Criptografar suas máquinas virtuais.

[Criptografar uma VM IaaS do Windows com o Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) orienta você durante essas etapas. Este tópico pressupõe que você esteja usando um computador cliente Windows do qual você configura a criptografia de disco.

Há muitas abordagens que podem ser usadas para máquinas virtuais do Azure. Se você já está bem inverso em Azure PowerShell ou CLI do Azure, talvez prefira usar abordagens alternativas. Para saber mais sobre essas outras abordagens, consulte [Azure Disk Encryption](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Consulte também
Este documento mostrou como implementar a recomendação da central de segurança "aplicar criptografia de disco". Para saber mais sobre a criptografia de disco, consulte o seguinte:

* [Criptografia e gerenciamento de chaves com o Cofre da Chave do Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 minutos e 39 segundos) – saiba como usar o gerenciamento de criptografia de disco para VMs IaaS e o Cofre da Chave do Azure para ajudar a proteger seus dados.
* [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) (documento) – saiba como habilitar a criptografia de disco para VMs do Windows e do Linux.

Para saber mais sobre a central de segurança, consulte o seguinte:

* [Configuração de políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) – saiba como definir as políticas de segurança.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
