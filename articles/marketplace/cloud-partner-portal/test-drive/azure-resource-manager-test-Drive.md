---
title: Azure Resource Manager Test Drive | Azure Marketplace
description: Criar um test drive do Marketplace usando o Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8b2a24b6f2d7df92f1c8ea1b22432471aa432011
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644878"
---
# <a name="azure-resource-manager-test-drive"></a>Test Drive do Azure Resource Manager

Este artigo é para publicadores que têm oferta no Azure Marketplace, ou que estão no AppSource, mas desejam compilar o Test Drive apenas com os recursos do Azure.

Um modelo de Azure Resource Manager (Gerenciador de recursos) é um contêiner codificado de recursos do Azure que você cria para representar melhor sua solução. Se você não estiver familiarizado com o que é um modelo do Resource Manager, leia sobre [noções básicas sobre modelos do Resource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) Manager e criação de modelos do [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para garantir que você saiba como criar e testar seus próprios modelos.

O Test Drive faz o seguinte: pega o modelo do Resource Manager e faz uma implantação de todos os recursos necessários desse modelo do Resource Manager em um grupo de recursos.

Se decidir compilar um Test Drive do Azure Resource Manager, os requisitos serão estes:

- Compilar, testar e, em seguida, fazer upload do seu modelo do Resource Manager para Test Drive.
- Configurar todos os metadados e configurações necessários para habilitar seu Test Drive.
- Republicar sua oferta com o Test Drive habilitado.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Como compilar um Test Drive do Azure Resource Manager

Este é o processo para criar um Azure Resource Manager unidade de teste:

1. Projete o que você deseja que os clientes façam em um diagrama de fluxo.
1. Defina quais experiências você gostaria que seus clientes criassem.
1. Com base nas definições acima, decida quais partes e recursos são necessários para os clientes realizarem essa experiência: por exemplo, instância de D365 ou um site com um banco de dados.
1. Crie o design localmente e teste a experiência.
1. Empacote a experiência em uma implantação de modelo ARM e a partir daí:
    1. Defina quais partes dos recursos são parâmetros de entrada;
    1. Quais são as variáveis;
    1. Quais saídas são dadas à experiência do cliente.
1. Publique, teste e entre em tempo real.

A parte mais importante na hora de compilar um Test Drive do Azure Resource Manager é definir quais cenários você deseja que seus clientes experimentem. Tem um produto de firewall e deseja demonstrar como lida com ataques de injeção de script? Tem um produto de armazenamento e deseja demonstrar como sua solução compacta arquivos de modo rápido e fácil?

Certifique-se de gastar um tempo suficiente avaliando quais são as melhores maneiras de mostrar seu produto. Especificamente, em todos os recursos necessários, você precisaria, pois torna o empacotamento do modelo do Resource Manager suficientemente mais fácil.

Para continuar com nosso exemplo de firewall, a arquitetura talvez exija uma URL de IP público para seu serviço e outra URL de IP público para o site que seu firewall está protegendo. Cada IP é implantado em uma Máquina Virtual e conectado com um grupo de segurança de rede + adaptador de rede.

Depois de criar o pacote de recursos desejado, agora vem a escrita e a criação do modelo test drive do Resource Manager.

## <a name="writing-test-drive-resource-manager-templates"></a>Escrevendo modelos do Resource Manager para Test Drive

O Test Drive executa implantações em um modo totalmente automatizado; por isso, os modelos para Test Drive têm algumas restrições descritas abaixo.

### <a name="parameters"></a>Parâmetros

A maioria dos modelos tem um conjunto de parâmetros. Os parâmetros definem os nomes de recursos, tamanhos de recursos (por exemplo, os tipos de contas de armazenamento ou tamanhos de máquina virtual), nomes de usuários e senhas, nomes DNS e assim por diante. Quando você implanta soluções usando o portal do Azure, pode preencher manualmente todos esses parâmetros, escolher nomes de DNS disponíveis ou nomes de conta de armazenamento e assim por diante.

![Lista de parâmetros em um Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

No entanto, o Test Drive funciona de modo totalmente automático, sem interação humana; portanto, dá suporte apenas a um conjunto limitado de categorias de parâmetros. Se um parâmetro no modelo do Resource Manager para Test Drive não se encaixar em uma das categorias com suporte, será necessário **substituir esse parâmetro por uma variável ou valor constante.**

Você pode usar qualquer nome válido para seus parâmetros; o Test Drive reconhece a categoria do parâmetro usando o valor do tipo de metadados. Você **precisa especificar o tipo de metadados para cada parâmetro do modelo**; caso contrário, o modelo não será aprovado na validação:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Também é importante observar que **todos os parâmetros são opcionais**. Portanto, se não deseja usar algum deles, não precisa.

### <a name="accepted-parameter-metadata-types"></a>Tipos de metadados de parâmetros aceitos

| Tipo de metadados   | Tipo de parâmetro  | Description     | Valor de exemplo    |
|---|---|---|---|
| **baseuri**     | cadeia de caracteres          | URI base do seu pacote de implantação| https:\//\<\..\>. blob.core.windows.net/\<\..\> |
| **username**    | cadeia de caracteres          | Novo nome de usuário aleatório.| admin68876      |
| **password**    | cadeia de caracteres segura    | Nova senha aleatória | Lp!ACS\^2kh     |
| **ID da sessão**   | cadeia de caracteres          | ID da sessão exclusiva do Test Drive (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>Nome de Usuário

O Test Drive inicializa esse parâmetro com um **URI base** do seu pacote de implantação; portanto, você pode usar esse parâmetro para construir o URI de qualquer arquivo incluído no seu pacote.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Dentro do seu modelo, é possível usar esse parâmetro para construir um URI de qualquer arquivo do seu pacote de implantação do Test Drive. O exemplo abaixo mostra como construir um URI do modelo vinculado:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>Nome de Usuário

O Test Drive inicializa esse parâmetro com um novo nome de usuário aleatório:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valor de exemplo:

    admin68876

É possível usar nomes de usuário aleatórios ou constantes para sua solução.

#### <a name="password"></a>password

O Test Drive inicializa esse parâmetro com uma nova senha aleatória:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valor de exemplo:

    Lp!ACS^2kh

É possível usar senhas aleatórias ou constantes para sua solução.

#### <a name="session-id"></a>ID da sessão

O Test Drive inicializa esse parâmetro com um GUID exclusivo que representa a ID da sessão do Test Drive:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Valor de exemplo:

    b8c8693e-5673-449c-badd-257a405a6dee

Você poderá usar esse parâmetro para identificar exclusivamente a sessão de Test Drive se for necessário.

### <a name="unique-names"></a>Nomes exclusivos

Alguns recursos do Azure, como contas de armazenamento ou nomes DNS, requerem nomes globalmente exclusivos.

Isso significa que sempre que o Test Drive implantar o modelo do Resource Manager, será criado um **novo grupo de recursos com um nome exclusivo** para todos os seus\' recursos. Portanto, é necessário usar a função [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) concatenada com seus nomes variáveis nos IDs do grupo de recursos para gerar valores exclusivos aleatórios:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Lembre-se de concatenar suas cadeias de caracteres de parâmetros/variáveis (\'contosovm\') com uma saída de cadeia de caracteres exclusiva (\'resourceGroup().id\'), porque isso garante a exclusividade e a confiabilidade de cada variável.

Por exemplo, a maioria dos nomes de recursos não pode começar com um dígito, mas a função de cadeia de caracteres exclusiva pode devolver uma cadeia de caracteres que começa com um dígito. Portanto, se usar uma saída de cadeia de caracteres exclusiva bruta, suas implantações falharão. 

Você pode encontrar informações adicionais sobre regras e restrições de nomenclatura de recursos em [este artigo](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Local da implantação

Você pode disponibilizar seu Test Drive em diferentes regiões do Azure. A ideia é permitir que um usuário escolha a região mais próxima para proporcionar a melhor experiência do usuário.

Quando cria uma instância do laboratório, o Test Drive sempre cria um grupo de recursos na região escolhida por um usuário; depois, executa seu modelo de implantação nesse contexto de grupo. Portanto, o modelo deve escolher o local da implantação no grupo de recursos:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

E, em seguida, usar esse local para todos os recursos para uma instância de laboratório específica:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Você precisa certificar-se de que sua assinatura tem permissão para implantar todos os recursos que deseja implantar em cada uma das regiões que está selecionando. Além disso, precisa certificar-se de que suas imagens de máquina virtual estão disponíveis em todas as regiões que pretende habilitar; caso contrário, seu modelo de implantação não funcionará para algumas regiões.

### <a name="outputs"></a>outputs

Normalmente, com modelos do Resource Manager, é possível implantar sem produzir nenhuma saída. Isso acontece porque você conhece todos os valores que usa para preencher parâmetros do modelo e pode sempre inspecionar manualmente as propriedades de qualquer recurso.

Entretanto, para modelos do Resource Manager para Test Drive, é importante devolver ao Test Drive todas as informações que são necessárias para obter acesso ao laboratório (URIs do site, nomes do host da Máquina Virtual, nomes de usuário e senhas). Certifique-se de que todos os nomes de saída sejam legíveis, porque essas variáveis são apresentadas ao cliente.

Não existem restrições relacionadas às saídas do modelo. Lembre-se: o Test Drive converte todos os valores de saída em **cadeias de caracteres**. Se você enviar um objeto para a saída, um usuário verá uma cadeia de caracteres do JSON.

Exemplo:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limites de assinatura

Outra coisa que você deve levar em consideração são os limites de assinatura e serviço. Por exemplo, se quiser implantar até dez máquinas virtuais com 4 núcleos, precisará ter certeza de que a assinatura usada para seu laboratório permite o uso de 40 núcleos.

É possível encontrar mais informações sobre os limites de assinatura e serviço do Azure [neste artigo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Como vários Test Drives podem ser feitos ao mesmo tempo, certifique-se de que sua assinatura possa lidar com o \# de núcleos multiplicados pelo número total de Test Drives simultâneos que podem ser realizados.

### <a name="what-to-upload"></a>O que deve ser transferido por upload

O upload do modelo do Resource Manager para Test Drive é feito como arquivo zip, que pode incluir vários artefatos de implantação, mas precisa ter um arquivo denominado **main-template.json**. Esse arquivo é o modelo de implantação do Azure Resource Manager, que o Test Drive utiliza para criar uma instância de laboratório.

Se tiver recursos adicionais além desse arquivo, poderá referenciá-los como recurso externo dentro do modelo ou incluir o recurso no arquivo zip.

Durante a certificação de publicação, o Test Drive descompacta o pacote de implantação e coloca o conteúdo em um contêiner de blob interno do Test Drive. A estrutura do contêiner reflete a estrutura do seu pacote de implantação:

| package.zip                       | Contêiner de blob do Test Drive         |
|---|---|
| main-template.json                | https:\//\<\..\>. blob.core.windows.net/\<\..\>/Main-template.JSON  |
| templates/solution.json           | https:\//\<\..\>. blob.core.windows.net/\<\..\>/templates/Solution.JSON |
| scripts/warmup.ps1                | https:\//\<\..\>. blob.core.windows.net/\<\..\>/scripts/warmup.ps1  |


Chamamos um URI desse contêiner de blob de URI base. Cada revisão do seu laboratório tem o próprio contêiner de blob. Portanto, cada revisão do laboratório tem seu próprio URI base. O Test Drive pode passar um URI base do seu pacote de implantação descompactado para seu modelo por meio de parâmetros do modelo.

## <a name="transforming-template-examples-for-test-drive"></a>Transformando exemplos de modelo para Test Drive

O processo de transformar uma arquitetura de recursos em um modelo do Resource Manager para Test Drive pode ser desanimador. Para ajudar a facilitar esse processo, criamos exemplos da melhor maneira de [transformar os modelos de implantação atuais aqui](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Agora que o seu Test Drive foi criado, esta seção explicará cada um dos campos necessários para publicá-lo com êxito.

![Habilitando o Test Drive na interface do usuário](./media/azure-resource-manager-test-drive/howtopub1.png)

O primeiro campo (e o mais importante) é decidir se quer o Test Drive habilitado para sua oferta ou não. Quando você seleciona **Sim,** o restante do formulário com todos os campos obrigatórios são apresentados para você preencher. Quando você seleciona **não,** o formulário fica desabilitado e, se você republicar com a unidade de teste desabilitada, sua unidade de teste será removida da produção.

Observação: se houver Test Drives ativamente usados por usuários, eles continuarão sendo executados até a sessão expirar.

### <a name="details"></a>Detalhes

A próxima seção para preencher são os detalhes sobre sua oferta de Test Drive.

![Informações detalhadas do Test Drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição –** é *necessário* que você escreva a descrição principal sobre o que está em seu Test Drive. O cliente usará este recurso para ler sobre os cenários relacionados ao produto, incluídos no seu Test Drive. 

**Manual do usuário –** *necessário* este é o passo a passos detalhados de sua experiência de teste de unidade. O cliente vai abri-lo e poderá ver exatamente o que você deseja que ele faça durante seu Test Drive. É importante que o conteúdo seja fácil de entender e seguir! Deve ser um arquivo .pdf.

**Vídeo de demonstração do Test Drive –** *recomendado* de forma semelhante ao manual do usuário, é melhor incluir um tutorial em vídeo de sua experiência de teste de unidade. O cliente vai assisti-lo antes ou durante o Test Drive e poderá ver exatamente o que você deseja que ele faça durante seu Test Drive. É importante que o conteúdo seja fácil de entender e seguir!

- **Nome** – Título do vídeo
- **Link** – Precisa ser uma URL inserida do tubo ou vídeo. A seguir há um exemplo de como obter a URL inserida:
- **Miniatura** ‒ Deve ser uma imagem de alta qualidade em pixels (533 x 324). É recomendável fazer uma captura de tela de alguma parte da sua experiência com o Test Drive aqui.

Veja abaixo como esses campos aparecem para o cliente durante a experiência com o Test Drive.

![Localização dos campos do Test Drive na oferta do Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração Técnica

Na próxima seção para preenchimento, você faz upload do modelo do Resource Manager para Test Drive e define com qual especificidade suas instâncias do Test Drive funcionam.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instâncias –** *obrigatório* é onde você configura quantas instâncias deseja, em quais regiões e a rapidez com que os clientes podem obter o Test Drive.

- **Instâncias** – Em Regiões selecionadas, você escolhe onde seu modelo do Resource Manager para Test Drive será implantado. É recomendável escolher apenas uma região em que espera que a maioria dos seus clientes esteja localizada.
- **Quente** – Número de instâncias do Test Drive que já foram implantadas e estão aguardando acesso por região selecionada. Os clientes podem acessar esses Test Drives instantaneamente em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. É altamente recomendável ter **pelo menos uma instância quente**, pois a maioria dos seus clientes não quer esperar pelo término de implantações completas e ocorre uma diminuição no uso pelos clientes.
- **Morno** – Número de instâncias do Test Drive por região que foram implantadas e, em seguida, a VM foi interrompida e armazenada no armazenamento do Azure. O tempo de espera para instâncias mornas é mais lento do que para instâncias quentes; porém, o custo de tempo de atividade do armazenamento é mais barato.
- **Frio** – Número de instâncias do Test Drive por região que podem ser possivelmente implantadas. As instâncias frias exigem que o modelo do Resource Manager para Test Drive inteiro passe por uma implantação no momento em que um cliente solicita o Test Drive; portanto, são mais lentas do que as instâncias quentes ou mornas. Entretanto, a desvantagem é que você precisa pagar apenas enquanto durar o Test Drive.

Neste momento, calcula-se o número total de possíveis Test Drives simultâneos que você disponibilizará e você verifica se o limite de cota para sua assinatura consegue lidar com essa quantidade simultânea:

**(Número de regiões selecionadas x instâncias quentes) + (Número de regiões selecionadas x instâncias mornas) + (Número de regiões selecionadas x instâncias frias)**

**Duração da unidade de teste (horas) –** duração *exigida* por quanto tempo a unidade de teste permanecerá ativa, em \# de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo.

**Testar o modelo do Resource Manager-** é *necessário* carregar seu modelo do Resource Manager aqui. É o arquivo que você compilou na seção anterior acima. Nomeie o arquivo de modelo principal como "main-Template. JSON" e certifique-se de que seu modelo do Resource Manager contém parâmetros de saída para as principais variáveis que são necessárias. (Deve ser um arquivo. zip)

**Informações de acesso –** *necessárias* depois que um cliente obtém seu Test Drive, as informações de acesso são apresentadas a eles. Essas instruções servem para compartilhar os parâmetros de saída úteis do seu modelo do Resource Manager para Test Drive. Para incluir parâmetros de saída, utilize chaves duplas (por exemplo, **{{outputname}}** ); eles serão inseridos corretamente no local. A formatação da cadeia de caracteres HTML é recomendada aqui para renderizar no front-end.

### <a name="test-drive-deployment-subscription-details"></a>Detalhes da assinatura para implantação do Test Drive

A seção final a ser preenchida deve habilitar a implementação  automática dos Test Drives, conectando sua Assinatura do Azure e do Azure AD (Azure Active Directory).

![Detalhes da assinatura para implantação do Test Drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID de assinatura do Azure-** *necessária* isso concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Caso ainda não tenha uma Assinatura do Azure **separada** apenas para Test Drives, crie uma. Para encontrar as IDs de Assinatura do Azure, entre no portal do Azure e navegue até Assinaturas no menu do lado esquerdo. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Assinaturas do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de locatário do Azure ad –** *obrigatório* se você já tiver uma ID de locatário disponível, poderá encontrá-la abaixo na ID de diretório de\> de propriedades.

![Propriedades do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo Locatário no Azure Active Directory.

![Lista de locatários do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definir a organização, o domínio e o país/região para o locatário do Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Confirmar a seleção](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID de aplicativo Azure ad –** a próxima etapa *necessária* é criar e registrar um novo aplicativo. Usaremos esse aplicativo para realizar operações em sua instância do Test Drive.

1. Navegue até o diretório criado recentemente ou o diretório preexistente e selecione o Azure Active Directory no painel de filtro.
2. Pesquise "Registros de aplicativo" e clique em "Adicionar"
3. Forneça o nome do aplicativo.
4. Selecione o tipo como "Aplicativo Web/API"
5. Forneça qualquer valor na URL de entrada; \'não utilizaremos esse campo.
6. Clique em Criar.
7. Depois que o aplicativo tiver sido criado, acesse Propriedades ‒\> Defina o aplicativo como vários locatários e pressione Salvar.

Clique em Salvar. O último passo é pegar o ID do aplicativo para esse aplicativo registrado e colá-lo aqui, no campo Test Drive.

![Detalhes da ID do aplicativo do Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Como estamos usando o aplicativo para implantar a assinatura, precisamos adicionar o aplicativo como colaborador na assinatura. Estas são as instruções:

1. Navegue até a folha Assinaturas e selecione a assinatura adequada que você usa apenas para o Test Drive.
1. Clique em **Controle de acesso (IAM)** .
1. Clique na guia **atribuições de função** .  ![adicionar um novo](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg) principal de controle de acesso
1. Clique em **Adicionar atribuição de função**.
1. Defina a função como **Colaborador**.
1. Digite o nome do aplicativo do Azure AD e selecione o aplicativo para atribuir a função.
    ![Adicione as permissões](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Save** (Salvar).

**Chave de aplicativo Azure ad-** *obrigatório* o campo final é gerar uma chave de autenticação. Em chaves, adicione uma Descrição da chave, defina a duração para nunca expirar e, em seguida, selecione Salvar. É **importante** evitar ter uma chave expirada, que interromperá seu Test Drive na produção. Copie esse valor e cole-o no campo Test Drive obrigatório.

![Mostra as chaves para o aplicativo Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Próximos passos

Agora que preencheu todos os campos do Test Drive, prossiga e **Republique** sua oferta. Quando o Test Drive passar na certificação, você deve testar extensivamente a experiência do cliente na **versão prévia** da sua oferta. Inicie um Test Drive na interface do usuário; depois, abra sua Assinatura do Azure dentro do portal do Azure e verifique se seus Test Drives estão sendo totalmente implantados de forma correta.

![Portal do Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

É importante cuidar para não excluir nenhuma instância do Test Drive conforme elas são provisionadas para seus clientes. Assim, o serviço do Test Drive removerá automaticamente esses Grupos de Recursos depois que um cliente tiver encerrado.

Quando estiver se sentindo confortável com sua oferta de versão prévia, é hora de **ativar**! Há um processo de revisão final da Microsoft depois que a oferta é publicada para nova verificação da experiência inteira, de ponta a ponta. Se, por algum motivo, a oferta for rejeitada, enviaremos uma notificação ao contato de engenharia da sua oferta, explicando o que precisará ser corrigido.

Se você tiver mais dúvidas, estiver procurando ajuda para a solução de problemas ou quiser um test drive de maior sucesso, acesse [FAQ, Troubleshooting, & Best Practices](./marketing-and-best-practices.md) (Perguntas frequentes, solução de problemas e práticas recomendadas).
