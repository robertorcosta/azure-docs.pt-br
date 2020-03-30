---
title: Quickstart - Crie um VMM Azure VMware em uma nuvem privada - Solução Azure VMware pela CloudSimple
description: Descreve como criar um VMWare Azure Em uma Nuvem Privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566141"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Crie máquinas virtuais VMware em sua Nuvem Privada

Para criar máquinas virtuais em sua Nuvem Privada, comece acessando o portal CloudSimple a partir do portal Azure.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual deseja criar sua Nuvem Privada.
4. Na página **Visão Geral,** clique **em Ir ao portal CloudSimple** para abrir uma nova guia do navegador para o portal CloudSimple.  Se solicitado, faça login com suas credenciais do Azure.  

    ![Inicie o portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Iniciar web-ui do vCenter

Agora você pode lançar o vCenter para configurar máquinas e políticas virtuais.

Para acessar o vCenter, comece pelo portal CloudSimple. Na página inicial, em **Tarefas Comuns,** clique em **Iniciar vSphere Client**.  Selecione a Nuvem Privada e clique em **Iniciar o cliente vSphere** na Nuvem Privada.

   ![Inicie o cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Faça upload de um modelo ISO ou vSphere

  > [!WARNING]
  > Para upload iso, use o cliente vSphere HTML5.  O uso de um cliente Flash pode resultar em um erro.

1. Obtenha o modelo ISO ou vSphere que você deseja carregar para o vCenter para criar uma VM e tê-la disponível em seu sistema local.
2. No vCenter, clique no ícone **Disk** e selecione **vsanDatastore**. Clique **em Arquivos** e clique em Nova **pasta**.
    ![ISO do vCenter](media/vciso00.png)

3. Crie uma pasta intitulada 'ISOs e Modelos'.

4. Navegue até a pasta ISOs em ISOs e Modelos e clique **em 'Enviar arquivos '** Siga as instruções na tela para carregar o ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Crie uma máquina virtual no vCenter

1. No vCenter, clique no ícone **Hosts e Clusters.**

2. Clique com o botão direito do mouse **na carga de trabalho** e selecione Nova máquina **virtual**.
    ![Nova VM](media/vcvm01.png)

3. Selecione **Criar nova máquina virtual** e clique em **Next**.
    ![Nova VM](media/vcvm02.png)

4. Nomeie a máquina, selecione a localização **da VM workload** e clique **em Next**.
    ![Nova VM](media/vcvm03.png)

5. Selecione o recurso de cálculo **de carga de trabalho** e clique em **Next**.
    ![Nova VM](media/vcvm04.png)

6. Selecione **vsanDatastore** e clique **em Next**.
    ![Nova VM](media/vcvm05.png)

7. Mantenha a seleção padrão de compatibilidade ESXi 6.5 e clique **em Next**.
    ![Nova VM](media/vcvm06.png)

8. Selecione o sistema operacional convidado da ISO para a VM que você está criando e clique **em Next**.
    ![Nova VM](media/vcvm07.png)

9. Selecione opções de disco rígido e rede. Para nova unidade de CD/DVD, selecione **arquivo ISO do Datastore**.  Se você quiser permitir o tráfego do endereço IP público para esta VM, selecione a rede como **vm-1**.
    ![Nova VM](media/vcvm08.png)

10. Uma janela de seleção se abre. Selecione o arquivo que você carregou anteriormente na pasta ISOs e Modelos e clique em **OK**.
    ![Nova VM](media/vcvm10.png)

11. Revise as configurações e clique em **OK** para criar a VM.
    ![Nova VM](media/vcvm11.png)

A VM agora é adicionada aos recursos de computação da Carga de Trabalho e está pronta para uso. 
![Nova VM](media/vcvm12.png)

A configuração básica está concluída. Você pode começar a usar sua Nuvem Privada semelhante à forma como você usaria sua infra-estrutura VM no local.

As seções a seguir contêm informações opcionais sobre a configuração de servidores DNS e DHCP para cargas de trabalho privadas na Nuvem e a modificação da configuração padrão de rede.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicionar usuários e fontes de identidade ao vCenter (Opcional)

O CloudSimple atribui uma conta de `cloudowner@cloudsimple.local`usuário padrão do vCenter com o nome de usuário . Nenhuma configuração adicional da conta é necessária para você começar.  O CloudSimple normalmente atribui aos administradores os privilégios necessários para executar operações normais.  Configure seu diretório ativo no local ou o Azure AD como uma [fonte de identidade adicional](set-vcenter-identity.md) em sua Nuvem Privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DNS e DHCP (Opcional)

Aplicativos e cargas de trabalho em execução em um ambiente de Nuvem Privada exigem resolução de nomes e serviços DHCP para pesquisa e atribuição de endereçoIP. Uma infra-estrutura DHCP e DNS adequada é necessária para fornecer esses serviços. Você pode configurar uma máquina virtual no vCenter para fornecer esses serviços em seu ambiente Private Cloud.

Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado

* Configuração de rota para servidores DNS no local ou na Internet

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

Os links a seguir fornecem orientações sobre a configuração de servidores DHCP e DNS no Linux e Windows.

#### <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseado em Linux

O Linux oferece vários pacotes para configurar servidores DNS.  Aqui está um link para instruções para configurar um servidor BIND De código aberto.

[Configuração de exemplo](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Configuração baseada no Windows

Esses tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

[Servidor Windows como Servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Servidor Windows como Servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar a configuração de rede (Opcional)

As páginas de Rede no portal CloudSimple permitem especificar a configuração para tabelas de firewall e endereços IP públicos para VMs.

### <a name="allocate-public-ips"></a>Alocar IPs públicos

1. Navegue até **a Rede > IP Público** no portal CloudSimple.
2. Clique **em Alocar IP público**.
3. Digite um nome para identificar a entrada do endereço IP.
4. Mantenha o local padrão.
5. Use o controle deslizante para alterar o tempo de inatividade, se desejar.
6. Digite o endereço IP local para o qual você deseja atribuir um endereço IP público.
7. Digite um nome DNS associado, se desejar.
8. Clique em **Concluído**.

    ![IP público](media/quick-create-pc-public-ip.png)

A tarefa de alocar o endereço IP público começa. Você pode verificar o status da tarefa na página **Atividade > Tarefas.** Quando a alocação estiver concluída, a nova entrada será mostrada na página de IPs públicos.

A VM para a qual este endereço IP deve ser mapeado precisa ser configurada com o endereço local especificado acima. O procedimento para configurar um endereço IP é específico para o sistema operacional VM. Consulte a documentação do seu sistema operacional VM para o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes para o Ubuntu 16.04.

Adicione o método estático à configuração da família de endereços inet no arquivo /etc/rede/interfaces. Altere os valores de endereço, netmask e gateway. Para este exemplo, estamos usando a interface eth0, endereço IP interno 192.168.24.10, endereço gateway 192.168.24.1 e netmask 255.255.255.0. Para o seu ambiente, as informações disponíveis da sub-rede são fornecidas no e-mail de boas-vindas.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Desative manualmente a interface.

```
sudo ifdown eth0
```
Habilite manualmente a interface novamente.

```
sudo ifup eth0
```

Por padrão, todo o tráfego de entrada da Internet é **negado**. Se você quiser abrir qualquer outra porta, crie uma [tabela de firewall](firewall.md).

Depois de configurar um endereço IP interno como endereço IP estático, verifique se você pode chegar à Internet a partir da VM.

```
ping 8.8.8.8
```
Verifique também se você pode acessar a VM da Internet usando o endereço IP público.

Certifique-se de que quaisquer regras iptable na VM não estão bloqueando a porta 80 de entrada.
        
```
netstat -an | grep 80
```

Inicie um servidor http que ouça na porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

ou

```
python3 -m http.server 80
```
Inicie um navegador na sua área de trabalho e aponte-o para a porta 80 para que o endereço IP público navegue pelos arquivos em sua VM.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras padrão de firewall do CloudSimple para IP público

* Tráfego VPN: Todo o tráfego entre (de/para) a VPN e todas as redes de carga de trabalho e rede de gerenciamento é permitido.
* Tráfego interno em nuvem privada: Todo o tráfego leste-oeste entre (de/para) redes de carga de trabalho e a rede de gerenciamento (mostrado acima) é permitido.
* Tráfego na Internet:
  * Todo o tráfego de entrada da Internet é negado às redes de carga de trabalho e à rede de gerenciamento.
  * Todo o tráfego de saída para a Internet a partir de redes de carga de trabalho ou da rede de gerenciamento é permitido.

Você também pode modificar a forma como seu tráfego é protegido, usando o recurso Regras de Firewall. Para obter mais informações, consulte [Configurar tabelas e regras de firewall](firewall.md).

## <a name="install-solutions-optional"></a>Instalar soluções (Opcional)

Você pode instalar soluções em seu CloudSimple Private Cloud para aproveitar ao máximo o seu ambiente private cloud vCenter. Você pode configurar backup, recuperação de desastres, replicação e outras funções para proteger suas máquinas virtuais. Exemplos incluem o VMware Site Recovery Manager (VMware SRM) e o Veeam Backup & Replication.

Para instalar uma solução, você deve solicitar privilégios adicionais por um período limitado. Veja [Escalar privilégios](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conecte-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configure gateways VPN na rede CloudSimple](vpn-gateway.md)
