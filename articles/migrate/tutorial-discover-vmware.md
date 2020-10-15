---
title: Descobrir VMs de VMware com a Avaliação de Servidor das Migrações para Azure
description: Saiba como descobrir VMs de VMware locais com a ferramenta de Avaliação de Servidor das Migrações para Azure
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: f39ad3cbc357575f735b963346c8a8b0cc95e7c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442227"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Tutorial: Descobrir VMs de VMware com a Avaliação de Servidor

Como parte de seu percurso de migração para o Azure, descubra o inventário local e as cargas de trabalho. 

Este tutorial mostrará como descobrir VMs (máquinas virtuais) de VMware locais com as Migrações para Azure: usando a ferramenta de Avaliação de Servidor e um dispositivo leve das Migrações para Azure. Implante o dispositivo como uma VM de VMware para descobrir VMs e metadados de desempenho de modo contínuo, além de aplicativos em execução nas VMs e dependências de VM.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure.
> * Prepare o ambiente de VMware para descoberta.
> * Criar um projeto do Migrações para Azure.
> * Configurar o dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se estes pré-requisitos estão em vigor.


**Requisito** | **Detalhes**
--- | ---
**Host de vCenter Server/ESXi** | Será necessário obter um vCenter Server executando a versão 5.5, 6.0, 6.5 ou 6.7.<br/><br/> As VMs deverão ser hospedadas em um host ESXi executando a versão 5.5 ou posterior.<br/><br/> No vCenter Server, permita conexões de entrada na porta TCP 443 para que o dispositivo possa coletar os dados de avaliação.<br/><br/> O dispositivo é conectado ao vCenter na porta 443 por padrão. Se o vCenter Server escutar em uma porta diferente, será possível modificar a porta ao se conectar do dispositivo ao servidor para iniciar a descoberta.<br/><br/> No servidor ESXi que hospeda as VMs, certifique-se de que o acesso de entrada seja permitido na porta TCP 443 para obter a descoberta de aplicativos.
**Dispositivo** | O vCenter Server precisará de recursos com o objetivo de alocar uma VM para o dispositivo das Migrações para Azure:<br/><br/> - Windows Server 2016<br/><br/> – 32 GB de RAM, oito vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> – Um comutador virtual externo e acesso à Internet para a VM, de modo direto ou por meio de um proxy.
**VMs** | Para usar este tutorial, as VMs do Windows devem estar executando o Windows Server 2016, 2012 R2, 2012 ou 2008 R2.<br/><br/> As VMs do Linux devem estar executando o Red Hat Enterprise Linux 7/6/5, o Ubuntu Linux 14.04/16.04, o Debian 7/8, o Oracle Linux 6/7 ou o CentOS 5/6/7.<br/><br/> As VMs precisam que ferramentas de VMware (uma versão posterior à 10.2.0) estejam instaladas e em execução.<br/><br/> O Windows PowerShell 2.0 ou posterior deverá ser instalado nas VMs do Windows.


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto das Migrações para Azure e registrar o dispositivo de Migrações para Azure, você precisa de uma conta com:
- Permissões de Colaborador ou Proprietário em uma assinatura do Azure.
- Permissões para registrar aplicativos do Azure Active Directory.

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual você deseja criar um projeto das Migrações para Azure. 
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-vmware/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-vmware/assign-role.png)

7. No portal, pesquise por usuários e, em **Serviços**, selecione **Usuários**.
8. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar em Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-vmware/register-apps.png)

9. Como alternativa, o locatário/administrador global pode atribuir a função de **Desenvolvedor de Aplicativos** a uma conta para permitir o registro de aplicativos do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Preparar o VMware

No vCenter Server, crie uma conta que o dispositivo possa usar para acessar o vCenter Server e verifique se as portas necessárias estão abertas. Também será necessário obter uma conta que o dispositivo possa usar para acessar as VMs. 

### <a name="create-an-account-to-access-vcenter"></a>Criar uma conta para acessar o vCenter

No Cliente Web vSphere, configure uma conta da seguinte maneira:

1. No Cliente Web vSphere > selecione **Administração** usando uma conta com privilégios de administrador.
2. Em **Acesso**, selecione **Usuários e Grupos de SSO**.
3. Em **Usuários**, adicione um novo usuário.
4. Em **Novo Usuário**, digite os detalhes da conta. Em seguida, clique em **OK**.
5. Em **Permissões Globais**, selecione a conta de usuário e atribua a função **Somente Leitura** à conta. Em seguida, clique em **OK**.
6. Em **Funções** > selecione a função **Somente Leitura**. Em **Privilégios**, selecione **Operações de Convidado**. Esses privilégios serão necessários para descobrir os aplicativos em execução nas VMs e analisar as dependências de VM.
 
    ![Use a caixa de seleção para permitir operações de convidado na função Somente Leitura](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Criar uma conta para acessar as VMs

O dispositivo acessará as VMs para descobrir aplicativos e analisar dependências de VM. O dispositivo não instalará nenhum agente nas VMs.

1. Crie uma conta do Administrador Local que o dispositivo possa usar para descobrir aplicativos e dependências em VMs do Windows.
2. Para computadores Linux, crie uma conta de usuário com privilégios raiz ou, como alternativa, uma conta de usuário com as seguintes permissões em arquivos /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> As Migrações para Azure são compatíveis com uma credencial para descoberta de aplicativos em todos os servidores do Windows. Além de uma credencial para descoberta de aplicativos em todas as máquinas Linux.


## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto das Migrações para Azure.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-vmware/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado.

A ferramenta **Migrações para Azure: Avaliação de Servidor** é adicionada por padrão ao novo projeto.

![Página mostrando a ferramenta de Avaliação de Servidor adicionada por padrão](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurar o dispositivo

Para configurar o dispositivo usando um modelo OVA, siga as etapas abaixo:
- Forneça um nome de dispositivo e gere uma chave de projeto das Migrações para Azure no portal
- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o no projeto das Migrações para Azure usando a chave de projeto das Migrações para Azure.

> [!NOTE]
> Caso não possa, por alguma razão, configurar o dispositivo usando o modelo, você poderá configurá-lo usando um script do PowerShell. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Implantar usando um modelo OVA

Para configurar o dispositivo usando um modelo OVA, siga as etapas abaixo:
- Forneça um nome de dispositivo e gere uma chave de projeto das Migrações para Azure no portal
- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o no projeto das Migrações para Azure usando a chave de projeto das Migrações para Azure.

### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave do projeto das Migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **As suas máquinas são virtualizadas?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3. Em **1: Gerar chave de projeto das Migrações para Azure**, forneça um nome para o dispositivo das Migrações para Azure que você vai configurar para a descoberta das VMs VMware. O nome deverá conter até 14 caracteres alfanuméricos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto das Migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .OVA e clique em **Baixar**. 


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exemplo de uso: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verifique as versões mais recentes do dispositivo e os valores de hash:

    - Para a nuvem pública do Azure:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140333) | bd5c19eec93a62d52cc507a6b7b408d07f33f92b7d39b8a1e3dfec4ec62830d7

    - Para o Azure Government:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.
2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy (na forma http://ProxyIPAddress ou http://ProxyFQDN) e na porta de escuta).
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo, conforme fornecido nas **Instruções de instalação**.

     A Migração de Servidor de Migrações para Azure usa o VDDK para replicar computadores durante a migração para o Azure. 
1. Se desejar, você poderá **executar os pré-requisitos novamente** a qualquer momento durante a configuração do dispositivo para verificar se ele atende a todos os pré-requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, acesse **Avaliação do Servidor> Descobrir> Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Clique em **Fazer logon**. Será aberto um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Depois de fazer logon com êxito, volte para o aplicativo Web. 
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](tutorial-prepare-vmware.md#prepare-azure) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.



## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

1. Na **Etapa 1: Fornecer as credenciais do vCenter Server**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicionar o **Nome de usuário** e a **Senha** para a conta do vCenter Server que o dispositivo usará para descobrir as VMs na instância do vCenter Server.
    - Você deve ter configurado uma conta com as permissões necessárias no [tutorial anterior](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.
1. Na **Etapa 2: Fornecer detalhes do vCenter Server**, clique em **Adicionar origem da descoberta** para selecionar o nome amigável de credenciais na lista suspensa e especifique o **Endereço IP/FQDN** da instância do vCenter Server. Você pode deixar a **Porta** como padrão (443) ou especificar uma porta personalizada na qual o vCenter Server escuta e clique em **Salvar**.
1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **Status de validação** na tabela com relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade com o vCenter Server a qualquer momento antes de iniciar a descoberta.
1. Na **Etapa 3: Fornecer as credenciais da VM para descobrir os aplicativos instalados e executar o mapeamento de dependência sem agente**, clique em **Adicionar credenciais** e especifique o sistema operacional para o qual as credenciais são fornecidas, o nome amigável para as credenciais e o **Nome de usuário** e a **Senha**. Em seguida, clique em **Salvar**.

    - Opcionalmente, adicione credenciais aqui se você tiver criado uma conta a ser usada para o [recurso de descoberta de aplicativo](how-to-discover-applications.md) ou o [recurso de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se você não desejar usar esses recursos, poderá clicar no controle deslizante para ignorar a etapa. Você poderá reverter a intenção a qualquer momento posterior.
    - Examine as credenciais necessárias para a [descoberta do aplicativo](migrate-support-matrix-vmware.md#application-discovery-requirements) ou para a [análise de dependência sem agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Clique em **Iniciar descoberta** para iniciar a descoberta da VM. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta com relação ao endereço IP/FQDN do vCenter Server na tabela.

A descoberta funciona da seguinte maneira:
- São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.
- A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs sendo descobertas. Para as VMs 500, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.


## <a name="next-steps"></a>Próximas etapas

- [Avaliar VMs de VMware](tutorial-assess-vmware.md) a fim de obter a migração para VMs do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta.
