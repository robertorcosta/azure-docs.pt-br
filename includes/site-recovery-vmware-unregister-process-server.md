---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67172450"
---
Siga as etapas para suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Cancelar o registro de um servidor de processo conectado

1. Estabeleça uma conexão remota com o servidor de processo como administrador.
2. No **painel de controle**, abra **programas > desinstalar um programa**.
3. Desinstale o programa **Microsoft Azure site Recovery serviço de mobilidade/servidor de destino mestre**.
4. Desinstale o programa **Microsoft Azure site Recovery servidor de configuração/processo**.
5. Depois que os programas nas etapas 3 e 4 forem desinstalados, desinstale **Microsoft Azure site Recovery configurações/dependências do servidor de processo**.

### <a name="unregister-a-disconnected-process-server"></a>Cancelar o registro de um servidor de processo desconectado

Use essas etapas apenas se não houver como reativar o computador no qual o servidor de processo está instalado.

1. Entre no servidor de configuração como um administrador.
2. Abra um prompt de comando administrativo e navegue até `%ProgramData%\ASR\home\svsystems\bin`.
3. Execute este comando para obter uma lista de um ou mais servidores de processo.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processo.
    - IP/Nome: o endereço IP e o nome do computador que executa o servidor de processo.
    - Pulsação: última pulsação do computador do servidor de processo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processo que você deseja cancelar o registro.
5. Cancelar o registro de um servidor de processo remove todos os seus detalhes do sistema e exibe a mensagem: **nome do servidor não registrado com êxito> (servidor-IP-address)**

