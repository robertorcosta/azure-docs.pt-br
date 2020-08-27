---
title: Guia de implantação do FortiGate | Microsoft Docs
description: Saiba como configurar o logon único entre o Azure Active Directory e o FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657013"
---
# <a name="fortigate-deployment-guide"></a>Guia de implantação do FortiGate

## <a name="contents"></a>Sumário

- Como resgatar a licença do FortiGate
- Baixar o firmware
- Implantar a VM do FortiGate
   - Definir um endereço IP público estático e atribuir um nome de domínio totalmente qualificado
   - Criar uma regra de entrada de grupo de segurança de rede para a porta TCP
- Criar um Aplicativo Azure personalizado para o FortiGate
- Preparar a correspondência de grupo
   - Criar grupos para usuários
- Configurar a VM do FortiGate
   - Instalar a licença
   - Atualizar firmware
   - Alterar a porta de gerenciamento para TCP
   - Carregar o certificado de autenticação SAML do Azure Active Directory
   - Carregar e configurar um certificado SSL personalizado
   - Executar a configuração de linha de comando
   - Criar portais de VPN e a política de firewall
- Testar a entrada usando o Azure

## <a name="redeeming-the-fortigate-license"></a>Como resgatar a licença do FortiGate

O produto Firewall de Próxima Geração Fortinet FortiGate está disponível como uma máquina virtual na IaaS do Azure. Há dois modos de licenciamento para essa máquina virtual:

- Pagamento conforme o uso (PAYG)
- BYOL (Traga sua própria licença)

Na parceria com a Fortinet para fornecer diretrizes sobre SHA (acesso híbrido seguro), ela poderá fornecer licenças aos membros da equipe Ir para SHA de Produção do Azure AD. Nos casos em que nenhuma licença foi fornecida, a implantação do PAYG também funciona.

Nos casos em que uma licença foi emitida, a Fortinet fornece um código de registro que precisa ser resgatado online

![Fortigate SSL VPN](registration-code.png)

1. Registre-se em https://support.fortinet.com/
2. Após o registro, entre em https://support.fortinet.com/
3. Acesse **Ativo** -> **Registrar/Ativar**
4. Insira o Código de Registro fornecido pela Fortinet
5. Especifique o código de registro, selecione **O produto será usado por um usuário não governamental** e clique em **Avançar**
6. Insira uma Descrição do Produto (por exemplo, FortiGate), defina o Parceiro Fortinet como **Outros** -> **Microsoft** e clique em **Avançar**
7. Aceite o **Contrato de Registro do Produto da Fortinet** e clique em **Avançar**
8. Aceite os **Termos** e clique em **Confirmar**
9. Clique em **Download do Arquivo de Licença** e salve a licença para uso posterior


## <a name="download-firmware"></a>Baixar o firmware

No momento da elaboração deste artigo, a VM do Fortinet FortiGate no Azure não era fornecida com a versão do firmware necessária para a autenticação SAML. A última versão precisa ser obtida com a Fortinet.

1. Entre em https://support.fortinet.com/
2. Acesse **Baixar** -> **Imagens de Firmware**
3. Clique em **Baixar** à direita das **Notas sobre a versão**
4. Clique em **v6.**
5. Clique em **6.**
6. Clique em **6.4.**
7. Baixe **FGT_VM64_AZURE-v6-build1637-FORTINET.out** clicando no link **HTTPS** na mesma linha
8. Salve o arquivo para uso posterior


## <a name="deploy-the-fortigate-vm"></a>Implantar a VM do FortiGate

1. Navegue até https://portal.azure.com e entre na assinatura na qual deseja implantar a Máquina Virtual do FortiGate
2. Crie um grupo de recursos ou abra o grupo de recursos no qual deseja implantar a Máquina Virtual do FortiGate
3. Clique em **Adicionar**
4. Insira “Forti” na caixa de diálogo **Pesquisar no Marketplace** e escolha **Firewall de Próxima Geração** **Fortinet FortiGate**
5. Selecione o plano de software (BYOL, se tiver uma licença, ou PAYG, caso contrário) e clique em **Criar**
6. Preencher a configuração da VM

    ![Fortigate SSL VPN](virtual-machine.png)

7. Defina o Tipo de autenticação como **Senha** e forneça credenciais administrativas para a VM
8. Clique em **Examinar + Criar**
9. Clique em **Criar**
10. Aguarde até a implantação da VM ser concluída


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Definir um endereço IP público estático e atribuir um nome de domínio totalmente qualificado

Para uma experiência de usuário consistente, o ideal é definir o endereço IP público atribuído à VM do FortiGate para que ele seja atribuído estaticamente. Além disso, o mapeamento dele para um nome de domínio totalmente qualificado também é útil pelos mesmos motivos.

_Definir um endereço IP público estático_

1. Navegue até https://portal.azure.com e abra as configurações da VM do FortiGate
2. Na tela **Visão Geral**, clique no endereço IP público

    ![Fortigate SSL VPN](public-ip-address.png)

3. Clique em **Estático** e em **Salvar**

_Atribuir um nome de domínio totalmente qualificado_

Se você tiver um nome de domínio publicamente roteável para o ambiente no qual a VM do FortiGate está sendo implantada, crie um registro de host (A) para a VM que seja mapeado para o endereço IP público atribuído estaticamente acima.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Criar uma regra de entrada de grupo de segurança de rede para a porta TCP

1. Navegue até https://portal.azure.com e abra as configurações da VM do FortiGate
2. Clique em **Rede** no menu à esquerda. O adaptador de rede será listado, e as regras de porta de entrada serão exibidas
3. Clique em **Adicionar regra de porta de entrada**
4. Crie uma regra de porta de entrada para TCP 8443

    ![Fortigate SSL VPN](port-rule.png)

5. Clique em **Adicionar**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Criar um Aplicativo Azure personalizado para o FortiGate

1. Navegue até https://portal.azure.com e abra a folha do Azure Active Directory do locatário que fornecerá a identidade para as entradas do FortiGate
2. Clique em **Aplicativos Empresariais** no menu à esquerda
3. Clique em **Novo Aplicativo**
4. Clique em **Aplicativo inexistente na galeria**
5. Forneça um nome (por exemplo, FortiGate) e clique em **Adicionar**
6. Clique em **Usuários e grupos** no menu à esquerda
7. Adicione os usuários que poderão se conectar e clique em **Atribuir**
8. Clique em **Logon único** no menu à esquerda
9. Clique em **SAML**
10. Em **Configuração Básica do SAML**, clique no lápis para editar a configuração
11. Configurar
    - Identificador (ID da Entidade) como `https://<address>/remote/saml/metadata`
    - URL de Resposta (URL do Serviço do Consumidor de Declaração) como `https://<address>/remote/saml/login`
    - URL de Logoff como `https://<address>/remote/saml/logout`

    Em que `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

    Registre cada uma dessas URLs para uso posterior:

    - ID da Entidade
    - URL de resposta
    - URL de logoff
12. Clique em **Salvar**
13. Feche a Configuração Básica do SAML
14. Em **3 – Certificado de Autenticação SAML**, baixe o **Certificado (Base64)** e salve-o para uso posterior
15. Em **4 – Configurar (Nome do Aplicativo)** , copie a URL de Logon do Azure, o Identificador do Azure AD e a URL de Logoff do Azure e salve-os para uso posterior
    - URL de Logon do Azure
    - Identificador do Azure AD
    - URL de Logoff do Azure
16. Em **2 – Declarações e Atributos de Usuário**, clique no lápis para editar a configuração
17. Clique em **Adicionar nova declaração**
18. Defina o Nome como **nomedeusuário**
19. Defina o atributo Origem como **user.userprincipalname**
20. Clique em **Salvar**
21. Clique em **Adicionar uma declaração de grupo**
22. Selecione **Todos os grupos**
23. Marque **Personalizar o nome da declaração de grupo**
24. Defina o Nome como **grupo**
25. Clique em **Salvar**


## <a name="prepare-for-group-matching"></a>Preparar a correspondência de grupo

O FortiGate permite diferentes experiências do portal do usuário após a entrada baseada na associação a um grupo. Por exemplo, pode haver uma experiência para o grupo de Marketing e outra para o grupo de Finanças.

Configure isso da seguinte maneira:

### <a name="create-groups-for-users"></a>Criar grupos para usuários

1. Navegue até https://portal.azure.com e abra a folha do Azure Active Directory do locatário que fornecerá a identidade para as entradas do FortiGate
2. Clique em **Grupos**
3. Clique em **Novo Grupo**
4. Crie um grupo com
    - Tipo de grupo = Segurança
    - Nome do grupo = `a meaningful name`
    - Descrição do grupo = `a meaningful description for the group`
    - Tipo de associação = Atribuído
    - Membros = `users for the user experience that will map to this group`
5. Repita as etapas 3 e 4 para qualquer experiência do usuário adicional
6. Depois que os grupos forem criados, selecione cada grupo e registre a ID do Objeto de cada um deles
7. Salve essas IDs do Objeto e os nomes dos grupos para uso posterior


## <a name="configure-the-fortigate-vm"></a>Configurar a VM do FortiGate

### <a name="install-the-license"></a>Instalar a licença

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Continue após os erros de certificado
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
4. Se a implantação usar o modelo BYOL, será mostrado um prompt que solicitará o upload de uma licença. Selecione o arquivo de licença criado anteriormente e carregue-o, clique em **OK** e reinicie a VM do FortiGate:

    ![Fortigate SSL VPN](license.png)

5. Após a reinicialização, conecte-se novamente com as credenciais de administrador para validar a licença

### <a name="update-firmware"></a>Atualizar firmware

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Continue após os erros de certificado
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
4. No menu à esquerda, clique em **Sistema**
5. No menu à esquerda, em Sistema, clique em **Firmware**
6. Na página Gerenciamento de Firmware, clique em **Procurar** e selecione o arquivo de firmware baixado anteriormente
7. Ignore o aviso e clique em **Configuração de backup e atualização**:

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. Clique em **Continuar**
9. Quando precisar salvar a configuração do FortiGate (como um arquivo .conf), clique em **Salvar**
10. Aguarde até o firmware ser carregado, de modo que ele seja aplicado e a VM do FortiGate seja reinicializada
11. Após a reinicialização da VM do FortiGate, conecte-se novamente com as credenciais de administrador
12. Quando precisar executar a Configuração do Painel, clique em **Mais tarde**
13. Quando o vídeo do tutorial for iniciado, clique em **OK**

### <a name="change-the-management-port-to-tcp"></a>Alterar a porta de gerenciamento para TCP

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Continue após os erros de certificado
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
4. No menu à esquerda, clique em **Sistema**
5. Em Configurações de Administração, altere a porta HTTPS para **8443**
6. Clique em **Aplicar**
7. Depois que a alteração for aplicada, o navegador tentará recarregar a página Administração, mas falhará. De agora em diante, o endereço da página de administração será `https://<address>`

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Carregar o certificado de autenticação SAML do Azure Active Directory

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Continue após os erros de certificado
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
4. No menu à esquerda, clique em **Sistema**
5. Em Sistema, clique em **Certificados**
6. Clique em **Importar** -> **Certificado Remoto**
7. Procure o certificado baixado da implantação de aplicativo personalizado do FortiGate no locatário do Azure, selecione-o e clique em **OK**

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Carregar e configurar um certificado SSL personalizado

O ideal é configurar a VM do FortiGate com o próprio certificado SSL que dê suporte ao FQDN que está sendo usado. Se você tiver acesso a um certificado SSL empacotado com a chave privada no formato .pfx, ele poderá ser usado para essa finalidade

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Continue após os erros de certificado
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
4. No menu à esquerda, clique em **Sistema**
5. Em Sistema, clique em **Certificados**
6. Clique em **Importar** -> **Certificado Local**
7. Clique em **Certificado PKCS nº 12**
8. Procure o arquivo .pfx que contém o certificado SSL e a chave privada
9. Insira a senha do .pfx
10. Forneça um nome significativo para o certificado
11. Clique em **OK**
12. No menu à esquerda, clique em **Sistema**
13. Em Sistema, clique em **Configurações**
14. Em Configurações de Administração, expanda a lista suspensa ao lado do certificado do servidor HTTPS e escolha o certificado SSL importado acima
15. Clique em **Aplicar**
16. Feche a janela do navegador e navegue novamente até `https://<address>`
17. Conecte-se com as credenciais de administrador do FortiGate e observe o certificado SSL correto em uso


### <a name="perform-command-line-configuration"></a>Executar a configuração de linha de comando

_Executar a configuração de linha de comando para a autenticação SAML_

1. Navegue até https://portal.azure.com e abra as configurações da VM do FortiGate
2. No menu à esquerda, clique em **Console Serial**
3. Entre no console serial com as credenciais de administrador da VM do FortiGate

    Para a próxima etapa, as URLs registradas anteriormente serão necessárias. Especificamente:

    - ID da Entidade
    - URL de resposta
    - URL de logoff
    - URL de Logon do Azure
    - Identificador do Azure AD
    - URL de Logoff do Azure
4. No console serial, execute os seguintes comandos:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > A URL de Logoff do Azure contém um caractere ?. Isso exige uma sequência de chave especial para que ela seja fornecida corretamente ao console serial do FortiGate. Normalmente, a URL é:

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Para fornecer isso no console serial, continue digitando

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Em seguida, digite CTRL + V,

    Depois, cole o restante da URL em para completar a linha

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Para confirmar a configuração, execute:

    ```
    show user saml
    ```

_Executar a configuração de linha de comando para a correspondência de grupo_

1. Navegue até https://portal.azure.com e abra as configurações da VM do FortiGate
2. No menu à esquerda, clique em **Console Serial**
3. Entre no console serial com as credenciais de administrador da VM do FortiGate
4. No console serial, execute os seguintes comandos:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Repita esses comandos em Editar `group 1 name`, mas para cada grupo adicional que terá uma experiência de portal diferente no FortiGate

_Executar a configuração de linha de comando para o tempo limite de autenticação_

1. Navegue até https://portal.azure.com e abra as configurações da VM do FortiGate
2. No menu à esquerda, clique em **Console Serial**
3. Entre no console serial com as credenciais de administrador da VM do FortiGate
4. No console serial, execute os seguintes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Criar portais de VPN e a política de firewall

1. Navegue até `https://<address>`

    aqui, `address` é o FQDN ou o endereço IP público atribuído à VM do FortiGate

2. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate
3. No menu à esquerda, clique em **VPN**
4. Em VPN, clique em **Portais de SSL-VPN**
5. Clique em **Criar**
6. Forneça um nome (geralmente correspondendo-o ao Grupo do Azure usado para fornecer a experiência de portal personalizada)
7. Clique no sinal de adição ( **+** ) ao lado de Pools de IP de Origem, selecione o pool padrão e clique em **Fechar**
8. Personalize a experiência para esse grupo. Como teste, isso poderá ser a personalização da Mensagem do Portal e do Tema. Esse também é o local em que você pode criar indicadores personalizados que direcionam os usuários para os recursos internos
9. Clique em **OK**
10. Repita as etapas 5 a 9 para cada Grupo do Azure que terá uma experiência de portal personalizada
11. Em VPN, clique em **Configurações de SSL-VPN**
12. Clique no sinal de adição ( **+** ) ao lado de Escutar Interfaces
13. Selecione **Port1** e clique em **Fechar**


14. Se um certificado SSL personalizado tiver sido instalado anteriormente, altere o Certificado do Servidor para usar o certificado SSL personalizado no menu suspenso
15. Em Autenticação/Mapeamento do Portal, clique em **Criar**
16. Escolha o primeiro Grupo do Azure e corresponda-o ao Portal de mesmo nome
17. Clique em **OK**
18. Repita as etapas 15 a 17 para cada par de Grupo do Azure/Portal
19. Em Autenticação/Mapeamento do Portal, edite **Todos os Outros Usuários/Grupos**
20. Defina o portal como **Acesso completo**
21. Clique em **OK**
22. Clique em **Aplicar**
23. Role a página até a parte superior da página Configuração de SSL-VPN e clique no aviso **Não há nenhuma política de SSL-VPN**
     **. Clique aqui para criar uma política de SSL-VPN usando essas configurações**
24. Forneça um nome, como **VPN Grp**
25. Defina Interface de Saída como **Porta**
26. Clique em **Origem**
27. Em Endereço, selecione **Tudo**
28. Em Usuário, selecione o primeiro Grupo do Azure
29. Clique em **Fechar**
30. Clique em **Destino**
31. Em Endereço, normalmente, ele será a rede interna. Selecione login.microsoft.com para o teste
32. Clique em **Fechar**
33. Clique em **Serviço**
34. Clique em **Todos**
35. Clique em **Fechar**
36. Clique em **OK**
37. No menu à esquerda, clique em **Política e Objetos**
38. Em Política e Objetos, clique em **Política de Firewall**
39. Expanda **Interface de túnel SSL-VPN (ssl.root) -> porta**
40. Clique com o botão direito do mouse na política de VPN criada anteriormente (**VPN Grp 1**) e escolha **Copiar**
41. Clique com o botão direito do mouse na política de VPN e escolha **Colar** -> **Abaixo**
42. Edite a nova política, fornecendo a ela um nome diferente (digamos, **VPN Grp2**) e alterando o grupo é aplicável a (outro Grupo do Azure)
43. Clique com o botão direito do mouse na nova política e defina o status como **Habilitado**


## <a name="test-sign-in-using-azure"></a>Testar a entrada usando o Azure

1. Usando uma sessão do navegador anônima, navegue até `https://<address>` 
2. A entrada deverá redirecionar você para o Azure Active Directory para a entrada
3. Depois que você fornecer credenciais para um usuário que foi atribuído ao Aplicativo FortiGate no locatário do Azure, o portal do usuário apropriado será mostrado

    ![Fortigate SSL VPN](test-sign-in.png)
