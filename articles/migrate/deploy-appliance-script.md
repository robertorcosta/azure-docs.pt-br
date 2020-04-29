---
title: Configurar um dispositivo de migrações para Azure com um script
description: Saiba como configurar um dispositivo de migrações para Azure com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676308"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um dispositivo com um script

Siga este artigo para criar um [dispositivo de migrações para Azure](deploy-appliance.md) para a avaliação/migração de VMs VMware e VMs do Hyper-V. Você executa um script para criar um dispositivo e verifica se ele pode se conectar ao Azure. 

Você pode implantar o dispositivo para VMs do VMware e do Hyper-V usando um script ou usando um modelo que você baixa do portal do Azure. Usar um script será útil se você não conseguir criar uma VM usando o modelo baixado.

- Para usar um modelo, siga os tutoriais para [VMware](tutorial-prepare-vmware.md) ou [Hyper-V](tutorial-prepare-hyper-v.md).
- Para configurar um dispositivo para servidores físicos, você só pode usar um script. Siga [Este artigo](how-to-set-up-appliance-physical.md).
- Para configurar um dispositivo em uma nuvem do Azure governamental, siga [Este artigo](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Pré-requisitos

O script configura o dispositivo migrações para Azure em uma VM ou máquina física existente.

- O computador que atuará como o dispositivo deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo. Ele requer um endereço IP estático ou dinâmico e o acesso à Internet.
- Antes de implantar o dispositivo, examine os requisitos de dispositivo detalhados para [VMs VMware](migrate-appliance.md#appliance---vmware), [VMs do Hyper-V](migrate-appliance.md#appliance---hyper-v)e [servidores físicos](migrate-appliance.md#appliance---physical).
- Não execute o script em um dispositivo de migrações do Azure existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o dispositivo para VMware

Para configurar o dispositivo para VMware, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.

### <a name="download-the-script"></a>Baixar o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3.  Clique em **baixar**para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Verifique o valor de hash gerado. Para obter a versão mais recente do dispositivo:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Certifique-se de não executar o script em um computador em um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller. ps1**, da seguinte maneira:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar acesso

Verifique se o dispositivo pode se conectar às URLs do Azure para a nuvem [pública](migrate-appliance.md#public-cloud-urls) .

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o dispositivo para Hyper-V

Para configurar o dispositivo para Hyper-V, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.

### <a name="download-the-script"></a>Baixar o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3.  Clique em **baixar**para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique os valores de hash gerados. Para obter a versão mais recente do dispositivo:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Certifique-se de não executar o script em um computador em um dispositivo de migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller. ps1**, da seguinte maneira:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar acesso

Verifique se o dispositivo pode se conectar às URLs do Azure para a nuvem [pública](migrate-appliance.md#public-cloud-urls) .

## <a name="next-steps"></a>Próximas etapas

Depois de implantar o dispositivo, você precisa configurá-lo pela primeira vez e registrá-lo com o projeto de migrações para Azure.

- Configure o dispositivo para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configure o dispositivo para o [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
