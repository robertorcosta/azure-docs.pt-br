---
title: Solução Azure VMware by CloudSimple - Configure o vCenter na Nuvem Privada para automação vRealize
description: Descreve como configurar um servidor VMware vCenter em sua Cloud Simple Private Cloud como um ponto final para vMware vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024833"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configure o vCenter em sua Nuvem Privada para VMware vRealize Automation

Você pode configurar um servidor VMware vCenter em sua CloudSimple Private Cloud como um ponto final para VMware vRealize Automation.

## <a name="before-you-begin"></a>Antes de começar

Complete essas tarefas antes de configurar o servidor vCenter:

* Configure uma [conexão VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre seu ambiente local e sua Nuvem Privada.
* [Configure o encaminhamento de DNS de solicitações de DNS no local](on-premises-dns-setup.md) para os servidores DNS para sua Nuvem Privada.
* Envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para criar um usuário administrativo vRealize Automation IaaS com o conjunto de permissões listadas na tabela a seguir.

| Valor do atributo | Permissão |
------------ | ------------- |  
| Repositório de dados |  Alocar espaço <br> Procurar datastore |
| Cluster de armazenamento de dados | Configure um cluster de datastore |
| Pasta | Criar pasta <br>Excluir Pasta |
| Global |  Gerenciar atributos personalizados<br>Definir atributo personalizado |
| Rede | Rede De atribuição |
| Permissões | Modificar permissões |
| Recurso | Atribuir VM ao pool de recursos<br>Migrar powered off máquina virtual<br>Migrar alimentado na máquina virtual |
| Inventário de máquinas virtuais |  Criar a partir de existente<br>Criar Novo<br>Mover<br>Remover | 
| Interação com máquinas virtuais |  Configurar a mídia cd<br>Interação do console<br>Conexão de Dispositivo<br>Desligar a energia<br>Energia ligado<br>Redefinir<br>Suspend<br>Instalação de ferramentas | 
| Configuração de máquina virtual |  Adicionar disco existente<br>Adicionar novo disco<br>Adicionar ou Remover<br>Remover disco<br>Avançado<br>Alterar contagem de CPU<br>Alterar recurso<br>Estender o disco virtual<br>Rastreamento de alteração de disco<br>Memória<br>Modificar as configurações do dispositivo<br>Renomear<br>Definir anotação (versão 5.0 e posterior)<br>Configurações<br>Colocação de arquivo de swap |
| Provisionamento |  Personalizar<br>Modelo de clone<br>Máquina virtual clone<br>Implantar o modelo<br>Leia as especificações de personalização |
| Estado da Máquina Virtual | Criar instantâneo<br>Remover instantâneo<br>Reverter para Snapshot |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instale a automação vRealize em seu ambiente local

1. Faça login no servidor vRealize Automation IaaS como o administrador iaaS que o CloudSimple Support criou para você.
2. Implante um agente vSphere para o ponto final da automação vRealize.
    1. Vá para https://*vra-url*:5480/installer, onde *vra-url* é a URL que você usa para acessar a ui de administração de automação vRealize.
    2. Clique no **Instalador IaaS** para baixar o instalador.<br>
    A convenção de nomeação para o arquivo instalador é setup_*vra-url*@5480.exe.
    3. Execute o instalador. Na tela de Boas-Vindas, clique em **Avançar**.
    4. Aceite o EULA e clique **em Next**.
    5. Forneça as informações de login, clique em **Aceitar Certificado**e clique em **Avançar**.
    ![credenciais vRA](media/configure-vra-endpoint-login.png)
    6. Selecione **Agentes de instalação** e proxy **personalizados** e clique **em Next**.
    ![tipo de instalação vRA](media/configure-vra-endpoint-install-type.png)
    7. Digite as informações de login do servidor IaaS e clique em **Next**. Se estiver usando o Active Directory, digite o nome de usuário no formato **domain\user.** Caso contrário, **user@domain** use o formato.
    ![informações de login do vRA](media/configure-vra-endpoint-account.png)
    8. Para as configurações de proxy, digite **vSphere** para **o tipo Agent**. Insira um nome para o agente.
    9. Insira o FQDN do servidor IaaS nos campos **Host de serviço** do gerente e do host de serviço web do **gerenciador de** modelos. Clique **em Testar** a conexão de cada um dos valores de FQDN. Se o teste falhar, modifique as configurações do DNS para que o nome de host do servidor IaaS seja resolvido.
    10. Digite um nome para o ponto final do servidor vCenter para a Nuvem Privada. Registre o nome para uso posteriormente no processo de configuração.

        ![proxy de instalação de vRA](media/configure-vra-endpoint-proxy.png)

    11. Clique em **Avançar**.
    12. Clique em **Instalar**.

## <a name="configure-the-vsphere-agent"></a>Configure o agente vSphere

1. Vá para https://*vra-url*/vcac e faça login como **ConfigurationAdmin**.
2. Selecione**Pontos finais de** >  **infra-estrutura** > .**Endpoints**
3. Selecione **Novo** > **vSphere****Virtual** > .
4. Digite o nome do ponto final do vSphere que você especificou no procedimento anterior.
5. Para **Endereço,** insira a URL do Servidor Private Cloud vCenter no formato https://*vcenter-fqdn*/sdk, onde *vcenter-fqdn* é o nome do servidor vCenter.
6. Digite as credenciais para o usuário administrativo vRealize Automation IaaS que o CloudSimple Support criou para você.
7. Clique **em Conexão de teste** para validar as credenciais do usuário. Se o teste falhar, verifique a URL, as informações da conta e [o nome do ponto final](#verify-the-endpoint-name) e teste novamente.
8. Após um teste bem-sucedido, clique em **OK** para criar o ponto final do vSphere.
    ![acesso de configuração de ponto final vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verifique o nome do ponto final

Para identificar o nome de ponto final do servidor vCenter correto, faça o seguinte:

1. Abra um prompt de comando no aparelho IaaS.
2. Alterar diretório para C:\Arquivos de programa (x86)\VMware\vCAC\Agents\agent\agent-name, onde *nome do agente* é o nome atribuído ao ponto final do servidor vCenter.
3. Execute o comando a seguir.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A saída é semelhante à seguinte. O valor `managementEndpointName` do campo é o nome do ponto final.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
