---
title: Solução de problemas Implantação e descoberta de aparelhos Do Azure Migrate
description: Obtenha ajuda para implantar o aparelho Azure Migrate e descobrir máquinas.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 9fbf55fbe16d958bf10541894159dade26668bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336718"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Solucionar problemas do aparelho E discovery do Azure Migrate

Este artigo ajuda você a solucionar problemas ao implantar o aparelho [Azure Migrate](migrate-services-overview.md) e usar o aparelho para descobrir máquinas no local.


## <a name="whats-supported"></a>Há suporte para quê?

[Revise](migrate-appliance.md) os requisitos de suporte do aparelho.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada manifesto ovf inválido"

Se você receber o erro "O arquivo manifesto fornecido é inválido: entrada de manifesto OVF inválida", faça o seguinte:

1. Verifique se o arquivo OVA do aparelho Azure Migrate é baixado corretamente verificando seu valor de hash. [Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor de hash não corresponder, baixe o arquivo OVA novamente e tente novamente a implantação.
2. Se a implantação ainda falhar, e você estiver usando o cliente VMware vSphere para implantar o arquivo OVF, tente implantá-lo através do cliente web vSphere. Se a implantação ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente web vSphere e tentando implantá-lo no vCenter Server 6.5 ou 6.7, tente implantar o OVA diretamente no host ESXi:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) com o cliente web (https://<*host IP Address*>/ui).
   - Em **Inventário doméstico,** > **Inventory**selecione O modelo **File** > **Deploy OVF**. Navegue até o OVA e complete a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte do Migrações para Azure.

## <a name="cant-connect-to-the-internet"></a>Não pode se conectar à internet

Isso pode acontecer se a máquina do aparelho estiver atrás de um proxy.

- Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
- Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, adicione [essas URLs](migrate-appliance.md#url-access) a uma lista de permitir.
- Se você estiver usando um proxy de interceptação para se conectar à internet, importe o certificado proxy para a VM do aparelho usando [essas etapas](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Erro de sincronização de data/hora

Um erro sobre a sincronização de data e hora (802) indica que o relógio do servidor pode estar fora de sincronia com o tempo atual em mais de cinco minutos. Alterar o tempo do relógio na VM coletora para corresponder ao tempo atual:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute **w32tm /tz**.
3. Para sincronizar o tempo, execute **w32tm /ressincronização**.


## <a name="unabletoconnecttoserver"></a>"In-LocávelConecte-se ao servidor"

Se você tiver esse erro de conexão, você pode não conseguir se conectar ao vCenter *ServerName*.com:9443. Os detalhes de erro indicam que `https://\*servername*.com:9443/sdk` não há nenhum ponto final ouvindo que possa aceitar a mensagem.

- Verifique se você está executando a versão mais recente do aparelho. Se você não estiver, atualize o aparelho para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se o problema ainda ocorrer na versão mais recente, o aparelho pode não conseguir resolver o nome do servidor vCenter especificado, ou a porta especificada pode estar errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar à porta número 443.

    1. Ping *Servername*.com do aparelho.
    2. Se a etapa 1 falhar, tente se conectar ao servidor vCenter usando o endereço IP.
    3. Identifique o número de porta correto para se conectar ao vCenter Server.
    4. Verifique se o vCenter Server está em funcionamento.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: Aparelho pode não estar registrado

- Erro 60052, "O aparelho pode não ser registrado com sucesso no projeto Azure Migrate" ocorre se a conta Do Azure usada para registrar o aparelho tiver permissões insuficientes.
    - Certifique-se de que a conta de usuário do Azure usada para registrar o aparelho tenha pelo menos permissões de contribuinte na assinatura.
    - [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- Erro 60039, "O aparelho pode não estar registrado com sucesso no projeto Azure Migrate" pode ocorrer se o registro falhar porque o projeto Azure Migrate usado para registrar o aparelho não pode ser encontrado.
    - No portal Dozure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto Azure Migrate em seu grupo de recursos e registre o aparelho novamente. [Aprenda a](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: falha na operação de gerenciamento do Key Vault

Se você receber o erro 60030 ou 60031, "Uma operação de gerenciamento do Azure Key Vault falhou", faça o seguinte:
- Certifique-se de que a conta de usuário do Azure usada para registrar o aparelho tenha pelo menos permissões de contribuinte na assinatura.
- Certifique-se de que a conta tenha acesso ao cofre de chaves especificado na mensagem de erro e, em seguida, tente novamente a operação.
- Se o problema persistir, contate o Suporte da Microsoft.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: A descoberta não pôde ser iniciada

Erro 60028: "A descoberta não pôde ser iniciada por causa de um erro. A operação falhou para a lista especificada de hosts ou clusters" indica que a descoberta não poderia ser iniciada nos hosts listados no erro devido a um problema no acesso ou recuperação de informações de VM. O resto dos anfitriões foram adicionados com sucesso.

- Adicione os hosts listados no erro novamente, usando a opção **Adicionar host.**
- Se houver um erro de validação, revise a orientação de correção para corrigir os erros e tente novamente a opção **Salvar e iniciar a detecção.**

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: Falha na operação do Azure AD 
Erro 60025: "Uma operação Azure AD falhou. O erro ocorreu ao criar ou atualizar o aplicativo Azure AD" ocorre quando a conta de usuário do Azure usada para iniciar a descoberta é diferente da conta usada para registrar o aparelho. Realize um dos seguintes procedimentos:

- Certifique-se de que a conta do usuário que incide a descoberta é a mesma usada para registrar o aparelho.
- Forneça permissões de acesso ao aplicativo do Azure Active Directory para a conta do usuário para a qual a operação de detecção está falhando.
- Exclua o grupo de recursos criado anteriormente para o projeto Azure Migrate. Crie outro grupo de recursos para começar novamente.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as permissões do aplicativo do Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: Não é possível conectar-se ao host ou cluster

Erro 50004: "Não é possível conectar-se a um host ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9" pode ocorrer se o serviço Azure DNS para o aparelho não puder resolver o cluster ou o nome do host que você forneceu.

- Se você ver esse erro no cluster, cluster FQDN.
- Você também pode ver este erro para hosts em um cluster. Isso indica que o aparelho pode se conectar ao cluster, mas o cluster retorna nomes host que não são FQDNs. Para resolver esse erro, atualize o arquivo hosts no aparelho adicionando um mapeamento do endereço IP e nomes de host:
    1. Abra o Bloco de Notas como um admin.
    2. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster onde você vê este erro.
    4. Salve e feche o arquivo de hosts.
    5. Verifique se o aparelho pode se conectar aos hosts, usando o aplicativo de gerenciamento do aparelho. Após 30 minutos, você deve ver as últimas informações para esses hosts no portal Azure.

## <a name="discovered-vms-not-in-portal"></a>VMs descobertos não estão no portal

Se o estado de descoberta for "Discovery em andamento", mas ainda não ver as VMs no portal, aguarde alguns minutos:
- Leva cerca de 15 minutos para uma VMware VMware .
- Leva cerca de dois minutos para cada host adicionado para a descoberta do Hyper-V VM.

Se você esperar e o estado não mudar, **selecione Atualizar** na guia **Servidores.** Isso deve mostrar a contagem dos servidores descobertos no Azure Migrate: Server Assessment e Azure Migrate: Migração do servidor.

Se isso não funcionar e você estiver descobrindo servidores VMware:

- Verifique se a conta vCenter especificada tem permissões definidas corretamente, com acesso a pelo menos uma VM.
- O Azure Migrate não pode descobrir VMms VMware se a conta vCenter tiver acesso concedido no nível da pasta vCenter VM. [Saiba mais](set-discovery-scope.md) sobre a descoberta de escopo.

## <a name="vm-data-not-in-portal"></a>Dados de VM que não estão no portal

Se as VMs descobertas não aparecerem no portal ou se os dados da VM estão desatualizados, aguarde alguns minutos. Leva até 30 minutos para que alterações nos dados de configuração de VM descobertos apareçam no portal. Pode levar algumas horas para que alterações nos dados do aplicativo apareçam. Se não houver dados após este tempo, tente atualizar, como segue

1. Em **Servers** > **Azure Migrate Server Assessment**, selecione Visão **geral**.
2. Em **Gerenciar,** selecione **Agent Health**.
3. Selecione **Agente de atualização**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs excluídas aparecem no portal

Se você excluir VMs e eles ainda aparecerem no portal, aguarde 30 minutos. Se eles ainda aparecerem, atualize como descrito acima.

## <a name="common-app-discovery-errors"></a>Erros comuns de detecção de aplicativos

O Azure Migrate suporta a descoberta de aplicativos, funções e recursos, usando o Azure Migrate: Server Assessment. A descoberta de aplicativos é suportada apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e etapas para configurar a descoberta do aplicativo.

Erros típicos de detecção de aplicativos são resumidos na tabela. 

**Erro** | **Causa** | **Ação**
--- | --- | --- | ---
10000: "Não é possível descobrir os aplicativos instalados no servidor". | Isso pode ocorrer se o sistema operacional da máquina não for o Windows ou o Linux. | Use apenas a descoberta de aplicativos para Windows/Linux.
10001: "Não é possível recuperar os aplicativos instalados no servidor". | Erro interno - alguns arquivos ausentes no aparelho. | Contatar Suporte da Microsoft
10002: "Não é possível recuperar os aplicativos instalados no servidor". | O agente de descoberta do aparelho pode não estar funcionando corretamente. | Se o problema não se resolver dentro de 24 horas, entre em contato com o suporte.
10003 "Não é possível recuperar os aplicativos instalados no servidor". | O agente de descoberta do aparelho pode não estar funcionando corretamente. | Se o problema não se resolver dentro de 24 horas, entre em contato com o suporte.
10004: "Incapaz de descobrir aplicativos instalados para máquinas de> <Windows/Linux." |  Credenciais para acessar <máquinas de> Windows/Linux não foram fornecidas no aparelho.| Adicione uma credencial ao aparelho que tem acesso às máquinas <Windows/Linux>.
10005: "Incapaz de acessar o servidor local". | As credenciais de acesso podem estar erradas. | Atualize as credenciais do aparelho certifique-se de que você pode acessar a máquina relevante com elas. 
10006: "Incapaz de acessar o servidor local". | Isso pode ocorrer se o sistema operacional da máquina não for o Windows ou o Linux.|  Use apenas a descoberta de aplicativos para Windows/Linux.
10007: "Incapaz de processar os metadados recuperados" | Este erro interno ocorreu ao tentar desserializar json | Entre em contato com o suporte da Microsoft para obter uma resolução
9000/9001/9002: "Não é possível descobrir os aplicativos instalados no servidor". | As ferramentas VMware podem não ser instaladas ou corrompidas. | Instale/reinstale ferramentas VMware na máquina relevante e verifique se está em execução.
9003: Não é possível descobrir os aplicativos instalados no servidor". | Isso pode ocorrer se o sistema operacional da máquina não for o Windows ou o Linux. | Use apenas a descoberta de aplicativos para Windows/Linux.
9004: "Não é possível descobrir os aplicativos instalados no servidor". | Isso pode acontecer se a VM for desligada. | Para descobrir, certifique-se de que a VM está ligado.
9005: "Não é possível descobrir os aplicativos instalados na VM. | Isso pode ocorrer se o sistema operacional da máquina não for o Windows ou o Linux. | Use apenas a descoberta de aplicativos para Windows/Linux.
9006/9007: "Não é possível recuperar os aplicativos instalados no servidor". | O agente de descoberta do aparelho pode não estar funcionando corretamente. | Se o problema não se resolver dentro de 24 horas, entre em contato com o suporte.
9008: "Não é possível recuperar os aplicativos instalados no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9009: "Não é possível recuperar os aplicativos instalados no servidor". | Pode ocorrer se as configurações do UAC (Controle de conta de usuário do Windows) no servidor forem restritivas e impedir a descoberta de aplicativos instalados. | Procure as configurações de 'Controle da conta de usuário' no servidor e configure a configuração uac no servidor para um dos dois níveis inferiores.
9010: "Não é possível recuperar os aplicativos instalados no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9011: "Arquivo para baixar de convidado não é encontrado na VM convidada" | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, entre em contato com o Microsoft Support.
9012: "O conteúdo do arquivo de resultados está vazio." | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, entre em contato com o Microsoft Support.
9013: "Um novo perfil temporário é criado para cada login no VMware VMM" | Um novo perfil temporário é criado para cada login na VM | Certifique-se de que o nome de usuário fornecido nas credenciais vm convidadas esteja no formato UPN.
9015: "Não é possível conectar-se às VMS vMware devido a privilégios insuficientes no vCenter" | A função Operações de hóspedes não está habilitada na conta de usuário do vCenter | Certifique-se de que a função Operações de hóspedes esteja ativada na conta de usuário do vCenter.
9016: "Não é possível conectar-se às VMS VMware, pois o agente de operações convidado está sem dados" | As ferramentas VMware não estão instaladas corretamente ou não estão atualizadas. | Certifique-se de que as ferramentas VMware estão devidamente instaladas e atualizadas.
9017: "Arquivo com metadados descobertos não é encontrado na VM." | O problema pode ocorrer devido a um erro interno. | Entre em contato com o Suporte da Microsoft para obter uma resolução.
9018: "O PowerShell não está instalado nas VMs convidadas." | O PowerShell não está disponível na VM convidada. | Instale o PowerShell na VM convidada.
9019: "Incapaz de descobrir devido a falhas na operação da VM" | A operação de hóspedes da VMware falhou na VM. | Certifique-se de que as credenciais vm são válidas e o nome do usuário fornecido nas credenciais vm convidado está no formato UPN.
9020: "A permissão de criação de arquivos é negada." | A função associada ao usuário ou à política de grupo está restringindo o usuário a criar o arquivo na pasta | Verifique se o usuário convidado fornecido criou permissão para o arquivo na pasta. Consulte **Notificações** na Avaliação do Servidor para o nome da pasta.
9021: "A permissão de criação de arquivos é negada na pasta System Temp Path." | A versão da ferramenta VMware na VM não tem suporte | Atualize sua versão da ferramenta VMware acima de 10.2.0.
9022: "O acesso ao objeto WMI é negado." | A função associada ao usuário ou à política de grupo está restringindo o usuário a acessar o objeto WMI. | Entre em contato com o Suporte da Microsoft.
9023: "O valor variável do ambiente SystemRoot está vazio." | Não é conhecido | Entre em contato com o Suporte da Microsoft.
9024: "O valor variável do ambiente TEMP está vazio." | Não é conhecido | Entre em contato com o Suporte da Microsoft.
9025: "PowerShell é corrupto nas VMs convidadas." | Não é conhecido | Reinstale o PowerShell na VM convidada e verifique se o PowerShell pode ser executado na VM convidada.
8084: "Incapaz de descobrir aplicativos devido a <Exception from VMware>erro do VMware: " | O aparelho Azure Migrate usa APIs vmware para descobrir aplicativos. Esse problema pode acontecer se uma exceção for lançada pelo vCenter Server enquanto tenta descobrir aplicativos. A mensagem de falha do VMware é exibida na mensagem de erro mostrada no portal. | Procure a mensagem na documentação do [VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e siga os passos a serem corrigidos. Se você não puder corrigir, entre em contato com o suporte da Microsoft.



## <a name="next-steps"></a>Próximas etapas
Configure um aparelho para servidores [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)ou [físicos.](how-to-set-up-appliance-physical.md)
