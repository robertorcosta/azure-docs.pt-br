---
title: Gerenciar dependências do JAR – Azure HDInsight
description: Este artigo discute as práticas recomendadas para o gerenciamento de dependências de arquivo Java (JAR) para aplicativos do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135727"
---
# <a name="jar-dependency-management-best-practices"></a>Práticas recomendadas de gerenciamento de dependências JAR

Os componentes instalados em clusters HDInsight têm dependências em bibliotecas de terceiros. Normalmente, uma versão específica de módulos comuns, como guava, é referenciada por esses componentes internos. Quando você envia um aplicativo com suas dependências, ele pode causar um conflito entre versões diferentes do mesmo módulo. Se a versão do componente que você referencia no classpath primeiro, os componentes internos podem gerar exceções devido à incompatibilidade de versão. No entanto, se os componentes internos injetarem suas dependências primeiro ao classpath, seu aplicativo poderá gerar `NoSuchMethod`erros como.

Para evitar o conflito de versão, considere sombrear suas dependências de aplicativo.

## <a name="what-does-package-shading-mean"></a>O que significa o sombreamento do pacote?
O sombreamento fornece uma maneira de incluir e renomear dependências. Ele realoca as classes e reescreve o código de bytes e os recursos afetados para criar uma cópia privada de suas dependências.

## <a name="how-to-shade-a-package"></a>Como sombrear um pacote?

### <a name="use-uber-jar"></a>Usar Uber-jar
Uber-jar é um único arquivo JAR que contém o JAR do aplicativo e suas dependências. As dependências em Uber-jar são, por padrão, não sombreadas. Em alguns casos, isso poderá introduzir conflito de versão se outros componentes ou aplicativos fizerem referência a uma versão diferente dessas bibliotecas. Para evitar isso, você pode criar um arquivo Uber-jar com algumas (ou todas) das dependências sombreadas.

### <a name="shade-package-using-maven"></a>Sombrear pacote usando o Maven
O Maven pode criar aplicativos escritos em Java e escalares. O plug-in Maven-Shad-plugin pode ajudá-lo a criar facilmente um Uber-jar sombreado.

O exemplo a seguir mostra um `pom.xml` arquivo que foi atualizado para sombrear um pacote usando o plug-in Maven-Shad-plugin.  A seção `<relocation>…</relocation>` XML move classes do pacote `com.google.guava` para o `com.google.shaded.guava` pacote movendo as entradas de arquivo JAR correspondentes e reescrevendo o código de bytes afetado.

Após a `pom.xml`alteração, você pode `mvn package` executar para criar o uber-jar sombreado.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Sombrear pacote usando SBT
O SBT também é uma ferramenta de compilação para escala e Java. SBT não tem um plug-in de sombreador como Maven-Shad-plugin. Você pode modificar `build.sbt` o arquivo para sombrear pacotes. 

Por exemplo, para sombrear `com.google.guava`, você pode adicionar o comando abaixo ao `build.sbt` arquivo:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Em seguida, você `sbt clean` pode `sbt assembly` executar e criar o arquivo JAR sombreado. 

## <a name="next-steps"></a>Próximas etapas

* [Usar as ferramentas de IntelliJ do HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Criar um aplicativo de escala do Maven para Spark no IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
