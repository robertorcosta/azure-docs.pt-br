---
title: Solução Azure VMware by CloudSimple - Modelo de permissão de nuvem privada
description: Descreve o modelo de permissão, grupos e categorias do CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014939"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Modelo de permissão cloud simple private cloud do VMware vCenter

O CloudSimple mantém acesso administrativo total ao ambiente Private Cloud. Cada cliente CloudSimple recebe privilégios administrativos suficientes para poder implantar e gerenciar as máquinas virtuais em seu ambiente.  Se necessário, você pode escalar temporariamente seus privilégios para desempenhar funções administrativas.

## <a name="cloud-owner"></a>Proprietário de Nuvem

Quando você cria uma Nuvem Privada, um usuário **do CloudOwner** é criado no domínio vCenter Single Sign-On, com acesso **cloud-owner-role** para gerenciar objetos na Nuvem Privada. Este usuário também pode configurar fontes de identidade adicionais [do vCenter](set-vcenter-identity.md)e outros usuários para o vCenter da Nuvem Privada.

> [!NOTE]
> O usuário padrão do seu CloudSimple Private Cloud vCenter é cloudowner@cloudsimple.local quando uma Nuvem Privada é criada.

## <a name="user-groups"></a>Grupos de usuários

Um grupo chamado **Cloud-Owner-Group** é criado durante a implantação de uma Nuvem Privada. Os usuários deste grupo podem administrar várias partes do ambiente vSphere na Nuvem Privada. Esse grupo recebe automaticamente privilégios **de Função de Proprietário de Nuvem** e o usuário do **CloudOwner** é adicionado como membro desse grupo.  O CloudSimple cria grupos adicionais com privilégios limitados para facilitar o gerenciamento.  Você pode adicionar qualquer usuário a esses grupos pré-criados e os privilégios definidos abaixo são automaticamente atribuídos aos usuários nos grupos.

### <a name="pre-created-groups"></a>Grupos pré-criados

| Nome do Grupo | Finalidade | Função |
| -------- | ------- | ------ |
| Grupo proprietário de nuvem | Os membros deste grupo têm privilégios administrativos para o Private Cloud vCenter | [Papel do proprietário da nuvem](#cloud-owner-role) |
| Grupo de administradores de cloud-global-cluster | Os membros deste grupo têm privilégios administrativos no Private Cloud vCenter Cluster | [Função de administrador de cluster em nuvem](#cloud-cluster-admin-role) |
| Grupo de administradores de armazenamento global em nuvem | Os membros deste grupo podem gerenciar o armazenamento no private cloud vCenter | [Função de administrador de armazenamento em nuvem](#cloud-storage-admin-role) |
| Grupo de admin Cloud-Global-Network-Admin | Os membros deste grupo podem gerenciar grupos de portas distribuídas e de rede no vCenter private cloud | [Função de admin de rede de nuvem](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Os membros deste grupo podem gerenciar máquinas virtuais no vCenter private cloud | [Papel de admin cloud-VM-Admin](#cloud-vm-admin-role) |

Para conceder permissões individuais aos usuários para gerenciar a Nuvem Privada, crie contas de usuário adicionadas aos grupos apropriados.

> [!CAUTION]
> Novos usuários devem ser adicionados apenas ao *Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os usuários adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas contas de serviço devem ser adicionadas ao grupo *Administradores* e as contas de serviço não devem ser usadas para fazer login na ui web vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista de privilégios do vCenter para funções padrão

### <a name="cloud-owner-role"></a>Papel do proprietário da nuvem

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Alarmes** | Reconhecer alerta <br> Criar alarme <br> Desativar a ação do alarme <br> Modificar alarme <br> Remover alarme <br> Definir o status do alarme |
| **Permissões** | Modificar permissão |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca <br> Criar biblioteca local <br> Criar biblioteca subscrita <br> Excluir item da biblioteca <br> Excluir biblioteca local <br> Excluir biblioteca subscrita <br> Baixar arquivos <br> Item da biblioteca de despejo <br> Biblioteca subscrita de despejo <br> Armazenamento de importação <br> Informações de assinatura da sonda <br> Ler armazenamento <br> Item da biblioteca de sincronização <br> Biblioteca subscrita do Sync <br> Introspecção de tipo <br> Configuração de atualização configurações <br> Atualizar arquivos <br> Biblioteca de atualização <br> Atualizar item da biblioteca <br> Atualizar biblioteca local <br> Atualizar biblioteca subscrita <br> Exibir configurações de configuração |
| **Operações criptográficas** | Adicionar disco <br> Clone <br> Descriptografar <br> Direct Access <br> Encrypt <br> Criptografar novos <br> Gerenciar KMS <br> Gerenciar políticas de criptografia <br> Gerenciar chaves <br> Migrar <br> Recriptografia <br> Registre VM <br> Registre hospedeiro |
| **grupo dvPort** | Criar <br> Excluir <br> Modificar <br> Operação de políticas <br> Operação de escopo |
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar datastore <br> Operações de arquivos de baixo nível <br> Mover datastore <br> Remover datastore <br> Remover arquivo <br> Renomear datastore <br> Atualizar arquivos de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Gerente de Agentes ESX** | Config <br> Modificar <br> Visualizar |
| **Extensão** | Registro de extensão <br> Extensão de não registro <br> Extensão de atualização |
| **Provedor de estatísticas externas**| Registrar  <br> Cancelar o registro  <br> Atualizar |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Global** | Cancelar tarefa <br> planejamento de capacidade <br> Diagnósticos <br> Desativar métodos <br> Habilitar métodos <br> Tag global <br> Integridade <br> Licenças <br> Evento de log <br> Gerenciar atributos personalizados <br> Proxy <br> Ação de script <br> Gerentes de serviços <br> Definir atributo personalizado <br> Tag do sistema |
| **Provedor de atualização de saúde** | Registrar  <br> Cancelar o registro  <br> Atualizar |
| **Configuração do > do host** | Configuração de partição de armazenamento |
| **Inventário de > de Host** | Modificar cluster |
| **Marcação vSphere** | Atribuir ou Desatribuir vSphere Tag <br> Criar tag vSphere <br> Criar categoria de tag vSphere <br> Excluir tag vSphere <br> Excluir categoria de tag vSphere <br> Editar tag vSphere <br> Editar categoria de tag vSphere <br> Modificar usadopor campo para categoria <br> Modificar usadopor campo para tag |
| **Rede** | Assign network <br> Configurar <br> Mover rede <br> Remover |
| **Desempenho** | Modificar intervalos |
| **Perfil do host** | Visualizar |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Assign virtual machine to resource pool <br> Criar pool de recursos <br> Migrar desligado da máquina virtual <br> Migrar alimentado na máquina virtual <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consulta vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefas <br> Modificar tarefa <br> Remover tarefa <br> Executar tarefa |
| **Sessões** | Usuário personificado <br> Mensagem <br> Validar sessão <br> Ver e interromper as sessões |
| **Cluster de datastore** | Configure um cluster de datastore |
| **Armazenamento orientado por perfil** | Atualização de armazenamento orientada a perfil <br> Exibição de armazenamento orientada a perfil |
| **Exibições de armazenamento** | Configurar serviços <br> Visualizar |
| **Tarefas** | Criar tarefa <br> Tarefa de atualização |
| **Serviço de transferência**| Gerenciar <br> Monitoramento |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Excluir <br> Exportação <br> Importar <br> Mover <br> Desligar <br> Ligue <br> Renomear <br> Suspend <br> Cancelar o registro  <br> Ver ambiente OVF <br> configuração do aplicativo vApp <br> configuração de instância de vApp <br> vApp gerenciadopor configuração <br> configuração de recursos do vApp |
| **Política vrm** | Consulta VRMPolicy <br> Atualizar a política vrm |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover o dispositivo <br> Avançado <br> Alterar a contagem da CPU <br> Alterar recurso <br> Configurar gerenciado <br> Rastreamento de alteração de disco <br> Locação de discos <br> Exibir configurações de conexão <br> Estender o disco virtual <br> Dispositivo USB host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade com tolerância a falhas de consulta <br> Consultar arquivos sem dono <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Configurações <br> Colocação de arquivos de swap <br> Alternar fork pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Máquina virtual > Operações de hóspedes** | Modificação de alias da operação do convidado <br> Consulta de alias de operação de hóspedes <br> Modificações na operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação > máquina virtual** | Responder pergunta <br> Operação de backup na máquina virtual <br> Configurar a mídia CD <br> Configurar mídia disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela VIX API <br> Injetar códigos de varredura USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou encolhimento <br> Desligar <br> Ligue <br> Sessão de gravação na máquina virtual <br> Sessão de replay na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Suspender a tolerância à falha <br> Failover de Teste <br> Reinicialização do teste VM secundário <br> Desativar a tolerância à falha <br> Ativar tolerância a falhas <br> Instalação de ferramentas VMware |
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar Novo <br> Mover <br> Registrar  <br> Remover <br> Cancelar o registro  |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir o download de máquinas virtuais <br> Permitir o upload de arquivos de máquinas virtuais <br> Modelo de clone <br> Clonar máquina virtual <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marca como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Leia as especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerenciar configurações de serviço <br> Modificar a configuração do serviço <br> Configurações do serviço de consulta <br> Leia a configuração do serviço |
| **Gerenciamento de instantâneos de > de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de > de máquina virtual vSphere** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Dependência de atualização |

### <a name="cloud-cluster-admin-role"></a>Função de administrador de cluster em nuvem

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar datastore <br> Operações de arquivos de baixo nível <br> Remover datastore <br> Renomear datastore <br> Atualizar arquivos de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Configuração do > do host**  | Configuração de partição de armazenamento |
| **Marcação vSphere** | Atribuir ou Desatribuir vSphere Tag <br> Criar tag vSphere <br> Criar categoria de tag vSphere <br> Excluir tag vSphere <br> Excluir categoria de tag vSphere <br> Editar tag vSphere <br> Editar categoria de tag vSphere <br> Modificar usadopor campo para categoria <br> Modificar usadopor campo para tag |
| **Rede** | Assign network |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Assign virtual machine to resource pool <br> Criar pool de recursos <br> Migrar desligado da máquina virtual <br> Migrar alimentado na máquina virtual <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consulta vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Excluir <br> Exportação <br> Importar <br> Mover <br> Desligar <br> Ligue <br> Renomear <br> Suspend <br> Cancelar o registro  <br> Ver ambiente OVF <br> configuração do aplicativo vApp <br> configuração de instância de vApp <br> vApp gerenciadopor configuração <br> configuração de recursos do vApp |
| **Política vrm** | Consulta VRMPolicy <br> Atualizar a política vrm |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover o dispositivo <br> Avançado <br> Alterar a contagem da CPU <br> Alterar recurso <br> Configurar gerenciado <br> Rastreamento de alteração de disco <br> Locação de discos <br> Exibir configurações de conexão <br> Estender o disco virtual <br> Dispositivo USB host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade com tolerância a falhas de consulta <br> Consultar arquivos sem dono <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Configurações <br> Colocação de arquivos de swap <br> Alternar fork pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Máquina virtual > Operações de hóspedes** | Modificação de alias da operação do convidado <br> Consulta de alias de operação de hóspedes <br> Modificações na operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação > máquina virtual** | Responder pergunta <br> Operação de backup na máquina virtual <br> Configurar a mídia CD <br> Configurar mídia disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela VIX API <br> Injetar códigos de varredura USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou encolhimento <br> Desligar <br> Ligue <br> Sessão de gravação na máquina virtual <br> Sessão de replay na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Suspender a tolerância à falha <br> Failover de Teste <br> Reinicialização do teste VM secundário <br> Desativar a tolerância à falha <br> Ativar tolerância a falhas <br> Instalação de ferramentas VMware
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar Novo <br> Mover <br> Registrar  <br> Remover <br> Cancelar o registro  |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir o download de máquinas virtuais <br> Permitir o upload de arquivos de máquinas virtuais <br> Modelo de clone <br> Clonar máquina virtual <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marca como máquina virtual <br> Modificar especificação de personalização <br> Promover discos  <br> Leia as especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerenciar configurações de serviço <br> Modificar a configuração do serviço <br> Configurações do serviço de consulta <br> Leia a configuração do serviço
| **Gerenciamento de instantâneos de > de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de > de máquina virtual vSphere** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Dependência de atualização |

### <a name="cloud-storage-admin-role"></a>Função de administrador de armazenamento em nuvem

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar datastore <br> Operações de arquivos de baixo nível <br> Remover datastore <br> Renomear datastore <br> Atualizar arquivos de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Configuração do > do host** | Configuração de partição de armazenamento |
| **Cluster de datastore** | Configure um cluster de datastore |
| **Armazenamento orientado por perfil** | Atualização de armazenamento orientada a perfil <br> Exibição de armazenamento orientada a perfil |
| **Exibições de armazenamento** | Configurar serviços <br> Visualizar |

### <a name="cloud-network-admin-role"></a>Função de admin de rede de nuvem

| **Categoria** | **Privilégio** |
|----------|-----------|
| **grupo dvPort** | Criar <br> Excluir <br> Modificar <br> Operação de políticas <br> Operação de escopo |
| **Rede** | Assign network <br> Configurar <br> Mover rede <br> Remover |
| **Configuração de > de máquina virtual** | Modificar as configurações de dispositivo |

### <a name="cloud-vm-admin-role"></a>Papel de admin cloud-VM-Admin

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados |
| **Rede** | Assign network |
| **Recurso** | Assign virtual machine to resource pool <br> Migrar desligado da máquina virtual <br> Migrar alimentado na máquina virtual
| **vApp** | Exportação <br> Importar |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover o dispositivo <br> Avançado <br> Alterar a contagem da CPU <br> Alterar recurso <br> Configurar gerenciado <br> Rastreamento de alteração de disco <br> Locação de discos <br> Exibir configurações de conexão <br> Estender o disco virtual <br> Dispositivo USB host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade com tolerância a falhas de consulta <br> Consultar arquivos sem dono <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Configurações <br> Colocação de arquivos de swap <br> Alternar fork pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Máquina virtual >Operações de hóspedes** | Modificação de alias da operação do convidado <br> Consulta de alias de operação de hóspedes <br> Modificações na operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes    |
| **Interação >máquina virtual** | Responder pergunta <br> Operação de backup na máquina virtual <br> Configurar a mídia CD <br> Configurar mídia disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela VIX API <br> Injetar códigos de varredura USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou encolhimento <br> Desligar <br> Ligue <br> Sessão de gravação na máquina virtual <br> Sessão de replay na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Suspender a tolerância à falha <br> Failover de Teste <br> Reinicialização do teste VM secundário <br> Desativar a tolerância à falha <br> Ativar tolerância a falhas <br> Instalação de ferramentas VMware |
| **Inventário de >de máquina virtual** | Criar a partir de existente <br> Criar Novo <br> Mover <br> Registrar  <br> Remover <br> Cancelar o registro  |
| **Provisionamento de >de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir o download de máquinas virtuais <br> Permitir o upload de arquivos de máquinas virtuais <br> Modelo de clone <br> Clonar máquina virtual <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marca como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Leia as especificações de personalização |
| **Configuração do serviço de >de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerenciar configurações de serviço <br> Modificar a configuração do serviço <br> Configurações do serviço de consulta <br> Leia a configuração do serviço
| **Gerenciamento de instantâneos de >de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de >de máquina virtual vSphere** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Dependência de atualização |
