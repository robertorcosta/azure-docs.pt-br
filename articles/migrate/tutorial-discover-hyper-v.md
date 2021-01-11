---
title: Descobrir VMs do Hyper-V usando a Avaliação de Servidor das Migrações para Azure
description: Saiba como descobrir VMs do Hyper-V locais com a ferramenta de Avaliação de Servidor das Migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 90532a88e145507b09de9d36f704bc5c88899e95
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861906"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Tutorial: descobrir VMs do Hyper-V com a Avaliação de Servidor

Como parte de sua jornada de migração para o Azure, descubra seu inventário local e suas cargas de trabalho. 

Este tutorial mostra como descobrir VMs (máquinas virtuais) do Hyper-V locais com ferramenta Migrações para Azure: Avaliação de Servidor usando um dispositivo leve de Migrações para Azure. Implante o dispositivo como uma VM do Hyper-V para descobrir continuamente os metadados de desempenho e do computador.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure
> * Preparar o ambiente do Hyper-V para descoberta.
> * Criar um projeto do Migrações para Azure.
> * Configurar o dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se estes pré-requisitos estão em vigor.


**Requisito** | **Detalhes**
--- | ---
**Host do Hyper-V** | Os hosts do Hyper-V nos quais as VMs estão localizadas podem ser autônomos ou estar em um cluster.<br/><br/> O host deve executar Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/><br/> Verifique se as conexões de entrada são permitidas na porta 5985 do WinRM (HTTP), para que o dispositivo possa se conectar a fim de extrair os metadados da VM e os dados de desempenho usando uma sessão do modelo CIM.
**Implantação do dispositivo** | O host do Hyper-V precisa de recursos a fim de alocar uma VM para o dispositivo:<br/><br/> - Windows Server 2016<br/><br/> – 16 GB de RAM<br/><br/> – Oito vCPUs<br/><br/> – Cerca de 80 GB de armazenamento em disco.<br/><br/> – Um comutador virtual externo.<br/><br/> – Acesso à Internet para a VM, diretamente ou por meio de um proxy.
**VMs** | As VMs podem executar qualquer sistema operacional Windows ou Linux. 

Antes de iniciar, [examine os dados](migrate-appliance.md#collected-data---hyper-v) que o dispositivo coleta durante a descoberta.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto das Migrações para Azure e registrar o dispositivo de Migrações para Azure, você precisa de uma conta com:
- Permissões de Colaborador ou Proprietário em uma assinatura do Azure.
- Permissões para registrar aplicativos do Azure Active Directory.

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:


1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual você deseja criar um projeto das Migrações para Azure. 
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-hyper-v/assign-role.png)

7. No portal, pesquise por usuários e, em **Serviços**, selecione **Usuários**.
8. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar nas Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Como alternativa, o locatário/administrador global pode atribuir a função de **Desenvolvedor de Aplicativos** a uma conta para permitir o registro de Aplicativos do AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Prepare os hosts do Hyper-V

Configure uma conta com acesso de Administrador nos hosts do Hyper-V. O dispositivo usa essa conta para descoberta.

- Opção 1: prepare uma conta com acesso de Administrador no computador host do Hyper-V.
- Opção 2: prepare uma conta de Administrador Local ou de Administrador de Domínio e adicione-a a estes grupos: Usuários de Gerenciamento Remoto, Administradores do Hyper-V e Usuários do Monitor de Desempenho.


## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto das Migrações para Azure.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado.

A ferramenta **Migrações para Azure: Avaliação de Servidor** é adicionada por padrão ao novo projeto.

![Página mostrando a ferramenta de Avaliação de Servidor adicionada por padrão](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurar o dispositivo

Este tutorial configura o dispositivo em uma VM do Hyper-V, da seguinte maneira:

- Forneça um nome de dispositivo e gere uma chave de projeto das Migrações para Azure no portal.
- Baixe um VHD compactado do Hyper-V no portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o no projeto das Migrações para Azure usando a chave de projeto das Migrações para Azure.
> [!NOTE]
> Se, por alguma razão, você não puder configurar o dispositivo usando um modelo, poderá configurá-lo usando um script do PowerShell. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave do projeto das Migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **1: Gerar chave de projeto das Migrações para Azure**, forneça um nome para o dispositivo das Migrações para Azure que você vai configurar para a descoberta das VMs do Hyper-V. O nome deverá conter até 14 caracteres alfanuméricos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto das Migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-vhd"></a>Baixar o VHD

Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .VHD e clique em **Baixar**. 


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.

2. Execute o comando do PowerShell a seguir para gerar o hash para o arquivo zip
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Verifique as versões mais recentes do dispositivo e os valores de hash:

    - Para a nuvem pública do Azure:

        **Cenário** | **Download** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Para o Azure Government:

        **Cenário** _ | _ *Download** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado para uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.
2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Próximo**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Próximo**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Próximo**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Próximo**.
5. Em **Escolher Rede**, especifique o comutador virtual que será usado pela VM. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie a máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do VHD baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No Gerenciador do Hyper-V > **Máquinas Virtuais**, clique com o botão direito do mouse na VM > **Conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
      - Clique em **Configurar proxy** e especifique o endereço de proxy (na forma http://ProxyIPAddress ou http://ProxyFQDN) e na porta de escuta.
      - Especifique as credenciais caso o proxy exija autenticação.
      - Há suporte apenas para o proxy HTTP.
      - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta da VM funcione corretamente.
    - **Instalar as atualizações**: A avaliação do servidor das Migrações para Azure verifica se o dispositivo tem as últimas atualizações instaladas. Depois que a verificação for concluída, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, acesse **Avaliação do Servidor> Descobrir> Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Será necessário um código do dispositivo para a autenticação com o Azure. Clicar em **Logon** abrirá uma janela restrita com o código do dispositivo, conforme mostrado abaixo.

    ![Janela restrita mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, cole o código do dispositivo e entre usando seu nome de usuário e sua senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Caso feche a guia de logon por engano sem fazer logon, você precisará atualizar a guia do navegador do gerenciador de configuração do dispositivo para habilitar novamente o botão Logon.
1. Depois de fazer logon com êxito, volte para a guia anterior usando o gerenciador de configuração do dispositivo.
4. Se a conta de usuário do Azure usada para o registro em log tiver as permissões corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em SMBs, você precisará habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso no dispositivo:

1. Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Como alternativa, faça isso no Editor de Política de Grupo Local no dispositivo:
    - Em **Política do Computador local** > **Configuração do Computador**, clique em **Modelos Administrativos** > **Sistemas** > **Delegação de Credenciais**.
    - Clique duas vezes em **Permitir delegação de novas credenciais** e selecione **Habilitado**.
    - Em **Opções**, clique em **Mostrar** e adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .
    - Em **Delegação de Credenciais**, clique duas vezes em **Permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Mais uma vez, adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de VM.

1. Na **Etapa 1: Fornecer as credenciais do host Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicione o **Nome de usuário** e a **Senha** para um host/cluster Hyper-V que o dispositivo usará para descobrir VMs. Clique em **Save**.
1. Se desejar adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para salvar e adicionar mais credenciais. Há suporte a várias credenciais para a descoberta de VMs Hyper-V.
1. Na **Etapa 2: Fornecer detalhes do host/cluster Hyper-V**, clique em **Adicionar origem da descoberta** para especificar o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais se conectarem ao host/cluster.
1. Você pode **Adicionar um item** de cada vez ou **Adicionar vários itens** em um só lugar. Também há uma opção de fornecer detalhes do host/cluster Hyper-V por meio de **Importar CSV**.


    - Se você escolher **Adicionar um item**, precisará especificar o nome amigável para as credenciais e o **endereço IP/FQDN** do host/cluster Hyper-V e clicar em **Salvar**.
    - Se você escolher **Adicionar vários itens** _(selecionado por padrão)_ , poderá adicionar vários registros de uma vez especificando o **endereço IP/FQDN** do host/cluster Hyper-V com o nome amigável para as credenciais na caixa de texto. **Verifique** os registros adicionados e clique em **Salvar**.
    - Se você escolher **Importar CSV**, poderá baixar um arquivo de modelo CSV, preenchê-lo com o **endereço IP/FQDN** do host/cluster Hyper-V e o nome amigável para as credenciais. Em seguida, importe o arquivo para o dispositivo, **verifique** os registros no arquivo e clique em **Salvar**.

1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com os hosts/clusters Hyper-V adicionados e mostrará o **Status de validação** na tabela em cada host/cluster.
    - Para ter hosts/clusters validados com êxito, você pode ver mais detalhes clicando no endereço IP/FQDN deles.
    - Se a validação falhar para um host, examine o erro clicando em **Falha na validação** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover hosts ou clusters, clique em **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
1. Você pode **revalidar** a conectividade com os hosts/clusters a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta** para iniciar a descoberta da VM dos hosts/clusters validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada host/cluster na tabela.

Isso iniciará a descoberta. São necessários aproximadamente 2 minutos por host para que os metadados dos servidores descobertos sejam exibidos no portal do Azure.

## <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta terminar, você poderá verificar se as VMs são exibidas no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração para as VMs do Azure.
- [Examinar os dados](migrate-appliance.md#collected-data---hyper-v) que o dispositivo coleta durante a descoberta.
