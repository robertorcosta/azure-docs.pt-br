---
title: Módulo Ansible e matriz de versão para o Azure | Microsoft Docs
description: Matriz de módulo e versão Ansible do Azure
keywords: ansible, funções, matriz, versão, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 275dca40ab20c222da2b9115f9a5dc141228c766
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385458"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. Este artigo lista os vários módulos do Ansible para o Azure e as versões do Ansible em que eles são fornecidos.

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure

Os módulos a seguir podem ser executados diretamente em hosts remotos ou por meio de guias estratégicos.  

Esses módulos estão disponíveis na versão oficial do Ansible e nas seguintes funções do guia estratégico da Microsoft.

> [!NOTE]
> Do Ansible 2,9 em diante, renomeamos todos os módulos * _facts para * _info para aderir à Convenção de nomenclatura de Ansible. Os módulos antigos e renomeados são vinculados de modo que, além de ver um aviso de reprovação, todos os módulos funcionam como antes.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Computação**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_availabilityset_info              | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_deployment                         | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_functionapp                        | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_functionapp_info                  | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_image                              | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_image_info                        | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_resource                           | -            | -                           | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_resource_info                     | -            | -                           | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_resourcegroup                      | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_resourcegroup_info                | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_virtualmachine                     | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachineextension            | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualmachineimage_info          | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescaleset             | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescaleset_info       | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | SIM          | SIM          | SIM          |
| **Rede**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_dnsrecordset                       | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_dnsrecordset_info                 | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_dnszone                            | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_dnszone_info                      | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_loadbalancer                       | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_loadbalancer_info                 | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_networkinterface                   | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_networkinterface_info             | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_publicipaddress                    | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_publicipaddress_info              | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_route                              | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_routetable                         | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_routetable_info                   | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_securitygroup                      | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_subnet                             | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualnetwork                     | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualnetwork_info               | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | SIM          | SIM          |
| **Armazenamento**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_manageddisk_info                  | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_storageaccount                     | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_storageaccount_info               | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_storageblob                        | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_webapp                             | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_webapp_info                       | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | SIM          | SIM          | SIM          |
| **Contêineres**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | SIM          | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_aks                                | -            | -                           | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_aks_info                          | -            | -                           | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_containerinstance                  | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_containerregistry                  | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_containerregistry_info            | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | SIM          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | SIM          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | SIM          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | SIM          |
| **Bancos de dados**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mysqldatabase                      | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_mysqlserver                        | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_postgresqldatabase                 | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_postgresqlserver                   | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_sqldatabase                        | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | SIM          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | SIM          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | SIM          | SIM          | SIM          | SIM          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_sqlserver                          | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_sqlserver_info                    | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | SIM          | SIM          |
| **Integração**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| **Segurança**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | SIM          | SIM          | SIM          |
| azure_rm_keyvaultkey                        | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_keyvaultsecret                     | -            | SIM                         | SIM          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | SIM          | SIM          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_autoscale_info                    | -            | -                         | -          | SIM          | SIM          | SIM          | SIM          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | SIM          | SIM          | SIM          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | SIM          | SIM          |
| **Gerenciamento e governança**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | SIM        | SIM          |
| **Internet das Coisas**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | SIM        | SIM          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | SIM        | SIM          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure

A [função de guia estratégico de azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclui todos os módulos mais recentes do Azure. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, você será incentivado a instalar a função de guia estratégico `azure_preview_module`.

A função de guia estratégico `azure_preview_module` é lançada a cada três semanas.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre as funções de guia estratégico, consulte [criando guias estratégicos reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 