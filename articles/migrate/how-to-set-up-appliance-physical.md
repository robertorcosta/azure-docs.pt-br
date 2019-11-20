---
title: Configurar um dispositivo de migrações para Azure para servidores físicos
description: Saiba como configurar um dispositivo de migrações para Azure para avaliação de servidor físico.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ade2a76c168002e573a6504e31769b83f0c6eb37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185902"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um dispositivo para servidores físicos

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando servidores físicos com a ferramenta migrações para Azure: Server Assessment.

> [!NOTE]
> Se os recursos forem mencionados aqui que você ainda não vê no portal de migrações para Azure, aguarde. Eles aparecerão na semana seguinte ou em breve.

O dispositivo de migrações para Azure é um dispositivo leve, usado pela avaliação de servidor de migrações para Azure para fazer o seguinte:

- Descobrir servidores locais.
- Enviar dados de desempenho e metadados para servidores descobertos para avaliação do Azure migrar servidor.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo:
- Baixe um arquivo compactado com o script do instalador de Migrações para Azure do portal do Azure.
- Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
- Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

## <a name="download-the-installer-script"></a>Baixe o script do instalador.

Baixe o arquivo compactado para o dispositivo.

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Em **Descobrir computadores** > **São seus computadores virtualizados?** , clique em **Não virtualizado/outro.**
3. Clique em **Download** para baixar o arquivo compactado.

    ![Baixar VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f



## <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalador de Migrações para Azure
= Esse script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instala as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para Migrações para Azure.
- Cria os seguintes arquivos sob o caminho:
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Config
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Logs

Crie o script da seguinte maneira:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo compactado baixado.
4. Execute o script, executando o seguinte comando:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
O script iniciará o aplicativo Web do dispositivo quando ele for concluído com êxito.



### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)necessárias.

## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
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


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a servidores físicos e inicie a descoberta.

1. Em **Adicionar credenciais**, especifique as credenciais de conta que o dispositivo usará para descobriros servidores.  
2. Especifique o **Sistema operacional**, nome amigável para as credenciais, **Nome de usuário** e **Senha** e clique em **Adicionar**.
Você pode adicionar um conjunto de credenciais para servidores Windows e Linux.
4. Clique em **Adicionar servidor**e especifique detalhes do servidor – endereço FQDN/IP e nome amigável das credenciais (uma entrada por linha) para se conectar ao servidor.
3. Clique em **Validar**. Após a validação, a lista de servidores que podem ser descobertos é mostrada.
    - Se a validação falhar para um servidor, examine o erro passando o ponteiro do mouse sobre o ícone na coluna **Status**. Corrija os problemas e valide novamente.
    - Para remover um servidor, selecione > **Excluir**.
4. Após a validação, clique em **Salvar e iniciar descoberta** para iniciar o processo de descoberta.

Isso iniciará a descoberta. São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você pode verificar se os servidores aparecem no Portal.

1. Abra o painel das Migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação de servidores físicos](tutorial-assess-physical.md) com a avaliação de servidor de migrações para Azure.
