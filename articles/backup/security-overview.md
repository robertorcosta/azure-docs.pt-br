---
title: Visão geral dos recursos de segurança
description: Aprenda sobre os recursos de segurança no Azure Backup que ajudam a proteger seus dados de backup e atender às necessidades de segurança de seus negócios.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585831"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Visão geral dos recursos de segurança no Azure Backup

Um dos passos mais importantes que você pode tomar para proteger seus dados é ter uma infra-estrutura de backup confiável. Mas é tão importante quanto garantir que seus dados sejam copiados de forma segura e que seus backups estejam protegidos o tempo todo. O Azure Backup oferece segurança ao seu ambiente de backup - tanto quando seus dados estão em trânsito quanto em repouso. Este artigo lista recursos de segurança no Azure Backup que ajudam a proteger seus dados de backup e atender às necessidades de segurança de seus negócios.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestão e controle de identidade e acesso ao usuário

O Azure Backup permite que você gerencie acesso com grãos finos usando [o RBAC (Azure Role-Based Access Control, controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). O RBAC permite que você esegregar deveres dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários necessários para fazer seus trabalhos.

O Azure Backup fornece três funções incorporadas para controlar as operações de gerenciamento de backup:

* Backup Contributor - para criar e gerenciar backups, exceto excluindo o cofre dos Serviços de Recuperação e dando acesso a outros
* Operador de backup - tudo o que um colaborador faz, exceto remover políticas de backup e gerenciamento de backup
* Leitor de backup - permissões para visualizar todas as operações de gerenciamento de backup

Saiba mais sobre [o controle de acesso baseado em função para gerenciar o Backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

O Azure Backup tem vários controles de segurança incorporados ao serviço para prevenir, detectar e responder a vulnerabilidades de segurança. Saiba mais sobre [controles de segurança do Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separação entre o armazenamento do hóspede e do Azure

Com o Azure Backup, que inclui backup de máquinavirtual e SQL e SAP HANA em backup de VM, os dados de backup são armazenados no armazenamento do Azure e o hóspede não tem acesso direto ao armazenamento de backup ou ao seu conteúdo.  Com o backup da máquina virtual, a criação e o armazenamento de instantâneos de backup são feitos pela malha Do Zure, onde o hóspede não tem nenhum envolvimento além de solicitar a carga de trabalho para backups consistentes do aplicativo.  Com SQL e SAP HANA, a extensão de backup obtém acesso temporário à gravação para blobs específicos.  Dessa forma, mesmo em um ambiente comprometido, os backups existentes não podem ser adulterados ou excluídos pelo hóspede.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Conectividade com a Internet não é necessária para backup do Azure VM

O backup de VMs do Azure requer a movimentação de dados do disco da sua máquina virtual para o cofre dos Serviços de Recuperação. No entanto, toda a comunicação e transferência de dados necessárias acontece apenas na rede backbone do Azure sem precisar acessar sua rede virtual. Portanto, o backup de VMs do Azure colocados dentro de redes seguras não exige que você permita o acesso a quaisquer IPs ou FQDNs.

## <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup do Azure

Agora você pode usar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de seus dados com segurança de servidores dentro de uma rede virtual para o cofre dos Serviços de Recuperação. O ponto final privado usa um IP do espaço de endereço VNET para o seu cofre, para que você não precise expor suas redes virtuais a quaisquer IPs públicos. Os Private Endpoints podem ser usados para fazer backup e restaurar seus bancos de dados SQL e SAP HANA que são executados dentro de suas VMs Azure. Ele também pode ser usado para seus servidores locais usando o agente MARS.

>[!NOTE]
> Este recurso está atualmente em disponibilidade limitada. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e envie [um e-mail](mailto:azbackupnetsec@microsoft.com) se estiver interessado em usar private endpoints para backup do Azure. A capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Criptografia de dados em trânsito e em repouso

A criptografia protege seus dados e ajuda você a cumprir seus compromissos de segurança organizacional e conformidade. Dentro do Azure, os dados em trânsito entre o armazenamento Azure e o cofre são protegidos pelo HTTPS. Esses dados permanecem na rede de backbone do Azure.

* Os dados de backup são criptografados automaticamente usando chaves gerenciadas pela Microsoft. Você também pode criptografar suas VMs de disco gerenciado de backup no Cofre de Serviços de Recuperação usando [chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) armazenadas no Cofre de Chaves do Azure. Você não precisa tomar nenhuma ação explícita para ativar essa criptografia. Ele se aplica a todas as cargas de trabalho que estão sendo apoiadas no cofre dos Serviços de Recuperação.

* O Azure Backup suporta backup e restauração de VMs do Azure que têm seus discos de oss/dados criptografados com a ADE (ADE). [Saiba mais sobre VMs azure criptografados e backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup contra exclusões não intencionais

O Azure Backup fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Com exclusão suave, se o usuário excluir o backup de uma VM, os dados de backup serão retidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias de dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente. [Saiba mais sobre soft delete](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoramento e alertas de atividades suspeitas

O Azure Backup fornece [recursos integrados de monitoramento e alerta](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) para visualizar e configurar ações para eventos relacionados ao Backup do Azure. [Os Relatórios](https://docs.microsoft.com/azure/backup/configure-reports) de Backup servem como um destino único para rastrear o uso, auditar backups e restaurações e identificar tendências-chave em diferentes níveis de granularidade. O uso das ferramentas de monitoramento e relatórios do Azure Backup pode alertá-lo para qualquer atividade não autorizada, suspeita ou maliciosa assim que ocorrer.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Recursos de segurança para ajudar a proteger backups híbridos

O serviço de backup do Azure usa o agente Microsoft Azure Recovery Services (MARS) para fazer backup e restaurar arquivos, pastas e o volume ou estado do sistema de um computador local para o Azure. A MARS agora fornece recursos de segurança para ajudar a proteger backups híbridos. Esses recursos incluem:

* Uma camada adicional de autenticação será adicionada sempre que uma operação crítica, como a alteração de senha, for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais válidas do Azure. [Saiba mais sobre os recursos que previnem ataques.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)

* Os dados de backup excluídos são retidos por mais 14 dias a partir da data de exclusão. Isso garante a recuperação dos dados dentro de um determinado período de tempo, para que não haja perda de dados mesmo se um ataque acontecer. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos. [Saiba mais sobre como recuperar dados de backup excluídos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Para obter o backup de dados usando o agente MARS (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure), uma senha é usada para garantir que os dados sejam criptografados antes de serem enviados ao Azure Backup e descriptografados somente após o download do Azure Backup. Os detalhes da senha estão disponíveis apenas para o usuário que criou a senha e para o agente que está configurado com ela. Nada é transmitido ou compartilhado com o serviço. Isso garante a segurança completa de seus dados, pois qualquer dado que seja exposto inadvertidamente (como um ataque man-in-the-middle na rede) é inutilizável sem a senha, e a senha não é enviada na rede.

## <a name="compliance-with-standardized-security-requirements"></a>Conformidade com requisitos de segurança padronizados

Para ajudar as organizações a cumprir os requisitos nacionais, regionais e específicos do setor que regem a coleta e o uso de dados individuais, o Microsoft Azure & o Azure Backup oferecem um conjunto abrangente de certificações e atestados. [Veja a lista de certificações de conformidade](compliance-offerings.md)

## <a name="next-steps"></a>Próximas etapas

* [Recursos de segurança para ajudar a proteger as cargas de trabalho na nuvem que usam o Azure Backup](backup-azure-security-feature-cloud.md)
* [Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure](backup-azure-security-feature.md)
