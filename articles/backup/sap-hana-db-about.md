---
title: Sobre o backup do banco de dados SAP HANA em VMs do Azure
description: Neste artigo, saiba como fazer backup de bancos de dados SAP HANA que estão sendo executados em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476450"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Sobre o backup do banco de dados SAP HANA em VMs do Azure

Os bancos de dados SAP HANA são cargas de trabalho críticas de missão que requerem um RPO (Objetivo de ponto de recuperação baixo) e um objetivo de tempo de recuperação rápido (RTO). Agora você pode fazer backup dos [bancos de dados SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) usando [o Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

O Azure Backup é certificado pelo [Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) pela SAP, para fornecer suporte de backup nativo, aproveitando as APIs nativas do SAP HANA. Essa oferta do Azure Backup está alinhada com o mantra do Azure Backup de backups **de infra-estrutura zero,** eliminando a necessidade de implantar e gerenciar a infra-estrutura de backup. Agora, você pode fazer backup e restaurar perfeitamente os bancos de dados SAP HANA em execução em VMs azure[(VMs da série M](../virtual-machines/m-series.md) também suportadas agora!) e aproveitar os recursos de gerenciamento corporativo que o Azure Backup fornece.

## <a name="added-value"></a>Valor adicionado

Usar o Backup do Azure para fazer backup e restaurar bancos de dados SAP HANA, oferece as seguintes vantagens:

* **Rpo (Recovery Point Objective, ponto de recuperação) de 15 minutos:** A recuperação de dados críticos de até 15 minutos agora é possível.
* **Restaurações pontuais**de um clique: A restauração dos dados de produção para servidores HANA alternativos é facilitada. O encadeamento de backups e catálogos para realizar restaurações é tudo gerenciado pelo Azure nos bastidores.
* **Retenção a longo prazo**: Para rigorosas necessidades de conformidade e auditoria. Mantenha seus backups por anos, com base na duração da retenção, além do qual os pontos de recuperação serão podados automaticamente pela capacidade de gerenciamento do ciclo de vida incorporado.
* **Gerenciamento de backup do Azure**: Use os recursos de gerenciamento e monitoramento do Azure Backup para melhorar a experiência de gerenciamento. O Azure CLI também é suportado.

Para visualizar os cenários de backup e restauração que apoiamos hoje, consulte a matriz de suporte ao [cenário SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Arquitetura de backup

![Diagrama de arquitetura de backup](./media/sap-hana-db-about/backup-architecture.png)

* O processo de backup começa [criando um cofre de serviços de recuperação](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) no Azure. Este cofre será usado para armazenar os backups e pontos de recuperação criados ao longo do tempo.
* O Azure VM executando o servidor SAP HANA é registrado no cofre, e os bancos de dados a serem backup são [descobertos](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Para habilitar o serviço de backup do Azure para descobrir bancos de dados, um [script de pré-registro](https://aka.ms/scriptforpermsonhana) deve ser executado no servidor HANA como um usuário raiz.
* Este script cria o usuário **AZUREWLBACKUPHANAUSER** DB e uma chave correspondente com o mesmo nome em **hdbuserstore**. Consulte o [que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) para entender mais sobre o que o script faz.
* O Azure Backup Service agora instala o **Plugin de backup do Azure para HANA** no servidor SAP HANA registrado.
* O usuário **AZUREWLBACKUPHANAUSER** DB criado pelo script de pré-registro é usado pelo Plugin de backup do **Azure para HANA** para executar todas as operações de backup e restauração. Se você tentar configurar o backup para DBs SAP HANA sem executar este script, você poderá receber o seguinte erro: **UserErrorHanaScriptNotRun**.
* Para configurar o [backup](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) nos bancos de dados descobertos, escolha a política de backup necessária e habilite backups.

* Uma vez configurado o backup, o serviço de backup do Azure configura os seguintes parâmetros de Backint no nível de DATABASE no servidor SAP HANA protegido:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Certifique-se de que esses parâmetros *não* estão presentes no nível HOST. Os parâmetros de nível de host substituirão esses parâmetros e podem causar comportamentos inesperados.
>

* O **Plugin de backup do Azure para HANA** mantém todos os horários de backup e detalhes da política. Ele aciona os backups programados e se comunica com o **HANA Backup Engine** através das APIs backint.
* O **HANA Backup Engine** retorna um fluxo Backint com os dados a serem copiados.
* Todos os backups programados e backups demanda (acionados do portal Azure) que são completos ou diferenciais são iniciados pelo Plugin de backup do **Azure para HANA**. No entanto, os backups de log são gerenciados e acionados pelo próprio **HANA Backup Engine.**
* O Backup do Azure para SAP HANA, sendo uma solução certificada pelo BackInt, não depende dos tipos de disco ou VM subjacentes. O backup é realizado por fluxos gerados pelo HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Usando backup do Azure VM com backup Azure SAP HANA

Além de usar o backup SAP HANA no Azure que fornece backup e recuperação de nível de banco de dados, você pode usar a solução de backup do Azure VM para fazer backup dos discos de sO e não-banco de dados.

A [solução de backup Azure SAP HANA certificada pelo Backint](#backup-architecture) pode ser usada para backup e recuperação de banco de dados.

[O backup do Azure VM](backup-azure-vms-introduction.md) pode ser usado para fazer backup do sistema operacional e outros discos não-banco de dados. O backup da VM é feito uma vez por dia e faz backup de todos os discos (exceto **discos write accelerator (WA)** e **UltraDisks**). Uma vez que o banco de dados está sendo feito backup usando a solução de backup Azure SAP HANA, você pode fazer um backup consistente com o arquivo apenas dos discos do SISTEMA OPERACIONAL e não-banco de dados usando o recurso de exclusão de disco, que está atualmente em visualização.

>[!NOTE]
> O uso de scripts pré-post com o backup do Azure VM permitirá backups consistentes com aplicativos dos volumes de dados do banco de dados. No entanto, se a área de log residir em discos WA, tirar um instantâneo desses discos pode não garantir a consistência da área de log. Hana tem uma maneira explícita de gerar backups de log por esta razão exata. Habilite o mesmo em seu SAP HANA, e eles podem ser backup usando backup Azure SAP HANA.

Para restaurar um VM executando o SAP HANA, siga estas etapas:

* [Restaurar uma nova VM do backup do Azure VM](backup-azure-arm-restore-vms.md) a partir do ponto de recuperação mais recente. Ou crie uma nova VM vazia e anexe os discos do ponto de recuperação mais recente.
* Como os discos WA não estão em backup, eles não são restaurados. Crie discos WA vazios e área de log.
* Depois que todas as outras configurações (como IP, nome do sistema e assim por diante) são definidas, a VM está definida para receber dados DB do backup do Azure.
* Agora, restaure o DB no VM do [backup do Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) até o ponto-em-tempo desejado.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar um banco de dados SAP HANA em execução em uma VM Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
