---
title: Tutorial para usar sinalizadores de recursos em um aplicativo Spring Boot – Configuração de Aplicativos do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a implementar sinalizadores de recursos em aplicativos Spring Boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687196"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Tutorial: Usar sinalizadores de recursos em um aplicativo Spring Boot

As bibliotecas de Gerenciamento de Recursos do Spring Boot Core oferecem suporte para implementar sinalizadores de recursos em um aplicativo Spring Boot. Essas bibliotecas permitem que você adicione sinalizadores de recursos ao código de forma declarativa.

As bibliotecas do Gerenciamento de Recursos também gerenciam os ciclos de vida dos sinalizadores de recursos nos bastidores. Por exemplo, as bibliotecas atualizam e armazenam em cache os estados do sinalizador ou garantem um estado de sinalizador imutável durante uma chamada de solicitação. Além disso, a biblioteca do Spring Boot oferece integrações, incluindo ações, rotas e middleware do controlador MVC.

O [Início rápido Adicionar sinalizadores de recursos a um aplicativo Spring Boot](./quickstart-feature-flag-spring-boot.md) mostra várias maneiras de adicionar sinalizadores de recursos em um aplicativo Spring Boot. Este tutorial explica esses métodos mais detalhadamente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Adicionar sinalizadores de recursos aos componentes principais de seu aplicativo para controlar a disponibilidade de recursos.
> * Integre-os à Configuração de Aplicativos ao usá-la para gerenciar sinalizadores de recursos.

## <a name="set-up-feature-management"></a>Configurar o gerenciamento de recursos

O gerenciador de recursos `FeatureManager` do Spring Boot obtém os sinalizadores de recursos do sistema de configuração nativo da estrutura. Como resultado, é possível definir os sinalizadores de recursos do aplicativo usando qualquer fonte de configuração compatível com o Spring Boot, incluindo o arquivo *bootstrap.yml* local ou variáveis de ambiente. O `FeatureManager` depende da injeção de dependência. Você pode registrar os serviços de gerenciamento de recursos usando convenções padrão:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Recomendamos que você mantenha os sinalizadores de recursos fora do aplicativo e gerencie-os separadamente. Isso permite que você modifique os estados do sinalizador a qualquer momento e que essas alterações entrem em vigor no aplicativo imediatamente. A Configuração de Aplicativos fornece um local centralizado para organizar e controlar todos os sinalizadores de recursos por meio de uma interface do usuário dedicada do portal. A Configuração de Aplicativos também fornece os sinalizadores ao aplicativo diretamente por meio de suas bibliotecas de clientes do Spring Boot.

A maneira mais fácil de conectar seu aplicativo Spring Boot à Configuração de Aplicativos é por meio do provedor de configuração:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaração do sinalizador de recurso

Cada sinalizador de recurso tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de um recurso é *ativado* (ou seja, quando seu valor é `True`). Um filtro define um caso de uso para os casos em que um recurso deve ser ligado.

Quando um sinalizador de recursos tem vários filtros, a lista de filtro é percorrida na ordem até um dos filtros determinar que o recurso deve ser habilitado. Neste ponto, o sinalizador de recursos é *ativado* e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que o recurso deve ser habilitado, o sinalizador de recursos será *desativado*.

O gerenciador de recursos dá suporte ao *application.yml* como uma fonte de configuração para sinalizadores de recursos. O seguinte exemplo mostra como configurar os sinalizadores de recursos em um arquivo YAML:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Por convenção, a seção `feature-management` deste documento YAML é usada para as configurações de sinalizadores de recursos. O exemplo anterior mostra três sinalizadores de recursos com seus filtros definidos na propriedade `EnabledFor`:

* `FeatureA` está *ativado*.
* `FeatureB` está *desativado*.
* `FeatureC` especifica um filtro chamado `Percentage` com uma propriedade `Parameters`. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% de que o sinalizador `FeatureC` esteja *ativado*.

## <a name="feature-flag-checks"></a>Verificações de sinalizadores de recursos

O padrão básico do gerenciamento de recursos é primeiro verificar se um sinalizador de recursos está definido como *ativado*. Nesse caso, em seguida, o gerenciador de recursos executa as ações contidas pelo recurso. Por exemplo:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No Spring Boot, é possível acessar o gerenciador de recursos `FeatureManager` por meio da injeção de dependência:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Ações do controlador

Em controladores MVC, use o atributo `@FeatureGate` para controlar se uma ação específica está habilitada. A seguinte ação `Index` exige que `FeatureA` esteja *ativado* para ser executada:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Quando uma ação ou um controlador MVC está bloqueado devido ao sinalizador de recursos controlador estar *desativado*, uma interface `IDisabledFeaturesHandler` registrada é chamada. A interface `IDisabledFeaturesHandler` padrão retorna um código de status 404 para o cliente sem nenhum corpo de resposta.

## <a name="mvc-filters"></a>Filtros do MVC

Configure filtros do MVC de modo que eles sejam ativados com base no estado de um sinalizador de recursos. O exemplo a seguir adiciona um filtro do MVC chamado `FeatureFlagFilter`. Esse filtro só é disparado no pipeline do MVC se `FeatureA` está habilitado.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Rotas

É possível usar sinalizadores de recursos para redirecionar rotas. O código a seguir que redirecionará um usuário de `FeatureA` está habilitado:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a implementar sinalizadores de recursos no aplicativo Spring Boot usando as bibliotecas `spring-cloud-azure-feature-management-web`. Para obter mais informações sobre o suporte de gerenciamento de recursos no Spring Boot e na Configuração de Aplicativos, confira os seguintes recursos:

* [Código de exemplo do sinalizador de recursos do Spring Boot](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gerenciar sinalizadores de recursos](./manage-feature-flags.md)
