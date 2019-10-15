---
title: Tutorial – Gerenciar o tráfego da Web com o Gateway de Aplicativo do Azure usando o Ansible
description: Saiba como usar o Ansible para criar e configurar um Gateway de Aplicativo do Azure para gerenciar o tráfego da Web
keywords: ansible, azure, devops, bash, guia estratégico, gateway de aplicativo, balanceador de carga, tráfego da Web
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241881"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Tutorial: Gerenciar o tráfego da Web com o Gateway de Aplicativo do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

O [Gateway de Aplicativo do Azure](/azure/application-gateway/overview) é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. Com base no endereço IP e na porta de origem, os balanceadores de carga tradicionais roteiam o tráfego para um endereço IP e uma porta de destino. O Gateway de Aplicativo oferece um nível mais refinado de controle em que o tráfego pode ser roteado com base na URL. Por exemplo, você poderia definir que, se `images` for o caminho da URL, o tráfego será roteado para um conjunto específico de servidores (conhecido como pool) configurado para imagens.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar uma rede
> * Criar duas instâncias de contêiner do Azure com imagem HTTPD
> * Criar um gateway de aplicativo que funciona com as instâncias de contêiner do Azure no pool de servidores

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O código do guia estratégico nesta seção cria um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são configurados.  

Salve o guia estratégico a seguir como `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Antes de executar o guia estratégico, confira as observações a seguir:

- O nome do grupo de recursos é `myResourceGroup`. Esse valor é usado em todo o tutorial.
- O grupo de recursos é criado na localização `eastus`.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Criar recursos da rede

O código do guia estratégico nesta seção cria uma rede virtual para permitir que o gateway de aplicativo se comunique com outros recursos.

Salve o guia estratégico a seguir como `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Antes de executar o guia estratégico, confira as observações a seguir:

* A seção `vars` contém os valores que são usados para criar os recursos de rede. 
* Você precisará alterar esses valores para seu ambiente específico.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Criar servidores

O código do guia estratégico nesta seção cria duas instâncias de contêiner do Azure com imagens HTTPD para serem usadas como servidores da Web para o gateway do aplicativo.  

Salve o guia estratégico a seguir como `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

O código do guia estratégico nesta seção cria um gateway de aplicativo chamado `myAppGateway`.  

Salve o guia estratégico a seguir como `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Antes de executar o guia estratégico, confira as observações a seguir:

* `appGatewayIP` é definido no bloco `gateway_ip_configurations`. Uma referência de sub-rede é necessária para a configuração de IP do gateway.
* `appGatewayBackendPool` é definido no bloco `backend_address_pools`. Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
* `appGatewayBackendHttpSettings` é definido no bloco `backend_http_settings_collection`. Ele especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
* `appGatewayHttpListener` é definido no bloco `backend_http_settings_collection`. É o ouvinte padrão associado a appGatewayBackendPool.
* `appGatewayFrontendIP` é definido no bloco `frontend_ip_configurations`. Ele atribui myAGPublicIPAddress a appGatewayHttpListener.
* `rule1` é definido no bloco `request_routing_rules`. É a regra padrão de roteamento associada a appGatewayHttpListener.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook appgw_create.yml
```

O gateway de aplicativo pode demorar vários minutos para ser criado.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Na seção [Criar um grupo de recursos](#create-a-resource-group), especifique um local. Observe seu valor.

1. Na seção [Criar recursos de rede](#create-network-resources), especifique o domínio. Observe seu valor.

1. Para a URL de teste, substituindo o padrão a seguir pelo local e domínio: `http://<domain>.<location>.cloudapp.azure.com`.

1. Navegue até a URL de teste.

1. Se você vir a página a seguir, o gateway de aplicativo estará funcionando conforme o esperado.

    ![Teste bem-sucedido de um gateway de aplicativo em funcionamento](media/ansible-application-gateway-configure/application-gateway.png)

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
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)