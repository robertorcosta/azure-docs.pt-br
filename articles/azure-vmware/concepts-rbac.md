---
title: Conceitos – RBAC (controle de acesso baseado em função)
description: Saiba mais sobre os principais recursos do controle de acesso baseado em função para a solução do Azure VMware
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 6b9e83f0442953f59d02c42514a8550301ea947b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752259"
---
# <a name="role-based-access-control-rbac-for-azure-vmware-solution"></a>RBAC (controle de acesso baseado em função) para a solução VMware do Azure

Em uma implantação local do vCenter e ESXi, o administrador tem acesso à conta do vCenter administrator@vsphere.local e pode ter usuários/grupos adicionais de Active Directory (AD) atribuídos. No entanto, em uma implantação de solução do Azure VMware, o administrador não tem acesso à conta de usuário administrador, mas pode atribuir usuários e grupos do AD à função CloudAdmin no vCenter.  Além disso, o usuário de nuvem privada da solução Azure VMware não tem permissão para acessar ou configurar componentes de gerenciamento específicos com suporte e gerenciados pela Microsoft, como clusters, hosts, repositórios de armazenamento e comutadores virtuais distribuídos.


Na solução Azure VMware, o vCenter tem um usuário local interno chamado cloudadmin que é atribuído à função CloudAdmin interna. O usuário cloudadmin local é usado para configurar usuários adicionais no AD. A função CloudAdmin, em geral, tem o privilégio para criar e gerenciar cargas de trabalho em sua nuvem privada (máquinas virtuais, pools de recursos, repositórios de armazenamento e redes). A função CloudAdmin na solução VMware do Azure tem um conjunto específico de privilégios vCenter que diferem de outras soluções de nuvem VMware.   

> [!NOTE]
> Atualmente, a solução Azure VMware não oferece funções personalizadas no vCenter ou no portal de solução do Azure VMware. 

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Função CloudAdmin da solução VMware do Azure no vCenter

Você pode exibir os privilégios concedidos à função CloudAdmin da solução VMware do Azure em sua nuvem privada da solução Azure VMware vCenter.

1. Faça logon no cliente SDDC vSphere e vá para o **menu**  >  **Administração**.
1. Em **controle de acesso**, selecione **funções**.
1. Na lista de funções, selecione **CloudAdmin** e, em seguida, selecione **privilégios**. 

   :::image type="content" source="media/rbac-cloudadmin-role-privileges.png" alt-text="Como exibir os privilégios da função CloudAdmin no cliente vSphere":::

A função CloudAdmin na solução VMware do Azure tem os seguintes privilégios no vCenter. Consulte a [documentação do produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) para obter uma explicação detalhada de cada privilégio.

| Privilege | Descrição |
| --------- | ----------- |
| **Alarmes** | Reconhecer alerta<br />Criar alarme<br />Desabilitar ação de alarme<br />Modificar alarme<br />Remover alarme<br />Definir status do alarme |
| **Permissões** | Modificar permissões |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca<br />Criar uma assinatura para uma biblioteca publicada<br />Criar biblioteca local<br />Criar biblioteca inscrita<br />Excluir item de biblioteca<br />Excluir biblioteca local<br />Excluir biblioteca assinada<br />Excluir a assinatura de uma biblioteca publicada<br />Baixar arquivos<br />Remover itens de biblioteca<br />Remover biblioteca assinada<br />Importar armazenamento<br />Informações de assinatura de investigação<br />Publicar um item de biblioteca em seus assinantes<br />Publicar uma biblioteca em seus assinantes<br />Armazenamento de leitura<br />Sincronizar item de biblioteca<br />Sincronizar biblioteca assinada<br />Introspecção de tipo<br />Atualizar definições de configuração<br />Arquivos de atualização<br />Atualizar biblioteca<br />Atualizar item de biblioteca<br />Atualizar biblioteca local<br />Atualizar biblioteca assinada<br />Atualizar a assinatura de uma biblioteca publicada<br />Exibir definições de configuração |
| **Operações criptográficas** | Acesso direto |
| **Datastore** | Alocar espaço<br />Procurar no Repositório de Dados<br />Configurar repositório de armazenamento<br />Operações de arquivo de nível baixo<br />Remover arquivos<br />Atualizar metadados da máquina virtual |
| **Pasta** | Criar pasta<br />Excluir pasta<br />Mover pasta<br />Renomear pasta |
| **Geral** | Cancelar tarefa<br />Marca global<br />Integridade<br />Evento de log<br />Gerenciar atributos personalizados<br />Gerenciadores de serviços<br />Definir atributo personalizado<br />Marca do sistema |
| **Host** | Replicação do vSphere<br />&#160;&#160;&#160;&#160;gerenciar a replicação |
| **marcação de vSphere** | Atribuir e cancelar a atribuição de marca vSphere<br />Criar marca vSphere<br />Criar categoria de marca vSphere<br />Excluir marca vSphere<br />Excluir categoria de marca vSphere<br />Editar marca vSphere<br />Editar categoria da marca vSphere<br />Modificar o campo UsedBy para a categoria<br />Modificar campo UsedBy para marca |
| **Rede** | Assign network |
| **Recurso** | Aplicar recomendação<br />Atribuir vApp ao pool de recursos<br />Assign virtual machine to resource pool<br />Criar pool de recursos<br />Migrar máquina virtual desligada<br />Migrar máquina virtual ligada<br />Modificar pool de recursos<br />Mover pool de recursos<br />Consultar o vMotion<br />Remover pool de recursos<br />Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefa<br />Modificar tarefa<br />Remover tarefa<br />Executar tarefa |
| **Sessões** | Mensagem<br />Validar sessão |
| **Perfil** | Exibição de armazenamento controlada por perfil |
| **Exibição de armazenamento** | Visualizar |
| **vApp** | Adicionar máquina virtual<br />Atribuir pool de recursos<br />Atribuir vApp<br />Clone<br />Criar<br />Excluir<br />Exportação<br />Importar<br />Mover<br />Desligar<br />Ligue<br />Renomear<br />Suspend<br />Cancelar o registro <br />Exibir ambiente OVF<br />configuração do aplicativo vApp<br />configuração da instância de vApp<br />configuração do vApp managedBy<br />configuração do recurso vApp |
| **Máquina virtual** | Alterar Configuração<br />&#160;&#160;&#160;&#160;adquirir concessão de disco<br />&#160;&#160;&#160;&#160;adicionar disco existente<br />&#160;&#160;&#160;&#160;adicionar novo disco<br />&#160;&#160;&#160;&#160;adicionar ou remover dispositivo<br />Configuração avançada do &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;alterar contagem de CPU<br />&#160;&#160;&#160;&#160;alterar a memória<br />&#160;&#160;&#160;&#160;alterar configurações<br />&#160;&#160;&#160;&#160;alterar o posicionamento do Swapfile<br />&#160;&#160;&#160;&#160;alterar recurso<br />&#160;&#160;&#160;&#160;configurar o dispositivo USB do host<br />&#160;&#160;&#160;&#160;configurar o dispositivo bruto<br />&#160;&#160;&#160;&#160;configurar o managedBy<br />&#160;&#160;&#160;&#160;exibir configurações de conexão<br />Estender o disco virtual &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificar as configurações do dispositivo<br />Compatibilidade de tolerância a falhas de consulta &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;consultar arquivos sem proprietário<br />Recarregar &#160;&#160;&#160;&#160;de caminhos<br />&#160;&#160;&#160;&#160;remover disco<br />Renomear &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;redefinir informações de convidado<br />&#160;&#160;&#160;&#160;definir anotação<br />&#160;&#160;&#160;&#160;alternar o controle de alterações do disco<br />&#160;&#160;&#160;&#160;alternar bifurcação pai<br />&#160;&#160;&#160;&#160;atualizar a compatibilidade da máquina virtual<br />Editar inventário<br />&#160;&#160;&#160;&#160;criar a partir de existente<br />&#160;&#160;&#160;&#160;criar novo<br />&#160;&#160;&#160;&#160;mover<br />Registro de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;remover<br />&#160;&#160;&#160;&#160;cancelar o registro<br />Operações de convidado<br />Modificação de alias de operação de convidado &#160;&#160;&#160;&#160;<br />Consulta de alias de operação de convidado &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificações de operação de convidado<br />&#160;&#160;&#160;&#160;execução do programa de operação de convidado<br />&#160;&#160;&#160;&#160;consultas de operação de convidado<br />Interação<br />&#160;&#160;&#160;&#160;pergunta de resposta<br />&#160;&#160;&#160;&#160;operação de backup na máquina virtual<br />&#160;&#160;&#160;&#160;configurar mídia de CD<br />&#160;&#160;&#160;&#160;configurar mídia de disquete<br />Dispositivos &#160;&#160;&#160;&#160;Connect<br />Interação do console do &#160;&#160;&#160;&#160;<br />Criar captura de tela &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desfragmentar todos os discos<br />Arrastar e soltar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;o gerenciamento do sistema operacional convidado pela API do VIX<br />&#160;&#160;&#160;&#160;injetar códigos de verificação de HID USB<br />&#160;&#160;&#160;&#160;instalar as ferramentas do VMware<br />&#160;&#160;&#160;&#160;pausar ou pausar<br />&#160;&#160;&#160;&#160;executar operações de apagamento ou redução<br />&#160;&#160;&#160;&#160;desligar<br />Ligar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;sessão de registro na máquina virtual<br />&#160;&#160;&#160;&#160;a sessão de reprodução na máquina virtual<br />&#160;&#160;&#160;&#160;suspender<br />Tolerância a falhas de suspensão &#160;&#160;&#160;&#160;<br />Failover de teste do &#160;&#160;&#160;&#160;<br />VM secundária de reinício de teste de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desativar a tolerância a falhas<br />&#160;&#160;&#160;&#160;ativar a tolerância a falhas<br />Provisionamento<br />&#160;&#160;&#160;&#160;permitir acesso ao disco<br />&#160;&#160;&#160;&#160;permitir o acesso ao arquivo<br />&#160;&#160;&#160;&#160;permitir acesso a disco somente leitura<br />&#160;&#160;&#160;&#160;permitir download de máquina virtual<br />&#160;&#160;&#160;&#160;o modelo de clonagem<br />Clonar &#160;&#160;&#160;&#160;máquina virtual<br />&#160;&#160;&#160;&#160;criar modelo a partir da máquina virtual<br />&#160;&#160;&#160;&#160;personalizar o convidado<br />&#160;&#160;&#160;&#160;implantar modelo<br />&#160;&#160;&#160;&#160;marcar como modelo<br />&#160;&#160;&#160;&#160;modificar especificação de personalização<br />&#160;&#160;&#160;&#160;promover discos<br />&#160;&#160;&#160;&#160;ler especificações de personalização<br />Configuração de serviço<br />&#160;&#160;&#160;&#160;permitir notificações<br />&#160;&#160;&#160;&#160;permitir sondagem de notificações de eventos globais<br />&#160;&#160;&#160;&#160;gerenciar a configuração de serviço<br />&#160;&#160;&#160;&#160;modificar a configuração de serviço<br />&#160;&#160;&#160;&#160;configurações do serviço de consulta<br />&#160;&#160;&#160;&#160;configuração do serviço de leitura<br />Gerenciamento de instantâneo<br />&#160;&#160;&#160;&#160;criar instantâneo<br />&#160;&#160;&#160;&#160;remover instantâneo<br />Renomear instantâneo &#160;&#160;&#160;&#160;<br />Reverta o instantâneo &#160;&#160;&#160;&#160;<br />Replicação do vSphere<br />&#160;&#160;&#160;&#160;configurar a replicação<br />&#160;&#160;&#160;&#160;gerenciar a replicação<br />&#160;&#160;&#160;&#160;monitorar a replicação |
| **vService** | Criar dependência<br />Destruir dependência<br />Reconfigurar a configuração de dependência<br />Atualizar dependência |



## <a name="next-steps"></a>Próximas etapas

Consulte a [documentação do produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) para obter uma explicação detalhada de cada privilégio.

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

