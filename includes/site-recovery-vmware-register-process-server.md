---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534555"
---
1. Estabeleça uma conexão de área de trabalho remota para a máquina que executa o servidor de processo. 
2. Execute cspsconfigtool.exe para iniciar a ferramenta de configuração do Azure Site Recovery Process Server.
    - A ferramenta é lançada automaticamente na primeira vez que você entra no servidor de processo.
    - Se ele não abrir automaticamente, clique em seu atalho na área de trabalho.

3. No **servidor de configuração FQDN ou IP,** especifique o nome ou endereço IP do servidor de configuração com o qual registrar o servidor de processo.
4. Na **porta do servidor de configuração,** certifique-se de que 443 esteja especificado. Esta é a porta na qual o servidor de configuração ouve solicitações.
5. Em **'Frase de falha' ' Conexão,** especifique a senha que você especificou ao configurar o servidor de configuração. Para encontrar a senha:
    -  No servidor de configuração, navegue até a pasta de instalação de recuperação de site **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Execute o comando abaixo para imprimir a senha atual:
    ```
    genpassphrase.exe -n
    ```

6. Na **Porta de Transferência de Dados,** deixe o valor padrão a menos que você tenha especificado uma porta personalizada.

7. Clique **em Salvar** salvar as configurações e registre o servidor de processo.

    
    ![Registre o servidor de processo](./media/site-recovery-vmware-register-process-server/register-ps.png)
