---
title: Conceitos-identidade e acesso
description: Saiba mais sobre os conceitos de identidade e acesso da solução do Azure VMware
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7127109801d92d2177f6edac3efcaf76ddf217e6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674652"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade da solução VMware do Azure

Ao implantar uma nuvem privada, um servidor vCenter e o Gerenciador de NSX-T são provisionados. Use o vCenter para gerenciar cargas de trabalho de máquina virtual (VM). Use o Gerenciador de NSX-T para estender a rede definida pelo software de nuvem privada.

Gerenciamento de acesso e identidade use privilégios de grupo CloudAdmin para o vCenter e direitos de administrador restritos para o Gerenciador de NSX-T. Ele garante que sua plataforma de nuvem privada seja automaticamente atualizada com os recursos e patches mais recentes.  Para obter mais informações, consulte o [artigo conceitos de atualizações de nuvem privada][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>acesso e identidade do vCenter

Os privilégios no vCenter são fornecidos por meio do grupo CloudAdmin. Esse grupo pode ser gerenciado localmente no vCenter ou por meio da integração do logon único LDAP do vCenter com o Azure Active Directory. Você tem a capacidade de habilitar essa integração depois de implantar uma nuvem privada.

Os privilégios CloudAdmin e CloudGlobalAdmin são mostrados na tabela a seguir.

|  Conjunto de privilégios           | CloudAdmin | CloudGlobalAdmin | Comentário |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmes                  | Um usuário CloudAdmin tem todos os privilégios de alarmes no Compute-ResourcePool e nas VMs.     |          --        |  -- |
|  Implantação automática             |  --  |        --        |  A Microsoft faz o gerenciamento de hosts.  |
|  Certificados            |  --  |        --       |  A Microsoft faz o gerenciamento de certificados.  |
|  Biblioteca de conteúdo         | Um usuário CloudAdmin tem privilégios para criar e usar arquivos em uma biblioteca de conteúdo.    |         Habilitado com SSO.         |  A Microsoft distribuirá arquivos na biblioteca de conteúdo para hosts ESXi.  |
|  Datacenter              |  --  |        --          |  A Microsoft faz todas as operações de data center.  |
|  Repositório de dados               | Datastore. AllocateSpace, datastore. procurar, Datastore.Config, datastore. Excluirfile, datastore. filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Gerenciador de Agentes ESX       |  --  |         --       |  A Microsoft faz todas as operações.  |
|  Pasta                  |  Um usuário CloudAdmin tem todos os privilégios de pasta.     |  --  |  --  |
|  Global                  |  Global. CancelTask, global. GlobalTag, global. Health, global. LogEvent, global. ManageCustomFields, global. promanagers, global. autocustomfield, Global.SystemTag         |                  |    |
|  Host                    |  Host. HBR. HbrManagement      |        --          |  A Microsoft faz todas as outras operações de host.  |
|  InventoryService        |  InventoryService. marcação      |        --          |  --  |
|  Rede                 |  Network.Assign    |                  |  A Microsoft faz todas as outras operações de rede.  |
|  Permissões             |  --  |        --       |  A Microsoft faz todas as operações de permissões.  |
|  Armazenamento controlado por perfil  |  --  |        --       |  A Microsoft faz todas as operações de perfil.  |
|  Recurso                |  Um usuário CloudAdmin tem todos os privilégios de recurso.        |      --       | --   |
|  Tarefa agendada          |  Um usuário CloudAdmin tem todos os privilégios de ScheduleTask.   |   --   | -- |
|  Sessões                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  A Microsoft realiza todas as outras operações de sessão.  |
|  Exibições de armazenamento           |  StorageViews. exibição   |        --          |  A Microsoft faz todas as outras operações de exibição de armazenamento (configure service).  |
|  Tarefas                   |  --  |  --   |  A Microsoft gerencia extensões que gerenciam tarefas.  |
|  vApp                    |  Um usuário CloudAdmin tem todos os privilégios de vApp.  |  --  |  --  |
|  Máquina Virtual         |  Um usuário CloudAdmin tem todos os privilégios de VirtualMachine.  |  --  |  --  |
|  vService                |  Um usuário CloudAdmin tem todos os privilégios de vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do NSX-T Manager

Use a conta "administrador" para acessar o Gerenciador de NSX-T. Ele tem privilégios totais e permite criar e gerenciar roteadores T1, comutadores lógicos e todos os serviços. Os privilégios dão acesso ao roteador NSX-T T0. Uma alteração no roteador de T0 pode resultar em desempenho de rede degradado ou sem acesso à nuvem privada. Abra uma solicitação de suporte no portal do Azure para solicitar qualquer alteração ao seu roteador do NSX-T T0.
  
## <a name="next-steps"></a>Próximas etapas

A próxima etapa é aprender sobre os [conceitos de atualização de nuvem privada][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md