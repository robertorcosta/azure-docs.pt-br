---
title: Solucionando problemas de bancos de dados SAP HANA erros de backup
description: Descreve como solucionar problemas que podem ocorrer quando você usa o Azure Backup para fazer backup dos bancos de dados SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459303"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solucionar problemas de backup de bancos de dados SAP HANA no Azure

Este artigo fornece informações de solução de problemas para fazer backup de bancos de dados SAP HANA em máquinas virtuais Do Zure. Para obter mais informações sobre os cenários de backup sap HANA que atualmente suportamos, consulte [Suporte ao cenário](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Pré-requisitos e Permissões

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) antes de configurar backups.

## <a name="common-user-errors"></a>Erros comuns do usuário

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Mensagem de erro**      | <span style="font-weight:normal">O backup do Azure não tem privilégios de função necessários para realizar backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possíveis causas**    | O papel pode ter sido substituído.                          |
| **Ação recomendada** | Para resolver o problema, execute o script do painel **Discover DB** ou baixe-o [aqui](https://aka.ms/scriptforpermsonhana). Alternativamente, adicione a função 'SAP_INTERNAL_HANA_SUPPORT' ao Usuário de Backup da Carga de Trabalho (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>Erro do usuárioAberturaHanaOdbcConexão

| Mensagem de erro      | <span style="font-weight:normal">Falha ao conectar ao sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância sap HANA pode estar em baixo.<br/>As permissões necessárias para o backup do Azure interagir com o banco de dados HANA não estão definidas. |
| **Ação recomendada** | Verifique se o banco de dados SAP HANA está em dia. Se o banco de dados estiver em funcionamento, verifique se todas as permissões necessárias estão definidas. Se alguma das permissões estiver faltando, execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) para adicionar as permissões faltantes. |

### <a name="usererrorhanainstancenameinvalid"></a>UsuárioErrorHanaInstanceNameInvalid

| Mensagem de erro      | <span style="font-weight:normal">A instância especificada do SAP HANA é inválida ou não pode ser encontrada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Várias instâncias sap HANA em um único VM Azure não podem ser backup. |
| **Ação recomendada** | Execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) na instância SAP HANA que você deseja fazer backup. Se o problema ainda persistir, entre em contato com o suporte da Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Mensagem de erro      | <span style="font-weight:normal">A operação SAP HANA especificada não é suportada</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O backup do Azure para SAP HANA não suporta backup incremental e ações realizadas em clientes nativos do SAP HANA (Studio/ Cockpit/ DBA Cockpit) |
| **Ação recomendada** | Para obter mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNãoSuportaBackupType

| Mensagem de erro      | <span style="font-weight:normal">Este banco de dados SAP HANA não suporta o tipo de backup solicitado</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O backup do Azure não suporta backup e backup incrementais usando instantâneos |
| **Ação recomendada** | Para obter mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>Falha de validação do UsuárioErrorHANALSN

| Mensagem de erro      | <span style="font-weight:normal">Cadeia de log de backup está quebrada</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O destino de backup de log pode ter sido atualizado de backint para sistema de arquivos ou o executável backint pode ter sido alterado |
| **Ação recomendada** | Acione um backup completo para resolver o problema                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>Erro do usuárioIncomaptibleSrcTargetSystsemsForRestore

| Mensagem de erro      | <span style="font-weight:normal">Os sistemas de origem e destino para restauração são incompatíveis</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O sistema de destino para restauração é incompatível com a fonte |
| **Ação recomendada** | Consulte o SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração suportados hoje |

### <a name="usererrorsdctomdcupgradedetected"></a>UsuárioErrorSCtoMDCUpgradeDetected

| Mensagem de erro      | <span style="font-weight:normal">Atualização SDC para MDC detectada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A ocorrência SAP HANA foi atualizada de SDC para MDC. Os backups falharão após a atualização. |
| **Ação recomendada** | Siga as etapas listadas na [atualização da seção SAP HANA 1.0 para 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>Erro de usuárioInvalidReintConfiguração

| Mensagem de erro      | <span style="font-weight:normal">Configuração de backint inválida detectada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Os parâmetros de backup são especificados incorretamente para backup do Azure |
| **Ação recomendada** | Verifique se os seguintes parâmetros (backint) estão definidos:<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:false]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Se os parâmetros baseados em backint estiverem presentes no HOST, remova-os. Se os parâmetros não estão presentes no nível HOST, mas foram modificados manualmente em um nível de banco de dados, reverta-os para os valores apropriados como descrito anteriormente. Ou, execute [a proteção contra stop e retenha dados](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) de backup do portal Azure e selecione **Retomar backup**. |

## <a name="restore-checks"></a>Restaurar verificações

### <a name="single-container-database-sdc-restore"></a>Restauração do Banco de Dados de Contêineres Únicos (SDC)

Cuide das entradas enquanto restaura um único banco de dados de contêineres (SDC) para HANA para outra máquina SDC. O nome do banco de dados deve ser dado com minúsculas e com "sdc" anexado entre parênteses. A instância hana será exibida em capitais.

Suponha que uma ocorrência "H21" do SDC HANA seja apoiada. A página de itens de backup mostrará o nome do item de backup como **"h21(sdc)"**. Se você tentar restaurar esse banco de dados para outro SDC de destino, digamos H11, então as entradas seguintes precisam ser fornecidas.

![Nome do banco de dados SDC restaurado](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observe os seguintes pontos:

- Por padrão, o nome db restaurado será preenchido com o nome do item de backup. Neste caso, h21(sdc).
- A seleção do destino como H11 NÃO mudará o nome db restaurado automaticamente. **Deve ser editado para h11(sdc)**. Em relação ao SDC, o nome db restaurado será o ID de instância de destino com letras minúsculas e 'sdc' anexados entre parênteses.
- Como o SDC pode ter apenas um banco de dados único, você também precisa clicar na caixa de seleção para permitir a substituição dos dados de banco de dados existentes com os dados do ponto de recuperação.
- O Linux é sensível a casos. Então tenha cuidado para preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauração do Banco de Dados de Vários Contêineres (MDC)

Em vários bancos de dados de contêineres para HANA, a configuração padrão é SYSTEMDB + 1 ou mais DBs inquilinos. Restaurar uma instância HANA inteira significa restaurar os DBs systemdb e inquilinos. Um restaura o SYSTEMDB primeiro e depois prossegue para o Inquilino DB. O sistema DB significa essencialmente substituir as informações do sistema no alvo selecionado. Essa restauração também substitui as informações relacionadas ao BackInt na instância de destino. Assim, depois que o DB do sistema for restaurado para uma instância de destino, execute o script de pré-registro novamente. Só então as restaurações subseqüentes do INQUILINO DB terão sucesso.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Atualização do SAP HANA 1.0 para 2.0

Se você estiver protegendo os bancos de dados SAP HANA 1.0 e desejar atualizar para 2.0, execute as seguintes etapas:

- [Pare a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com dados de retenção para o banco de dados SDC antigo.
- Realize a atualização. Após a conclusão, o HANA agora é MDC com um DB do sistema e db(s) inquilino
- Reexecute [o script de pré-registro](https://aka.ms/scriptforpermsonhana) com detalhes corretos de (sid e mdc).
- Reregistre a extensão para a mesma máquina no portal Azure (Backup -> exibir detalhes -> Selecione o Recadastramento azure -> Re-register).
- Clique em Redescobrir DBs para a mesma VM. Esta ação deve mostrar os novos DBs na etapa 2 com detalhes corretos (SYSTEMDB e Tenant DB, não SDC).
- Configure backup para esses novos bancos de dados.

## <a name="upgrading-without-an-sid-change"></a>Atualização sem uma alteração SID

Upgrades para O ou SAP HANA que não causem uma alteração SID podem ser tratados conforme descrito abaixo:

- [Parar a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com dados de retenção para o banco de dados
- Realize a atualização.
- Reexecute o [script de pré-registro](https://aka.ms/scriptforpermsonhana). Normalmente, vimos o processo de upgrade remover as funções necessárias. A execução do script de pré-registro ajudará a verificar todas as funções necessárias.
- [Retomar a proteção](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) para o banco de dados novamente

## <a name="re-registration-failures"></a>Falhas de reregistro

Verifique se há um ou mais dos seguintes sintomas antes de acionar a operação de re-registro:

- Todas as operações (como backup, restauração e backup configurado) estão falhando na VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable, UserErrorWorkloadWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidnTDequeueMsg**.
- Se a área **de status de backup** do item de backup estiver mostrando não **acessível,** exclua todas as outras causas que possam resultar no mesmo status:

  - Falta de permissão para executar operações relacionadas ao backup na VM
  - A VM está desligada, então os backups não podem ocorrer
  - Problemas de rede

Esses sintomas podem surgir por uma ou mais das seguintes razões:

- Uma extensão foi excluída ou desinstalada do portal.
- A VM foi restaurada no tempo através da restauração do disco no local.
- A VM foi desligada por um período prolongado, então a configuração de extensão nele expirou.
- A VM foi excluída, e outra VM foi criada com o mesmo nome e no mesmo grupo de recursos da VM excluída.

Nos cenários anteriores, recomendamos que você acione uma operação de re-registro na VM.

## <a name="next-steps"></a>Próximas etapas

- Revise as [perguntas freqüentes](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) sobre o backup de bancos de dados SAP HANA em VMs Azure.
