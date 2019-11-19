---
title: Módulo Ansible e matriz de versão para o Azure | Microsoft Docs
description: Matriz de módulo e versão Ansible do Azure
keywords: ansible, funções, matriz, versão, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155991"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. Este artigo lista os vários módulos do Ansible para o Azure e as versões do Ansible em que eles são fornecidos.

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure

Os módulos a seguir podem ser executados diretamente em hosts remotos ou por meio de guias estratégicos.  

Esses módulos estão disponíveis na versão oficial do Ansible e nas seguintes funções do guia estratégico da Microsoft.

> [!NOTE]
> Do Ansible 2,9 em diante, renomeamos todos os módulos * _facts para * _info para aderir à Convenção de nomenclatura do Ansible. Os módulos antigos e renomeados são vinculados de modo que, além de ver um aviso de reprovação, todos os módulos funcionam como antes.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Computação**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_availabilityset_info              | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_deployment                         | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_functionapp                        | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_functionapp_info                  | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_image                              | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_image_info                        | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_resource                           | -            | -                           | sim          | sim          | sim          | sim          | sim          |
| azure_rm_resource_info                     | -            | -                           | sim          | sim          | sim          | sim          | sim          |
| azure_rm_resourcegroup                      | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_resourcegroup_info                | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_virtualmachine                     | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachineextension            | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_virtualmachineimage_info          | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachinescaleset             | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachinescaleset_info       | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | sim          | sim          | sim          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | sim          | sim          | sim          |
| **Rede**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_dnsrecordset                       | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_dnsrecordset_info                 | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_dnszone                            | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_dnszone_info                      | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_loadbalancer                       | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_loadbalancer_info                 | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_networkinterface                   | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_networkinterface_info             | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_publicipaddress                    | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_publicipaddress_info              | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_route                              | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_routetable                         | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_routetable_info                   | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_securitygroup                      | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_subnet                             | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_virtualnetwork                     | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualnetwork_info               | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | sim          | sim          | sim          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | sim          | sim          |
| **Armazenamento**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_manageddisk_info                  | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_storageaccount                     | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_storageaccount_info               | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_storageblob                        | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_webapp                             | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_webapp_info                       | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | sim          | sim          | sim          |
| **Contêineres**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | sim          | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_aks                                | -            | -                           | sim          | sim          | sim          | sim          | sim          |
| azure_rm_aks_info                          | -            | -                           | sim          | sim          | sim          | sim          | sim          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_containerinstance                  | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_containerregistry                  | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_containerregistry_info            | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | sim          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | sim          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | sim          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | sim          |
| **Bancos de dados**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mysqldatabase                      | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_mysqlserver                        | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_postgresqldatabase                 | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_postgresqlserver                   | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_sqldatabase                        | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | sim          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | sim          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | sim          | sim          | sim          | sim          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_sqlserver                          | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_sqlserver_info                    | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | sim          | sim          |
| **Integração**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | sim          | sim          | sim          |
| **Segurança**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | sim          | sim          | sim          |
| azure_rm_keyvaultkey                        | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_keyvaultsecret                     | -            | sim                         | sim          | sim          | sim          | sim          | sim          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | sim          | sim          | sim          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | sim          | sim          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | sim          | sim          | sim          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_autoscale_info                    | -            | -                         | -          | sim          | sim          | sim          | sim          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | sim          | sim          | sim          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | sim          | sim          |
| **Gerenciamento e governança**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | sim        | sim          |
| **Internet das Coisas**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | sim        | sim          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | sim        | sim          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure

A [azure_preview_module função de guia estratégico](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclui todos os módulos mais recentes do Azure. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, você será incentivado a instalar a `azure_preview_module` função de guia estratégico.

A `azure_preview_module` função de guia estratégico é lançada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as funções de guia estratégico, consulte [criando guias estratégicos reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 