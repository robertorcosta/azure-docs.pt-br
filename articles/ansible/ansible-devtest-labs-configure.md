---
title: Tutorial – Configurar laboratórios no Azure DevTest Labs usando o Ansible
description: Saiba como configurar um laboratório no Azure DevTest Labs usando o Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, devtest labs
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d035c76a811df45af5ed8183b86e14a2ee6218b7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241667"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Tutorial: Configurar laboratórios no Azure DevTest Labs usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[O Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) permite aos desenvolvedores automatizar a criação de ambientes de VM para seus aplicativos. Esses ambientes podem ser configurados para o desenvolvimento, teste e treinamento do aplicativo. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um laboratório
> * Definir as políticas do laboratório
> * Definir os agendamentos do laboratório
> * Criar a rede virtual do laboratório
> * Definir uma origem do artefato para o laboratório
> * Criar uma VM no laboratório
> * Listar os artefatos e as origens dos artefatos do laboratório
> * Obter informações do Azure Resource Manager para as origens do artefato
> * Criar um ambiente de laboratório
> * Criar a imagem do laboratório
> * Excluir o laboratório

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Criar grupo de recursos

O snippet do guia estratégico de exemplo cria um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Criar o laboratório

A próxima tarefa cria o laboratório de exemplo.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Definir as políticas do laboratório

Você pode definir as configurações da política do laboratório. Os seguintes valores podem ser definidos:

- `user_owned_lab_vm_count` é o número de VMs que um usuário pode ter
- `user_owned_lab_premium_vm_count` é o número de VMs que um usuário pode possuir
- `lab_vm_count` é o número máximo de VMs de laboratório
- `lab_premium_vm_count` é o número máximo de VMs premium de laboratório
- `lab_vm_size` é o tamanho permitido das VMs de laboratório
- `gallery_image` é a imagem permitida da galeria
- `user_owned_lab_vm_count_in_subnet` é o número máximo de VMs do usuário em uma sub-rede
- `lab_target_cost` é o custo de destino do laboratório

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Definir os agendamentos do laboratório

A tarefa de exemplo nesta seção configura o agendamento do laboratório. 

No snippet de código a seguir, o valor `lab_vms_startup` é usado para especificar o tempo de inicialização da VM. Da mesma forma, definir o valor `lab_vms_shutdown` estabelece o tempo de desligamento da VM do laboratório.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Criar a rede virtual do laboratório

Essa tarefa a seguir cria a rede virtual do laboratório padrão.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Definir uma origem do artefato para o laboratório

Uma origem de artefatos é um repositório do GitHub adequadamente estruturado que contém a definição dos artefatos e os modelos do Azure Resource Manager. Todos os laboratórios contam com artefatos públicos predefinidos. As tarefas a seguir mostram como criar uma origem de artefato para um laboratório.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Criar uma VM no laboratório

Crie uma VM no laboratório.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Listar os artefatos e as origens dos artefatos do laboratório

Para listar todas as origens de artefatos padrão e personalizadas no laboratório, use a tarefa a seguir:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

A tarefa a seguir lista todos os artefatos:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Obter informações do Azure Resource Manager para as origens do artefato

Para listar todos os modelos do Azure Resource Manager em `public environment repository`, o repositório predefinido com modelos:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

E a tarefa a seguir recupera os detalhes de um modelo específico do Azure Resource Manager do repositório:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Criar um ambiente de laboratório

A tarefa a seguir cria o ambiente de laboratório com base em um dos modelos do repositório de ambiente público.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Criar a imagem do laboratório

A tarefa a seguir cria uma imagem a partir de uma VM. A imagem permite criar VMs idênticas.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Excluir o laboratório

Para excluir o laboratório, use a seguinte tarefa:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Obter o guia estratégico de exemplo

Há duas maneiras de obter o guia estratégico de exemplo completo:
- [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) e salve-o em `devtestlab-create.yml`.
- Crie um novo arquivo chamado `devtestlab-create.yml` e copie-o para o conteúdo a seguir:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Executar o guia estratégico

Nesta seção, execute o guia estratégico para testar diversos recursos mostrados neste artigo.

Antes de executar o guia estratégico, faça as seguintes alterações:
- Na seção `vars`, substitua o espaço reservado `{{ resource_group_name }}` pelo nome do grupo de recursos.
- Armazene o token do GitHub como uma variável de ambiente chamada `GITHUB_ACCESS_TOKEN`.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. 

Salve o seguinte código como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)