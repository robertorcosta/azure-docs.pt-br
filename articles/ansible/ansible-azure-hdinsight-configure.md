---
title: Tutorial – Configurar um cluster no Azure HDInsight usando o Ansible
description: Saiba como usar o Ansible para criar e redimensionar um Azure HDInsight
keywords: ansible, azure, devops, bash, guia estratégico, apache hadoop, hdinsight
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ad17e6ff4cbf5b583e3a4be410847c1349c9edce
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241943"
---
# <a name="tutorial-configure-a-cluster-in-azure-hdinsight-using-ansible"></a>Tutorial: Configurar um cluster no Azure HDInsight usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

O [Azure HDInsight](/azure/hdinsight/) é um serviço de análise com base no Hadoop para processamento de dados. O HDInsight é uma ferramenta ETL (extrair, transformar, carregar) usada para trabalhar com Big Data, sejam estruturados ou não. O HDInsight dá suporte a vários [tipos de clusters](/azure/hdinsight/hadoop/apache-hadoop-introduction), em que cada tipo tem suporte para um conjunto diferente de componentes. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma conta de armazenamento para o HDInsight
> * Configure um cluster do [HDInsight Spark](/azure/hdinsight/spark/apache-spark-overview).
> * Redimensionar um cluster
> * Excluir um cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-random-postfix"></a>Criar um sufixo aleatório

O código do guia estratégico nessa seção cria um sufixo aleatório para usar como parte do nome do cluster do Azure HDInsight.

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"  
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O código do guia estratégico nesta seção cria um grupo de recursos.


```yml
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

## <a name="create-a-storage-account-and-retrieve-key"></a>Criar uma conta de armazenamento e recuperar a chave

Uma conta de armazenamento do Azure é usada como o armazenamento padrão do cluster HDInsight. 

O código do guia estratégico nesta seção recupera a chave usada para acessar a conta de armazenamento.

```yml
- name: Create storage account
  azure_rm_storageaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ storage_account_name }}"
      account_type: Standard_LRS
      location: eastus2

- name: Get storage account keys
  azure_rm_resource:
    api_version: '2018-07-01'
    method: POST
    resource_group: "{{ resource_group }}"
    provider: storage
    resource_type: storageaccounts
    resource_name: "{{ storage_account_name }}"
    subresource:
      - type: listkeys
  register: storage_output

- debug:
    var: storage_output
```

## <a name="create-an-hdinsight-spark-cluster"></a>Criar um cluster Spark no HDInsight

O código do guia estratégico nesta seção cria o cluster do Azure HDInsight.

```yml
- name: Create instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
```

A criação da instância pode levar vários minutos para ser concluída.

## <a name="resize-the-cluster"></a>Redimensionar o cluster

Após a criação do cluster, a única configuração que você pode alterar é o número de nós de trabalho. 

O código do guia estratégico nessa seção incrementa o número de nós de trabalho atualizando `target_instance_count` em `workernode`.

```yml
- name: Resize cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 2
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
    tags:
      aaa: bbb
  register: output
```

## <a name="delete-the-cluster-instance"></a>Excluir a instância do cluster

A cobrança de clusters HDInsight é proporcional por minuto. 

O código do guia estratégico nesta seção exclui o cluster.

```yml
- name: Delete instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    state: absent
```

## <a name="get-the-sample-playbook"></a>Obter o guia estratégico de exemplo

Há duas maneiras de obter o guia estratégico de exemplo completo:
- [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/hdinsight_create.yml) e salve-o em `hdinsight_create.yml`.
- Crie um novo arquivo chamado `hdinsight_create.yml` e copie-o para o conteúdo a seguir:

```yml
---
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus2
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cluster_name: mycluster{{ rpfx }}
    storage_account_name: mystorage{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create storage account
      azure_rm_storageaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ storage_account_name }}"
        account_type: Standard_LRS
        location: "{{ location }}"

    - name: Get storage account keys
      azure_rm_resource:
        api_version: '2018-07-01'
        method: POST
        resource_group: "{{ resource_group }}"
        provider: storage
        resource_type: storageaccounts
        resource_name: "{{ storage_account_name }}"
        subresource:
          - type: listkeys
      register: storage_output
    - debug:
        var: storage_output

    - name: Create instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net" 
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123

    - name: Resize cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net"
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 2
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
        tags:
          aaa: bbb
      register: output
    - debug:
        var: output

    - name: Assert the state has changed
      assert:
        that:
          - output.changed

    - name: Delete instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

Nesta seção, execute o guia estratégico para testar diversos recursos mostrados neste artigo.

Antes de executar o guia estratégico, faça as seguintes alterações:
- Na seção `vars`, substitua o espaço reservado `{{ resource_group_name }}` pelo nome do grupo de recursos.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook hdinsight.yml
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