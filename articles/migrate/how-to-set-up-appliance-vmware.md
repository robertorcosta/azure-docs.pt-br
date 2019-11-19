---
title: Configurar um dispositivo para migração/avaliação do servidor migrações para VMs VMware | Microsoft Docs
description: Descreve como configurar um dispositivo para descoberta, avaliação e migração sem agente de VMs do VMware usando a migração/avaliação do servidor migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 086d5bf2e0e2bd1e4c1db5960d402a8e1b129e94
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158604"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um dispositivo para VMs VMware

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando VMs VMware com a ferramenta de avaliação de servidor migrações para Azure ou migrando VMs VMware para o Azure com a migração sem agente usando a ferramenta de migração de servidor migrações para Azure.

O dispositivo de VM VMware é um dispositivo leve usado pela migração/avaliação do servidor de migrações para Azure para fazer o seguinte:

- Descubra VMs locais do VMware.
- Enviar dados de desempenho e metadados para VMs descobertas para migração/avaliação de servidor migrar do Azure.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo:
- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

## <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , clique em **Sim, com o hipervisor do VMware vSphere**.
3. Clique em **Baixar** para baixar o arquivo de modelo .OVA.



### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o comando a seguir para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.
2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de disco**, especifique o tipo e o tamanho do disco.
8. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização de horário**: o tempo é verificado. O tempo no dispositivo deve ser sincronizado com o horário da Internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: migrações para Azure verifica se as atualizações mais recentes do dispositivo estão instaladas.
    - **Instalar o VDDK**: migrações para Azure verifica se o VDDK (Kit de desenvolvimento de disco virtual) do VMware vSphere está instalado.
        - As migrações para Azure usam o VDDK para replicar máquinas durante a migração para o Azure.
        - Baixe o VDDK 6.7 da VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Clique em **Fazer Logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e sua senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
2. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado. Em seguida, selecione o projeto.
3. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **Registrar**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Iniciar a descoberta contínua fornecendo vCenter Server e credencial de VM

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em **Especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Você pode manter a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escutará.
2. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta somente leitura que o dispositivo usará para descobrir VMs no vCenter Server. Verifique se a conta tem as [permissões necessárias para a descoberta](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Você pode fazer o escopo da descoberta limitando o acesso à conta do vCenter de acordo; Saiba mais sobre a descoberta de escopo [aqui](tutorial-assess-vmware.md#scoping-discovery).
3. Clique em **Validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais de VM
Para a descoberta de aplicativos, funções e recursos e visualização de dependências das VMs, você pode fornecer uma credencial de VM que tenha acesso às VMs VMware. Você pode adicionar uma credencial para VMs do Windows e uma credencial para VMs do Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sobre os privilégios de acesso necessários.

> [!NOTE]
> Essa entrada é opcional e é necessária para habilitar a descoberta de aplicativos e a visualização de dependência sem agente.

1. Em **Descobrir aplicativos e dependências em VMs**, clique em **Adicionar credenciais**.
2. Selecione o **Sistema operacional**.
3. Informar um nome amigável para a credencial.
4. Em **Nome de usuário** e **Senha**, especifique uma conta que tenha pelo menos acesso de convidado nas VMs.
5. Clique em **Adicionar**.

Depois de especificar o vCenter Server e as credenciais da VM (opcional), clique em **Salvar e iniciar a descoberta** para iniciar a descoberta do ambiente local.

São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal. A descoberta de aplicativos, funções e recursos instalados leva algum tempo, a duração depende do número de VMs que estão sendo descobertas. Para as VMs 500, leva aproximadamente 1 hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Examine os tutoriais para [avaliação do VMware](tutorial-assess-vmware.md) e [migração sem agente](tutorial-migrate-vmware.md).
