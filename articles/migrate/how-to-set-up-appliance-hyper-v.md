---
title: Configurar um dispositivo de migrações para Azure para Hyper-V
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs do Hyper-V.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 1676ad3dde5a97d2afdb64595d51f5502f9dcc3d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185926"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um dispositivo para VMs do Hyper-V

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando VMs do Hyper-V com a ferramenta de avaliação de servidor migrações para Azure ou migrando VMs VMware para o Azure usando a ferramenta de migração de servidor migrações para Azure.

O dispositivo de VM do Hyper-V é um dispositivo leve usado pela migração/avaliação do servidor de migrações para Azure para fazer o seguinte:

- Descubra VMs do Hyper-V locais.
- Enviar dados de desempenho e metadados para VMs descobertas para migração/avaliação de servidor migrar do Azure.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo:
- Baixe um VHD compactado do Hyper-V no portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

## <a name="download-the-vhd"></a>Baixar o VHD

Baixe o modelo de VHD compactado para o dispositivo.

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , clique em **Sim, com o Hyper-V**.
3. Clique em **Download** para baixar o arquivo VHD.

    ![Baixar VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão do dispositivo 2.19.11.12, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado para uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.

    ![Implantar o VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Avançar**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Avançar**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Avançar**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Avançar**.
5. Em **Escolher Rede**, especifique o comutador virtual que será usado pela VM. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie a máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No Gerenciador do Hyper-V > **Máquinas Virtuais**, clique com o botão direito do mouse na VM > **Conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização de horário**: o tempo é verificado. o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta da VM funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Clique em **Fazer Logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e sua senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
4. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **Registrar**.


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

1. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta que o dispositivo usará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Salvar detalhes**.
2. Clique em **Adicionar host** e especifique os detalhes do host/cluster do Hyper-V.
3. Clique em **Validar**. Após a validação, o número de VMs que podem ser descobertas em cada host/cluster é mostrado.
    - Se a validação falhar para um host, examine o erro passando o ponteiro do mouse sobre o ícone na coluna **Status**. Corrija os problemas e valide novamente.
    - Para remover hosts ou clusters, selecione > **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
4. Após a validação, clique em **Salvar e iniciar descoberta** para iniciar o processo de descoberta.

Isso iniciará a descoberta. São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal do Azure.

## <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta terminar, você poderá verificar se as VMs são exibidas no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com a avaliação do servidor de migrações para Azure.
