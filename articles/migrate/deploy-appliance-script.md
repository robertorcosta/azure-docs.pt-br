---
title: Configure um aparelho Azure Migrate com um script
description: Saiba como configurar um aparelho Do Azure Migrate com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676308"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configure um aparelho com um script

Siga este artigo para criar um [aparelho Azure Migrate](deploy-appliance.md) para avaliação/migração de VMs VMware e VMs Hyper-V. Você executa um script para criar um aparelho e verifique se ele pode se conectar ao Azure. 

Você pode implantar o aparelho para VMware e Hyper-V VMs usando um script ou usando um modelo que você baixa do portal Azure. Usar um script é útil se você não conseguir criar uma VM usando o modelo baixado.

- Para usar um modelo, siga os tutoriais para [VMware](tutorial-prepare-vmware.md) ou [Hyper-V](tutorial-prepare-hyper-v.md).
- Para configurar um aparelho para servidores físicos, você só pode usar um script. Siga [este artigo](how-to-set-up-appliance-physical.md).
- Para configurar um aparelho em uma nuvem do Governo Azure, siga [este artigo](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate em uma máquina física ou VM existente.

- A máquina que funcionará como aparelho deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo. Ele requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de implantar o aparelho, revise os requisitos detalhados do aparelho para [VMs,](migrate-appliance.md#appliance---vmware) [VMs Hyper-V](migrate-appliance.md#appliance---hyper-v)e [servidores físicos.](migrate-appliance.md#appliance---physical)
- Não execute o script em um aparelho Azure Migrate existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configure o aparelho para VMware

Para configurar o aparelho para VMware, você baixa um arquivo fechado do portal Azure e extrai o conteúdo. Você executa o script PowerShell para iniciar o aplicativo web do aparelho. Você configura o aparelho e o configura pela primeira vez. Em seguida, você registra o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Baixe o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3.  Clique **em Download,** para baixar o arquivo fechado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Verifique o valor de hash gerado. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>Executar o script

Eis o que o roteiro faz:

- Instala agentes e um aplicativo web.
- Instala funções do Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Baixa e instala um módulo regravável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registro (HKLM), com configurações persistentes para o Azure Migrate.
- Cria arquivos de log e configuração da seguinte forma:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extrair o arquivo fechado para uma pasta na máquina que hospedará o aparelho. Certifique-se de que você não execute o script em uma máquina em um aparelho Azure Migrate existente.
2. Inicie o PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do arquivo fechado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte forma:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Depois que o script é executado com sucesso, ele inicia a aplicação web do aparelho para que você possa configurar o aparelho. Se você encontrar algum problema, revise os logs de script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Marcação de tempo</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode se conectar aos URLs do Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="set-up-the-appliance-for-hyper-v"></a>Configure o aparelho para Hyper-V

Para configurar o aparelho para Hyper-V, você baixa um arquivo fechado do portal Azure e extrai o conteúdo. Você executa o script PowerShell para iniciar o aplicativo web do aparelho. Você configura o aparelho e o configura pela primeira vez. Em seguida, você registra o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Baixe o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3.  Clique **em Download,** para baixar o arquivo fechado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique os valores de hash gerados. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>Executar o script

Eis o que o roteiro faz:

- Instala agentes e um aplicativo web.
- Instala funções do Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Baixa e instala um módulo regravável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registro (HKLM), com configurações persistentes para o Azure Migrate.
- Cria arquivos de log e configuração da seguinte forma:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extrair o arquivo fechado para uma pasta na máquina que hospedará o aparelho. Certifique-se de que você não execute o script em uma máquina em um aparelho Azure Migrate existente.
2. Inicie o PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do arquivo fechado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte forma:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois que o script é executado com sucesso, ele inicia a aplicação web do aparelho para que você possa configurar o aparelho. Se você encontrar algum problema, revise os logs de script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Marcação de tempo</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode se conectar aos URLs do Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Próximas etapas

Depois de implantar o aparelho, você precisa configurá-lo pela primeira vez e registrá-lo no projeto Azure Migrate.

- Configure o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configure o aparelho para [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
