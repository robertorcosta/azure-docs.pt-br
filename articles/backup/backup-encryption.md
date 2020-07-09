---
title: Criptografia no backup do Azure
description: Saiba mais sobre como os recursos de criptografia no backup do Azure ajudam a proteger seus dados de backup e atender às necessidades de segurança de sua empresa.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: ca570cfdc6e78e712715ba075168f4b06c55e4af
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116549"
---
# <a name="encryption-in-azure-backup"></a>Criptografia no backup do Azure

Todos os dados submetidos a backup são criptografados automaticamente quando armazenados na nuvem usando a criptografia de armazenamento do Azure, o que ajuda você a atender aos seus compromissos de segurança e conformidade. Esses dados em repouso são criptografados usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2.

Além da criptografia em repouso, todos os dados de backup em trânsito são transferidos por HTTPS. Ele sempre permanece na rede de backbone do Azure.

Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte as [perguntas frequentes sobre o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a perguntas que você possa ter sobre criptografia.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pela plataforma

Por padrão, todos os seus dados são criptografados usando chaves gerenciadas por plataforma. Você não precisa tomar nenhuma ação explícita do seu fim para habilitar essa criptografia e ela se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre dos serviços de recuperação.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pelo cliente

Ao fazer backup de suas máquinas virtuais do Azure, agora você pode criptografar seus dados usando chaves de propriedade e gerenciadas por você. O backup do Azure permite que você use suas chaves RSA armazenadas no Azure Key Vault para criptografar seus backups. A chave de criptografia usada para criptografar backups pode ser diferente da usada para a origem. Os dados são protegidos usando uma DEK (chave de criptografia de dados) baseada em AES 256, que é, por sua vez, protegida usando suas chaves. Isso lhe dá controle total sobre os dados e as chaves. Para permitir a criptografia, é necessário que o cofre dos serviços de recuperação receba acesso à chave de criptografia no Azure Key Vault. Você pode desabilitar a chave ou revogar o acesso sempre que necessário. No entanto, você deve habilitar a criptografia usando as chaves antes de tentar proteger todos os itens para o cofre.

>[!NOTE]
>Este recurso está atualmente em disponibilidade limitada. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e envie AskAzureBackupTeam@microsoft.com um email para se quiser criptografar seus dados de backup usando chaves gerenciadas pelo cliente. Observe que a capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup de VMs de disco gerenciado criptografadas usando chaves gerenciadas pelo cliente

O backup do Azure também permite fazer backup de suas VMs do Azure que usam sua chave para a [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). A chave usada para criptografar os discos é armazenada no Azure Key Vault e gerenciada por você. Criptografia do Serviço de Armazenamento (SSE) usando chaves gerenciadas pelo cliente difere de Azure Disk Encryption, já que o ADE aproveita o BitLocker (para Windows) e o DM-cript (para Linux) para executar a criptografia no convidado, a SSE criptografa os dados no serviço de armazenamento, permitindo que você use qualquer sistema operacional ou imagens para suas VMs. Consulte [criptografia de discos gerenciados com chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) para obter mais detalhes.

## <a name="infrastructure-level-encryption-for-backup-data"></a>Criptografia no nível de infraestrutura para dados de backup

Além de criptografar seus dados no cofre dos serviços de recuperação usando chaves gerenciadas pelo cliente, você também pode optar por ter uma camada adicional de criptografia configurada na infraestrutura de armazenamento. Essa criptografia de infraestrutura é gerenciada pela plataforma e, juntamente com a criptografia em repouso usando chaves gerenciadas pelo cliente, ela permite a criptografia de duas camadas de seus dados de backup. Deve-se observar que a criptografia de infraestrutura pode ser configurada apenas se você optar por usar suas próprias chaves para criptografia em repouso. A criptografia de infraestrutura usa chaves gerenciadas por plataforma para criptografar dados.

>[!NOTE]
>A criptografia de infraestrutura está atualmente em visualização limitada e está disponível apenas nas regiões leste dos EUA, US West2 e centro-sul dos EUA. Se você quiser usar o recurso em qualquer uma dessas regiões, preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e envie-nos um email para [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Backup de VMs criptografadas usando ADE

Com o backup do Azure, você também pode fazer backup de suas máquinas virtuais do Azure que têm seu sistema operacional ou discos de dados criptografados usando Azure Disk Encryption. O ADE usa o BitLocker para VMs do Windows e o DM-cript para VMs do Linux para executar a criptografia no convidado. Para obter detalhes, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Próximas etapas

- [Fazer backup e restaurar uma VM do Azure criptografada](backup-azure-vms-encryption.md)
