---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67172450"
---
Siga os passos para suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Desregistre um servidor de processo conectado

1. Estabeleça uma conexão remota ao servidor de processo como administrador.
2. No **Painel de Controle,** programas abertos **> Desinstale um programa.**
3. Desinstale o programa **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Desinstale o programa **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Depois que os programas nas etapas 3 e 4 forem desinstalados, desinstale as **dependências de configuração/servidor de processo do Microsoft Azure Site**.

### <a name="unregister-a-disconnected-process-server"></a>Desregistre um servidor de processo desconectado

Use apenas essas etapas se não houver como reviver a máquina na qual o servidor de processo está instalado.

1. Faça login no servidor de configuração como administrador.
2. Abra um prompt de comando `%ProgramData%\ASR\home\svsystems\bin`administrativo e navegue para .
3. Execute este comando para obter uma lista de um ou mais servidores de processo.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processo.
    - IP/Nome: O endereço IP e o nome da máquina que executa o servidor de processo.
    - Batimentos cardíacos: Último batimento cardíaco da máquina do servidor de processo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processo que deseja cancelar o registro.
5. Cancelar o registro de um servidor de processo remove todos os seus detalhes do sistema e exibe a mensagem: **Sem registro com sucesso> (endereço IP do servidor)**

