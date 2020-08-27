---
title: Configurar um dispositivo de migrações para Azure para VMware
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c9a9f1567f984fc5770b47d3998610cb69643360
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923595"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um dispositivo para VMs VMware

Siga este artigo para configurar o dispositivo migrações para Azure para avaliação com a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) e para a migração sem agente usando a ferramenta [migrações para Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md) é um dispositivo leve usado pelas migrações para Azure: avaliação de servidor e migração de servidor para descobrir VMs VMware locais, enviar dados de metadados/desempenho de VM para o Azure e para replicação de VMs VMware durante a migração sem agente.

Você pode implantar o dispositivo usando dois métodos:

- Configurar em uma VM do VMware usando um modelo OVA baixado. Esse é o método descrito neste artigo.
- Configurar em uma VM do VMware ou computador físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deverá ser usado se você não puder configurar uma VM usando um modelo OVA ou se você estiver no Azure Government.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.


## <a name="appliance-deployment-ova"></a>Implantação de dispositivo (OVA)

Para configurar o dispositivo usando um modelo OVA, você:
- Forneça um nome de dispositivo e gere uma chave de projeto de migrações para Azure no portal
- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure usando a chave de projeto migrações para Azure.

### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave de projeto de migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **As suas máquinas são virtualizadas?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3. Em **1: gerar chave de projeto de migrações para Azure**, forneça um nome para o dispositivo de migrações para Azure que você irá configurar para a descoberta de VMs VMware. o nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto de migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante sua configuração.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA
Em **2: baixar o dispositivo de migrações para Azure**, selecione o. OVA arquivo e clique em **baixar**. 


   ![Seleções para computadores de descoberta](./media/tutorial-assess-vmware/servers-discover.png)


   ![Seleções para a chave de geração](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o comando a seguir para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas [configurações](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **arquivo**  >  **implantar o modelo OVF**.
![Comando de menu para implantar um modelo de OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **mapeamento de rede**, especifique a rede à qual a VM será conectada. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


## <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. Aceite os **termos de licença**e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço do proxy (no formulário http://ProxyIPAddress ou http://ProxyFQDN) porta de escuta.
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Depois que a verificação for concluída, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6,7 do VMware e extraia o conteúdo do zip baixado para o local especificado no dispositivo, conforme fornecido nas **instruções de instalação**.

     A Migração de Servidor de Migrações para Azure usa o VDDK para replicar computadores durante a migração para o Azure. 
1. Se desejar, você poderá **executar novamente os pré-requisitos** a qualquer momento durante a configuração do dispositivo para verificar se o dispositivo atende a todos os pré-requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave de projeto de migrações para Azure** copiada do Portal. Se você não tiver a chave, vá para **avaliação do servidor> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração de chave e copie a chave correspondente.
1. Clique em **fazer logon**. Ele abrirá um prompt de logon do Azure em uma nova guia do navegador. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Depois de fazer logon com êxito, volte para o aplicativo Web. 
4. Se a conta de usuário do Azure usada para registro em log tiver as [permissões](tutorial-prepare-vmware.md#prepare-azure) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

1. Na **etapa 1: fornecer credenciais de vCenter Server**, clique **em Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de usuário** e **senha** para a conta de vCenter Server que o dispositivo usará para descobrir VMs na instância de vCenter Server.
    - Você deve ter configurado uma conta com as permissões necessárias no [tutorial anterior](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.
1. Na **etapa 2: fornecer vCenter Server detalhes**, clique em **Adicionar origem de descoberta** para selecionar o nome amigável para credenciais na lista suspensa, especifique o **endereço IP/FQDN** da instância de vCenter Server. Você pode deixar a **porta** como padrão (443) ou especificar uma porta personalizada na qual vCenter Server escuta e clicar em **salvar**.
1. Ao clicar em salvar, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **status de validação** na tabela em relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade para vCenter Server a qualquer momento antes de iniciar a descoberta.
1. Na **etapa 3: fornecer credenciais de VM para descobrir os aplicativos instalados e executar o mapeamento de dependência sem agente**, clique em **Adicionar credenciais**e especifique o sistema operacional para o qual as credenciais são fornecidas, nome amigável para credenciais e **nome de usuário** e **senha**. Em seguida, clique em **salvar**.

    - Opcionalmente, adicione credenciais aqui se você tiver criado uma conta a ser usada para o [recurso de descoberta de aplicativo](how-to-discover-applications.md) ou o [recurso de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se você não quiser usar esses recursos, poderá clicar no controle deslizante para ignorar a etapa. Você pode reverter a intenção a qualquer momento mais tarde.
    - Examine as credenciais necessárias para a [descoberta de aplicativos](migrate-support-matrix-vmware.md#application-discovery-requirements)ou para análise de [dependência sem agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Clique em **Iniciar descoberta**, para iniciar a descoberta de VM. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em relação à vCenter Server endereço IP/FQDN na tabela.

A descoberta funciona da seguinte maneira:
- São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.
- A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs sendo descobertas. Para as VMs 500, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Examine os tutoriais para [avaliação do VMware](tutorial-assess-vmware.md) e [migração sem agente](tutorial-migrate-vmware.md).
