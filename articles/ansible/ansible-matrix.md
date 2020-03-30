---
title: Módulo ansible e matriz de versão para Azure | Microsoft Docs
description: Matriz de módulo e versão Ansible do Azure
keywords: ansible, funções, matriz, versão, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74155991"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. Este artigo lista os vários módulos Ansible para o Azure e as versões Ansible em que eles enviam.

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure

Os módulos a seguir podem ser executados diretamente em hosts remotos ou através de cartilhas.  

Esses módulos estão disponíveis a partir do lançamento oficial da Ansible e das seguintes funções de playbook da Microsoft.

> [!NOTE]
> A partir de Ansible 2.9 em diante, renomeamos todos os módulos *_facts para *_info aderir à convenção de nomeação ansible. Os módulos antigos e renomeados estão ligados para que, além de ver um aviso de depreciação, todos os módulos funcionem como antes.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Calcular**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_availabilityset_info              | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_deployment                         | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_functionapp                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_functionapp_info                  | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_image                              | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_resource                           | -            | -                           | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resource_info                     | -            | -                           | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resourcegroup                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resourcegroup_info                | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachine                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachineextension            | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualmachineimage_info          | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescaleset             | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescaleset_info       | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Sim          | Sim          | Sim          |
| **Rede**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_dnsrecordset                       | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnsrecordset_info                 | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnszone                            | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnszone_info                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_loadbalancer                       | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_loadbalancer_info                 | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_networkinterface                   | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_networkinterface_info             | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_publicipaddress                    | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_publicipaddress_info              | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_route                              | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_routetable                         | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_routetable_info                   | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_securitygroup                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_subnet                             | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetwork                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetwork_info               | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Sim          | Sim          |
| **Armazenamento**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_manageddisk_info                  | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageaccount                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageaccount_info               | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageblob                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_webapp                             | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_webapp_info                       | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Sim          | Sim          | Sim          |
| **Recipientes**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aks                                | -            | -                           | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aks_info                          | -            | -                           | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_containerinstance                  | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_containerregistry                  | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Sim          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Sim          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Sim          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Sim          |
| **Bancos**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqldatabase                      | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqlserver                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqldatabase                 | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqlserver                   | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_sqldatabase                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Sim          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Sim          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sim          | Sim          | Sim          | Sim          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_sqlserver                          | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_sqlserver_info                    | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Sim          | Sim          |
| **Integração**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| **Segurança**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Sim          | Sim          | Sim          |
| azure_rm_keyvaultkey                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_keyvaultsecret                     | -            | Sim                         | Sim          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| **Devops**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Sim          | Sim          | Sim          | Sim          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Sim          | Sim          | Sim          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Sim          | Sim          |
| **Gerenciamento e governança**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Sim        | Sim          |
| **Internet das Coisas**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Sim        | Sim          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Sim        | Sim          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure

A [azure_preview_module função playbook](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclui todos os módulos Azure mais recentes. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de `azure_preview_module` recursos do Azure, você será encorajado a instalar a função playbook.

O `azure_preview_module` papel do livro de jogadas é lançado a cada três semanas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os papéis do livro de [jogadas, consulte Criando livros de jogadas reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 