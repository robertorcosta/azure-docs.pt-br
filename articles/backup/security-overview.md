---
title: Visão geral dos recursos de segurança
description: Saiba mais sobre os recursos de segurança no backup do Azure que ajudam a proteger seus dados de backup e atender às necessidades de segurança de sua empresa.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585831"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Visão geral dos recursos de segurança no backup do Azure

Uma das etapas mais importantes que você pode tomar para proteger seus dados é ter uma infraestrutura de backup confiável. Mas é tão importante garantir que seus dados sejam submetidos a backup de maneira segura e que seus backups sejam protegidos o tempo todo. O backup do Azure fornece segurança para seu ambiente de backup-ambos quando seus dados estiverem em trânsito e em repouso. Este artigo lista os recursos de segurança no backup do Azure que ajudam a proteger seus dados de backup e atender às necessidades de segurança de sua empresa.

## <a name="management-and-control-of-identity-and-user-access"></a>Gerenciamento e controle de identidade e acesso de usuário

O backup do Azure permite que você gerencie o acesso refinado usando o [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). O RBAC permite separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso para os usuários necessários para realizar seus trabalhos.

O backup do Azure fornece três funções internas para controlar as operações de gerenciamento de backup:

* Colaborador de backup – para criar e gerenciar backups, exceto excluir o cofre dos serviços de recuperação e conceder acesso a outros
* Operador de backup-tudo o que um colaborador faz, exceto remover backup e gerenciar políticas de backup
* Leitor de backup-permissões para exibir todas as operações de gerenciamento de backup

Saiba mais sobre o [controle de acesso baseado em função para gerenciar o backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

O backup do Azure tem vários controles de segurança incorporados ao serviço para impedir, detectar e responder a vulnerabilidades de segurança. Saiba mais sobre os [controles de segurança para o backup do Azure](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separação entre o convidado e o armazenamento do Azure

Com o backup do Azure, que inclui backup de máquina virtual e SQL e SAP HANA no backup de VM, os dados de backup são armazenados no armazenamento do Azure e o convidado não tem acesso direto ao armazenamento de backup ou ao seu conteúdo.  Com o backup de máquina virtual, a criação e o armazenamento de instantâneos de backup são feitos pela malha do Azure, em que o convidado não tem envolvimento além de desativar a carga de trabalho para backups consistentes do aplicativo.  Com o SQL e o SAP HANA, a extensão de backup obtém acesso temporário para gravar em BLOBs específicos.  Dessa forma, mesmo em um ambiente comprometido, os backups existentes não podem ser adulterados ou excluídos pelo convidado.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Conectividade com a Internet não necessária para o backup de VM do Azure

O backup de VMs do Azure requer a movimentação de dados do disco da máquina virtual para o cofre dos serviços de recuperação. No entanto, toda a comunicação necessária e a transferência de dados acontecem apenas na rede de backbone do Azure sem a necessidade de acessar sua rede virtual. Portanto, o backup de VMs do Azure colocadas dentro de redes seguras não exige que você permita o acesso a IPs ou FQDNs.

## <a name="private-endpoints-for-azure-backup"></a>Pontos de extremidade privados para o backup do Azure

Agora você pode usar [pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de seus dados com segurança de servidores dentro de uma rede virtual para seu cofre de serviços de recuperação. O ponto de extremidade privado usa um IP do espaço de endereço de VNET para seu cofre, portanto, você não precisa expor suas redes virtuais para qualquer IPs público. Pontos de extremidade privados podem ser usados para fazer backup e restaurar seus bancos de dados SQL e SAP HANA que são executados dentro de suas VMs do Azure. Ele também pode ser usado para seus servidores locais usando o agente MARS.

>[!NOTE]
> Este recurso está atualmente em disponibilidade limitada. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e [envie-nos um email](mailto:azbackupnetsec@microsoft.com) se estiver interessado em usar pontos de extremidade privados para o backup do Azure. A capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Criptografia de dados em trânsito e em repouso

A criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. No Azure, os dados em trânsito entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede de backbone do Azure.

* Os dados de backup são criptografados automaticamente usando chaves gerenciadas pela Microsoft. Você também pode criptografar suas VMs de disco gerenciado de backup no cofre dos serviços de recuperação usando [chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) armazenadas no Azure Key Vault. Você não precisa tomar nenhuma ação explícita para habilitar essa criptografia. Ele se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre de serviços de recuperação.

* O backup do Azure dá suporte ao backup e à restauração de VMs do Azure que têm seus discos de sistema operacional/dados criptografados com o Azure Disk Encryption (ADE). [Saiba mais sobre as VMs do Azure e o backup do Azure criptografados](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de exclusões não intencionais

O backup do Azure fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Com a exclusão reversível, se o usuário excluir o backup de uma VM, os dados de backup serão mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias dos dados de backup no estado de "exclusão reversível" não incorre em nenhum custo para o cliente. [Saiba mais sobre a exclusão reversível](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoramento e alertas de atividade suspeita

O backup do Azure fornece [recursos internos de monitoramento e alerta](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) para exibir e configurar ações de eventos relacionados ao backup do Azure. Os [relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports) funcionam como um destino único para controlar o uso, a auditoria de backups e restaurações e a identificação das principais tendências em diferentes níveis de granularidade. Usar as ferramentas de monitoramento e relatório do backup do Azure pode alertá-lo sobre qualquer atividade não autorizada, suspeita ou mal-intencionada assim que ocorrer.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Recursos de segurança para ajudar a proteger backups híbridos

O serviço de backup do Azure usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup e restaurar arquivos, pastas e o volume ou o estado do sistema de um computador local para o Azure. O MARS agora fornece recursos de segurança para ajudar a proteger backups híbridos. Esses recursos incluem:

* Uma camada adicional de autenticação será adicionada sempre que uma operação crítica, como a alteração de senha, for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais válidas do Azure. [Saiba mais sobre os recursos que impedem ataques](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Os dados de backup excluídos são retidos por mais 14 dias a partir da data de exclusão. Isso garante a capacidade de recuperação dos dados em um determinado período de tempo, portanto, não há perda de dados, mesmo se ocorrer um ataque. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos. [Saiba mais sobre como recuperar dados de backup excluídos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Para backup de dados usando o agente de Serviços de Recuperação do Microsoft Azure (MARS), uma frase secreta é usada para garantir que os dados sejam criptografados antes do carregamento para o backup do Azure e descriptografados somente após o download do backup do Azure. Os detalhes da frase secreta estão disponíveis somente para o usuário que criou a senha e o agente que está configurado com ele. Nada é transmitido ou compartilhado com o serviço. Isso garante a segurança total de seus dados, pois todos os dados expostos inadvertidamente (como um ataque man-in-the-Middle na rede) são inutilizáveis sem a frase secreta e a senha não é enviada na rede.

## <a name="compliance-with-standardized-security-requirements"></a>Conformidade com os requisitos de segurança padronizados

Para ajudar as organizações a cumprirem os requisitos nacionais, regionais e específicos do setor que regem a coleta e o uso de dados de indivíduos, Microsoft Azure & o backup do Azure oferece um conjunto abrangente de certificações e atestado. [Consulte a lista de certificações de conformidade](compliance-offerings.md)

## <a name="next-steps"></a>Próximas etapas

* [Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem que usam o backup do Azure](backup-azure-security-feature-cloud.md)
* [Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure](backup-azure-security-feature.md)
