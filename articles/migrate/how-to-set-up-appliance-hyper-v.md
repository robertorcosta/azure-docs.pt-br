---
title: Configurar um dispositivo de migrações para Azure para Hyper-V
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs do Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 21d88c4a2b2095fe677fe479bd7320f7a494db9e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929901"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um dispositivo para VMs do Hyper-V

Siga este artigo para configurar o dispositivo de migrações para Azure para avaliação de VMs do Hyper-V com a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md)  é um dispositivo leve usado pelas migrações para Azure: avaliação/migração do servidor para descobrir VMs do Hyper-V locais e enviar dados de metadados/desempenho da VM para o Azure.

Você pode implantar o dispositivo usando dois métodos:

- Configurar em uma VM do Hyper-V usando um VHD baixado. Esse é o método descrito neste artigo.
- Configurar em uma VM do Hyper-V ou computador físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deve ser usado se você não puder configurar uma VM usando um VHD ou se estiver no Azure governamental.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.

## <a name="appliance-deployment-vhd"></a>Implantação de dispositivo (VHD)

Para configurar o dispositivo usando um modelo VHD:

- Forneça um nome de dispositivo e gere uma chave de projeto de migrações para Azure no Portal.
- Baixe um VHD compactado do Hyper-V no portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure usando a chave de projeto migrações para Azure.

### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave de projeto de migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **1: gerar chave de projeto de migrações para Azure**, forneça um nome para o dispositivo de migrações para Azure que você irá configurar para a descoberta de VMs do Hyper-V. o nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto de migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante sua configuração.

### <a name="download-the-vhd"></a>Baixar o VHD

Em **2: baixar o dispositivo de migrações para Azure**, selecione o. Arquivo VHD e clique em **baixar**. 

   ![Seleções para computadores de descoberta](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Seleções para a chave de geração](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Para a versão do dispositivo 2.19.11.12, o hash gerado deve corresponder a essas [configurações](./tutorial-assess-hyper-v.md#verify-security).




## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado para uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.

    ![Implantar o VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

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
1. Aceite os **termos de licença**e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
      - Clique em **Configurar proxy** para e especifique o endereço do proxy (no formulário http://ProxyIPAddress ou na http://ProxyFQDN) porta de escuta.
      - Especifique as credenciais caso o proxy exija autenticação.
      - Há suporte apenas para o proxy HTTP.
      - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta da VM funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas. Depois que a verificação for concluída, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave de projeto de migrações para Azure** copiada do Portal. Se você não tiver a chave, vá para **avaliação do servidor> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração de chave e copie a chave correspondente.
1. Clique em **fazer logon**. Ele abrirá um prompt de logon do Azure em uma nova guia do navegador. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Depois de fazer logon com êxito, volte para o aplicativo Web. 
4. Se a conta de usuário do Azure usada para registro em log tiver as [permissões](tutorial-prepare-hyper-v.md#prepare-azure) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em SMBs, você precisará habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso no dispositivo:

1. Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Como alternativa, faça isso no Editor de Política de Grupo Local no dispositivo:
    - Em **Política do Computador local** > **Configuração do Computador**, clique em **Modelos Administrativos** > **Sistemas** > **Delegação de Credenciais**.
    - Clique duas vezes em **Permitir delegação de novas credenciais** e selecione **Habilitado**.
    - Em **Opções**, clique em **Mostrar** e adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .
    - Em **Delegação de Credenciais**, clique duas vezes em **Permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Mais uma vez, adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de VM.

1. Na **etapa 1: fornecer credenciais de host do Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de usuário** e **senha** para um host/cluster Hyper-v que o dispositivo usará para descobrir VMS. Clique em **Save**.
1. Se você quiser adicionar várias credenciais ao mesmo tempo, clique em **adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais para a descoberta de VMs do Hyper-V.
1. Na **etapa 2: fornecer detalhes de host/cluster do Hyper-v**, clique em **Adicionar origem de descoberta** para especificar o host do Hyper-v/ **endereço IP do cluster/FQDN** e o nome amigável para as credenciais a serem conectadas ao host/cluster.
1. Você pode **Adicionar um único item** por vez ou **adicionar vários itens** em um só lugar. Também há uma opção para fornecer detalhes de host/cluster do Hyper-V por meio de **importação CSV**.

    ![Seleções para adicionar origem de descoberta](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se você escolher **Adicionar um único item**, precisará especificar um nome amigável para credenciais e o host do Hyper-V/ **endereço IP do cluster/FQDN** e clicar em **salvar**.
    - Se você escolher **adicionar vários itens** _(selecionados por padrão)_, poderá adicionar vários registros de uma só vez especificando o host do Hyper-V/ **endereço IP do cluster/FQDN** com o nome amigável para credenciais na caixa de texto. **Verifique** os registros adicionados e clique em **salvar**.
    - Se você escolher **importar CSV**, poderá baixar um arquivo de modelo CSV, preencher o arquivo com o host do Hyper-V/ **endereço IP do cluster/FQDN** e nome amigável para credenciais. Em seguida, importe o arquivo para o dispositivo, **Verifique** os registros no arquivo e clique em **salvar**.

1. Ao clicar em salvar, o dispositivo tentará validar a conexão com os hosts/clusters do Hyper-V adicionados e mostrará o **status de validação** na tabela em cada host/cluster.
    - Para os hosts/clusters validados com êxito, você pode exibir mais detalhes clicando em seu endereço IP/FQDN.
    - Se a validação falhar para um host, examine o erro clicando em **validação falha** na coluna status da tabela. Corrija o problema e valide novamente.
    - Para remover hosts ou clusters, clique em **excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
1. Você pode **revalidar** a conectividade a hosts/clusters a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta**para disparar a descoberta de VM dos hosts/clusters validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada host/cluster na tabela.

Isso iniciará a descoberta. Leva aproximadamente 2 minutos por host para que os metadados de servidores descobertos apareçam na portal do Azure.

## <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta terminar, você poderá verificar se as VMs são exibidas no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com a avaliação do servidor de migrações para Azure.
