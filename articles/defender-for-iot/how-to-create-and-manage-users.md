---
title: Criar e gerenciar usuários
description: Crie e gerencie usuários de sensores e o console de gerenciamento local. Os usuários podem receber a função de administrador, analista de segurança ou usuário somente leitura.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c3a9e1c7e96d0392e1f94b71549f612738622dea
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837939"
---
# <a name="about-defender-for-iot-console-users"></a>Sobre os usuários do console do defender para IoT

Este artigo descreve como criar e gerenciar usuários de sensores e o console de gerenciamento local. As funções de usuário incluem administrador, analista de segurança ou usuário somente leitura. Cada função é associada a um intervalo de permissões para ferramentas para o sensor ou o console de gerenciamento local. As funções foram projetadas para facilitar o acesso granular e seguro ao Azure defender para IoT.

Os recursos também estão disponíveis para acompanhar a atividade do usuário e habilitar a entrada Active Directory.

Por padrão, cada sensor e o console de gerenciamento local são instalados com um *CyberX e* um usuário de suporte. Esses usuários têm acesso a ferramentas avançadas para solução de problemas e configuração. Os usuários administradores devem entrar com essas credenciais de usuário, criar um usuário administrador e, em seguida, criar usuários adicionais para analistas de segurança e usuários somente leitura.

## <a name="role-based-permissions"></a>Permissões baseadas em função
As seguintes funções de usuário estão disponíveis:

- **Somente leitura**: os usuários somente leitura executam tarefas como exibir alertas e dispositivos no mapa do dispositivo. Esses usuários têm acesso às opções exibidas em **navegação**.

- **Analista de segurança**: os analistas de segurança têm permissões de usuário somente leitura. Eles também podem executar ações em dispositivos, reconhecer alertas e usar ferramentas de investigação. Esses usuários têm acesso às opções exibidas em **navegação** e **análise**.

- **Administrador**: os administradores têm acesso a todas as ferramentas, incluindo a definição de configurações do sistema, a criação e o gerenciamento de usuários e muito mais. Esses usuários têm acesso às opções exibidas em **navegação**, **análise** e **Administração**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Permissões baseadas em função para ferramentas de console de gerenciamento local

Esta seção descreve as permissões disponíveis para administradores, analistas de segurança e usuários somente leitura para o console de gerenciamento local.  

| Permissão | Somente leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Exibir e filtrar o mapa Enterprise | ✓ | ✓ | ✓ |
| Criar um site |  |  | ✓ |
| Gerenciar um site (adicionar e editar zonas) |  |  | ✓ |
| Exibir e filtrar o inventário de dispositivos | ✓ | ✓ | ✓ |
| Exibir e gerenciar alertas: reconhecer, aprender e fixar | ✓ | ✓ | ✓ |
| Gerar relatórios |  | ✓ | ✓ |
| Exibir relatórios de avaliação de risco |  | ✓ | ✓ |
| Definir exclusões de alerta |  | ✓ | ✓ |
| Exibir ou definir grupos de acesso |  |  | ✓ |
| Gerenciar configurações do sistema |  |  | ✓ |
| Gerenciar usuários |  |  | ✓ |
| Enviar dados de alerta para parceiros |  |  | ✓ |
| Gerenciar certificados |  |  | ✓ |
| Tempo limite da sessão quando os usuários não estão ativos | 30 minutos | 30 minutos  | 30 minutos  |

#### <a name="assign-users-to-access-groups"></a>Atribuir usuários a grupos de acesso

Os administradores podem aprimorar o controle de acesso do usuário no defender para IoT atribuindo usuários a *grupos de acesso* específicos. Os grupos de acesso são atribuídos a zonas, sites, regiões e unidades de negócios onde um sensor está localizado. Ao atribuir usuários a grupos de acesso, os administradores têm controle específico sobre onde os usuários gerenciam e analisam as detecções de dispositivos. 

Trabalhar dessa maneira acomoda grandes organizações em que as permissões de usuário podem ser complexas ou determinadas por uma política de segurança organizacional global. Para obter mais informações, consulte [definir o controle de acesso global](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Permissões baseadas em função para ferramentas de sensor

Esta seção descreve as permissões disponíveis para administradores de sensor, analistas de segurança e usuários somente leitura.  

| Permissão | Somente leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Exibir o painel | ✓ | ✓ | ✓ |
| Exibições de zoom do mapa de controle |  |  | ✓ |
| Exibir alertas | ✓ | ✓ | ✓ |
| Gerenciar alertas: reconhecer, aprender e fixar |  | ✓ | ✓ |
| Exibir eventos em uma linha do tempo |  | ✓ | ✓ |
| Autorizar dispositivos, dispositivos de verificação conhecidos, dispositivos de programação |  | ✓ | ✓ |
| Exibir dados de investigação | ✓ | ✓ | ✓ |
| Gerenciar configurações do sistema |  |  | ✓ |
| Gerenciar usuários |  |  | ✓ |
| Servidores DNS para pesquisa inversa |  |  | ✓ |
| Enviar dados de alerta para parceiros |  | ✓ | ✓ |
| Criar comentários de alerta |  | ✓ | ✓ |
| Exibir histórico de alterações de programação | ✓ | ✓ | ✓ |
| Criar regras de alerta personalizadas |  | ✓ | ✓ |
| Gerenciar várias notificações simultaneamente |  | ✓ | ✓ |
| Gerenciar certificados |  |  | ✓ |
| Tempo limite da sessão quando os usuários não estão ativos | 30 minutos | 30 minutos | 30 minutos |

## <a name="define-users"></a>Definir usuários

Esta seção descreve como definir usuários. Os usuários de CyberX, suporte e administrador podem adicionar, remover e atualizar outras definições de usuário.

Para definir um usuário:

1. No painel esquerdo do sensor ou do console de gerenciamento local, selecione **usuários**.
2. Na janela **usuários** , selecione **criar usuário**.
3. No painel **criar usuário** , defina os seguintes parâmetros:

   - **Nome de usuário**: Insira um nome de usuário.
   - **Email**: Insira o endereço de email do usuário.
   - **Nome**: Insira o nome do usuário.
   - **Sobrenome**: Insira o sobrenome do usuário.
   - **Função**: defina a função do usuário. Consulte [permissões baseadas em função](#role-based-permissions).
   - **Grupo de acesso**: se você estiver criando um usuário para o console de gerenciamento local, defina o grupo de acesso do usuário. Consulte [definir o controle de acesso global](how-to-define-global-user-access-control.md).
   - **Senha**: selecione o tipo de usuário da seguinte maneira:
     - **Usuário local**: defina uma senha para o usuário de um sensor ou um console de gerenciamento local. A senha deve incluir pelo menos seis caracteres e deve incluir letras e números.
     - **Active Directory usuário**: você pode permitir que os usuários entrem no sensor ou no console de gerenciamento usando as credenciais do Active Directory. Os grupos de Active Directory definidos podem ser associados a níveis de permissão específicos. Por exemplo, configure um grupo de Active Directory específico e atribua todos os usuários no grupo ao tipo de usuário somente leitura.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Gerencie seus usuários.":::

## <a name="user-session-timeout"></a>Tempo limite da sessão de usuário

Se os usuários não estiverem ativos no teclado ou no mouse por um horário específico, eles serão desconectados de sua sessão e deverão entrar novamente.

Quando os usuários não funcionaram com o mouse ou o teclado do console por um período de 30 minutos, uma sessão de saída é forçada.

Esse recurso é habilitado por padrão e na atualização, mas pode ser desabilitado. Além disso, os tempos de contagem de sessão podem ser atualizados. Os tempos de sessão são definidos em segundos. As definições são aplicadas por sensor e pelo console de gerenciamento local.

Uma mensagem de tempo limite de sessão é exibida no console quando o tempo limite de inatividade foi aprovado.

### <a name="control-inactivity-sign-out"></a>Controlar a saída de inatividade

Os usuários administradores podem habilitar e desabilitar a saída de inatividade e ajustar os limites de inatividade.

Para acessar o comando:

1. Entre na CLI para o sensor ou console de gerenciamento local usando o defender para credenciais administrativas de IoT.

2. Digite `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Para desabilitar o recurso, altere `infinity_session_expiration = true` para `infinity_session_expiration = false` .

Para atualizar os períodos de contagem de saída, ajuste o `= <number>` valor para o tempo necessário.


## <a name="track-user-activity"></a>Acompanhar a atividade do usuário 

Você pode acompanhar a atividade do usuário na linha do tempo de evento em cada sensor. A linha do tempo exibe o evento ou o dispositivo afetado e a hora e a data em que o usuário realizou a atividade.

Para exibir a atividade do usuário:

1. Entre no sensor.
1. Na linha do tempo do evento, habilite a opção **operações do usuário** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Exibir a atividade de um usuário.":::

## <a name="integrate-with-active-directory-servers"></a>Integrar com servidores Active Directory 

Configure o sensor ou o console de gerenciamento local para trabalhar com Active Directory. Isso permite que Active Directory usuários acessem os consoles do defender para IoT usando suas credenciais de Active Directory.

Há suporte para dois tipos de autenticação baseada em LDAP:

- **Autenticação completa**: os detalhes do usuário são recuperados do servidor LDAP. Os exemplos são as permissões nome, sobrenome, email e usuário.

- **Usuário confiável**: somente a senha do usuário é recuperada. Outros detalhes do usuário que são recuperados são baseados em usuários definidos no sensor.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Permissões do Active Directory e do defender para IoT

Você pode associar grupos de Active Directory definidos aqui com níveis de permissão específicos. Por exemplo, configure um grupo de Active Directory específico e atribua permissões de RO a todos os usuários no grupo. Consulte [criar e gerenciar usuários](how-to-create-and-manage-users.md) para obter detalhes.

Para configurar Active Directory:

1. No painel esquerdo, selecione **configurações do sistema**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Exiba as configurações do sistema Active Directory.":::

2. No painel **configurações do sistema** , selecione **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Edite suas configurações de Active Directory.":::

3. Na caixa de diálogo **Editar configuração de Active Directory** , selecione **Active Directory a integração habilitada**  >  . A caixa de diálogo **Editar configuração de Active Directory** expande e agora você pode inserir os parâmetros para configurar Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Insira os parâmetros a serem configurados Active Directory.":::

    > [!NOTE]
    > - Você deve definir os parâmetros LDAP aqui exatamente como eles aparecem no Active Directory.
    > - Para todos os parâmetros de Active Directory, use somente minúsculas. Use minúsculas mesmo quando as configurações em Active Directory usarem maiúsculas.
    > - Você não pode configurar LDAP e LDAPs para o mesmo domínio. No entanto, você pode usar ambos para domínios diferentes ao mesmo tempo.

4. Defina os parâmetros do servidor Active Directory, da seguinte maneira:

   | Parâmetro de servidor | Descrição |
   |--|--|
   | FQDN do controlador de domínio | Defina o nome de domínio totalmente qualificado (FQDN) exatamente como ele aparece no servidor LDAP. Por exemplo, insira `host1.subdomain.domain.com`. |
   | Porta do controlador de domínio | Defina a porta na qual o LDAP está configurado. |
   | Domínio primário | Defina o nome de domínio (por exemplo, `subdomain.domain.com` ) e o tipo de conexão de acordo com a configuração do LDAP. |
   | Grupos do Active Directory | Insira os nomes de grupo que são definidos em sua configuração de Active Directory no servidor LDAP. |
   | Domínios confiáveis | Para adicionar um domínio confiável, adicione o nome de domínio e o tipo de conexão de um domínio confiável. <br />Você pode configurar domínios confiáveis somente para usuários que foram definidos em usuários. |

5. Clique em **Salvar**.

6. Para adicionar um servidor confiável, selecione **Adicionar servidor** e configurar outro servidor.

## <a name="see-also"></a>Consulte também

[Ativar e configurar seu sensor](how-to-activate-and-set-up-your-sensor.md) 
 [Ativar e configurar seu console](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 de gerenciamento local [Acompanhar atividade do sensor](how-to-track-sensor-activity.md)
