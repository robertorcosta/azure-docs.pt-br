---
title: Habilite a virtualização aninhada em um modelo VM no Azure Lab Services (UI) | Microsoft Docs
description: Aprenda a criar um modelo vm com várias VMs dentro.  Em outras palavras, habilite a virtualização aninhada em um modelo VM no Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503069"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Habilite a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services manualmente

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada usuário no laboratório uma máquina virtual configurada com várias VMs dentro dele.  Para obter mais informações sobre virtualização aninhada e serviços do Azure Lab, consulte [Ativar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Este artigo abrange como configurar a virtualização aninhada em uma máquina de modelos no Azure Lab Services usando diretamente funções e ferramentas do Windows.  Existem algumas coisas necessárias para permitir que uma classe use virtualização aninhada.  As etapas abaixo descreverão como configurar manualmente um modelo de máquina de Serviços de Laboratório com Hyper-V.  As etapas destinam-se ao Windows Server 2016 ou ao Windows Server 2019.  

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="enable-hyper-v-role"></a>Habilitar a função Hyper-V

As etapas a seguir descrevem as ações necessárias para ativar o Hyper-V no Windows Server usando qualquer servidor do Server Manager.  Uma vez que a instalação seja bem-sucedida, o gerenciador Hyper-V estará disponível para adicionar, modificar e excluir máquinas virtuais do cliente.

1. No **Gerenciador do servidor,** na página Painel, clique em **Adicionar funções e recursos**.
2. Na página **Antes de começar**, clique em **Avançar**.
3. Na página **Selecionar tipo de instalação,** mantenha a seleção padrão da instalação baseada em função ou baseada em recursos e clique em **'Avançar**' .
4. Na página **Selecionar servidor de destino,** selecione Selecionar um servidor no pool de servidores.   O servidor atual já será selecionado.  Clique em Avançar.
5. Na página **Selecionar funções do servidor**, selecione **Hyper-V**.  
6. O **pop-up Add Roles and Features Wizard** será exibido.  Selecione **Incluir ferramentas de gerenciamento (se aplicável)**.  Clique no botão **Adicionar recursos.**
7. Na página **Selecionar funções de servidor**, clique em **Avançar**.
8. Na **página Selecionar recursos,** clique **em "A seguir".**
9. Na página **Hyper-V**, clique em **Avançar**.
10. Na página **Criar switches virtuais,** aceite os padrões e clique **em Next**.
11. Na página **Migração de máquinavirtual,** aceite os padrões e clique **em Next**.
12. Na **página 'Lojas padrão',** aceite os padrões e clique **em 'Avançar**' .
13. Na página **Confirmar seleções de instalação,** **selecione Reiniciar o servidor de destino automaticamente se necessário**.
14. Quando o **pop-up do Assistente adicionar funções e recursos** aparecer, clique em **Sim**.
15. Clique em **Instalar**.
16. Aguarde a página **de progresso da instalação** para indicar que a função Hyper-V está completa.  A máquina pode reiniciar no meio da instalação.
17. Clique em **Fechar**.

## <a name="enable-dhcp-role"></a>Habilitar a função DHCP

Qualquer máquina virtual cliente Hyper-V criada, precisa de um endereço IP na rede NAT.  Criaremos a rede NAT mais tarde.  Uma maneira de atribuir endereços IP é configurar o host, neste caso o modelo de máquina virtual de laboratório, como um servidor DHCP.  Abaixo estão as etapas necessárias para ativar a função DHCP.

1. No **Gerenciador do servidor,** na página **Painel,** clique em **Adicionar funções e recursos**.
2. Na página **Antes de começar**, clique em **Avançar**.
3. Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em função ou recurso** e em **Avançar**.
4. Na página **Selecionar servidor de destino,** selecione o servidor atual no pool do servidor e clique em **'Avançar**' .
5. Na página **Selecionar funções do servidor,** selecione **Servidor DHCP**.  
6. O **pop-up Add Roles and Features Wizard** será exibido.  Selecione **Incluir ferramentas de gerenciamento (se aplicável)**.  Clique em **Adicionar Recursos**.

    >[!NOTE]
    >Você pode ver um erro de validação afirmando que não foram encontrados endereços IP estáticos.  Este aviso pode ser ignorado para o nosso cenário.

7. Na página **Selecionar funções de servidor**, clique em **Avançar**.
8. Na página **Selecionar recursos**, clique em **Avançar**.
9. Na página **do Servidor DHCP,** clique **em Next**.
10. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
11. Aguarde a **página de progresso da instalação** para indicar que a função DHCP está concluída.
12. Clique em Fechar.

## <a name="enable-routing-and-remote-access-role"></a>Habilite a função de roteamento e acesso remoto

1. No **Gerenciador do servidor,** na página **Painel,** clique em **Adicionar funções e recursos**.
2. Na página **Antes de começar**, clique em **Avançar**.
3. Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em função ou recurso** e em **Avançar**.
4. Na página **Selecionar servidor de destino,** selecione o servidor atual no pool do servidor e clique em **'Avançar**' .
5. Na página **Selecionar funções do servidor,** selecione **Acesso remoto**. Clique em **OK**.
6. Na página **Selecionar recursos**, clique em **Avançar**.
7. Na página **Acesso remoto,** clique **em Next**.
8. Na página **Serviços de função,** selecione **Roteamento**.
9. O **pop-up Add Roles and Features Wizard** será exibido.  Selecione **Incluir ferramentas de gerenciamento (se aplicável)**.  Clique em **Adicionar Recursos**.
10. Clique em **Avançar**.
11. Na página **Função de Servidor Web (IIS)**, clique em **Avançar**.
12. Na página **Selecionar serviços de função**, clique em **Avançar**.
13. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
14. Aguarde a página **de progresso instalação** para indicar que a função acesso remoto está concluída.  
15. Clique em **Fechar**.

## <a name="create-virtual-nat-network"></a>Criar rede VIRTUAL NAT

Agora que todas as funções necessárias foram instaladas, é hora de criar a rede NAT.  O processo de criação envolverá a criação de um switch e a própria rede NAT.  Uma rede NAT (tradução de endereço de rede) atribui um endereço IP público a um grupo de VMs em uma rede privada para permitir a conectividade à internet.  No nosso caso, o grupo de VMs privadas serão as VMs aninhadas.  A rede NAT permitirá que as VMs aninhadas se comuniquem entre si. Um switch é um dispositivo de rede que lida com o recebimento e o roteamento do tráfego em uma rede.

### <a name="create-a-new-virtual-switch"></a>Crie um novo switch virtual

1. Abra **o Hyper-V Manager** das ferramentas administrativas do Windows.
2. Selecione o servidor atual no menu de navegação à esquerda.
3. Clique **em Virtual Switch Manager...** do menu **Ações** no lado direito do **Hyper-V Manager**.
4. No **pop-up virtual do Switch Manager,** selecione **Interno** para o tipo de switch a ser criado.  Clique em **Criar comutador virtual**.
5. Para o switch virtual recém-criado, defina o nome como algo memorável.  Para este exemplo, usaremos 'LabServicesSwitch'.  Clique em **OK**.
6. Um novo adaptador de rede será criado.  O nome será semelhante ao 'vEthernet (LabServicesSwitch)'.  Para verificar a abertura do **Painel de Controle,** clique **em Rede e Internet,** clique **em Exibir status e tarefas da rede**.  À esquerda, clique em **Alterar configurações do adaptador**.

### <a name="create-a-nat-network"></a>Crie uma rede NAT

1. Abra a ferramenta **Roteamento e Acesso Remoto** a partir de ferramentas administrativas do Windows.
2. Selecione o servidor local na página de navegação à esquerda.
3. Escolha **configurar ação** -> **e habilitar roteamento e acesso remoto**.
4. Quando **o assistente de configuração do servidor de roteamento e acesso remoto aparecer,** clique em **Next**.
5. Na página **Configuração,** selecione Configuração de **tradução de endereço de rede (NAT).**  Clique em **Avançar**.

    >[!WARNING]
    >Não escolha a opção 'Rede privada virtual (VPN) e NAT'.

6. Na página **NAT Conexão à Internet,** escolha 'Ethernet'.  Não escolha a conexão 'vEthernet (LabServicesSwitch)' que criamos no Hyper-V Manager. Clique em **Avançar**.
7. Clique **em Terminar** na última página do assistente.
8. Quando a **caixa de diálogo Iniciar o serviço** for exibida, clique em Iniciar **serviço**.
9. Espere até que o serviço seja iniciado.

## <a name="update-network-adapter-settings"></a>Atualizar as configurações do adaptador de rede

O adaptador de rede será associado ao IP usado para o IP gateway padrão para a rede NAT criada anteriormente.  Neste exemplo, criamos um endereço IP de 192.168.0.1 com uma máscara de sub-rede de 255.255.255.0.  Usaremos o switch virtual criado anteriormente.

1. Abra o **Painel de Controle,** clique **em Rede e Internet,** clique **em Exibir status da rede e tarefas**.
2. À esquerda, clique em **Alterar configurações do adaptador**.  
3. Na janela **Conexões de rede,** clique duas vezes em 'vEthernet (LabServicesSwitch)' para mostrar a caixa de diálogo de detalhes **de status vEthernet (LabServicesSwitch).**
4. Clique no botão **Propriedades.**
5. Selecione o item **Protocolo da Internet Versão 4 (TCP/IPv4)** e clique no botão **Propriedades.**
6. Na caixa de diálogo Propriedades Do Protocolo da **Internet Versão 4 (TCP/IPv4),** **selecione Usar o seguinte endereço IP**.  Para o endereço IP, digite 192.168.0.1. Para a máscara de sub-rede, digite 255.255.255.0.  Deixe o gateway padrão em branco.  Deixe os servidores DNS em branco também.

    >[!NOTE]
    > Nosso alcance para nossa rede NAT será, na notação CIDR, 192.168.0.0/24.  Isso cria uma gama de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  Por convenção, os gateways têm o primeiro endereço IP em uma faixa de sub-rede.

7. Clique em OK.

## <a name="create-dhcp-scope"></a>Criar Escopo do DHCP

As seguintes etapas são instruções para adicionar escopo DHCP.  Neste artigo, nossa rede NAT é 192.168.0.0/24 na notação CIDR.  Isso cria uma gama de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  O escopo criado deve estar nessa faixa de endereços utilizáveis, excluindo o endereço IP já criado anteriormente.

1. Abra **ferramentas administrativas** e abra a ferramenta administrativa **DHCP.**
2. Na ferramenta **DHCP,** expanda o nó para o servidor atual e selecione **IPv4**.
3. No menu Ação, escolha **Novo Escopo...**
4. Quando o **Novo Assistente de Escopo** for exibido, clique em **Seguir** na página **Bem-vindo.**
5. Na página Nome do **escopo,** digite 'LabServicesDhcpScope' ou algo mais memorável para o nome.  Clique em **Avançar**.
6. Na página **'Intervalo de endereços IP',** digite os seguintes valores.

    - 192.168.0.100 para o endereço IP Iniciar
    - 192.168.0.200 para o endereço IP final
    - 24 para o Comprimento
    - 255.255.255.0 para a máscara de sub-rede

7. Clique em **Avançar**.
8. Na página **Adicionar exclusões e retardar,** clique **em Avançar**.
9. Na página Duração do contrato de **locação,** clique **em Next**.
10. Na página **Configurar opções dHCP,** selecione **Sim, eu quero configurar essas opções agora**. Clique em **Avançar**.
11. No **roteador (Gateway padrão)**
12. Adicione 192.168.0.1, se ainda não estiver pronto. Clique em **Avançar**.
13. Na página **Nome de domínio e servidores DNS,** adicione 168.63.129.16 como um endereço IP do servidor DNS, se ainda não estiver pronto.  168.63.129.16 é o endereço IP de um servidor DNS estático do Azure. Clique em **Avançar**.
14. Na página **WINS Servers,** clique **em Next**.
15. Uma página **ativar escopo,** selecione **Sim, eu quero ativar este escopo agora**.  Clique em **Avançar**.
16. Na **página Completar o Novo Assistente de Escopo,** clique **em Concluir**.

## <a name="conclusion"></a>Conclusão

Agora sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V.   Consulte [Criar uma máquina virtual em Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hyper-V.  Consulte também o [Centro de Avaliação](https://www.microsoft.com/evalcenter/) da Microsoft para verificar os sistemas operacionais e software disponíveis.

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)