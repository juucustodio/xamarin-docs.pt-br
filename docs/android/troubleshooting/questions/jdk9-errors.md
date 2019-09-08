---
title: Xamarin. Android e Java Development Kit 9
description: Este artigo explica como resolver erros do Java Development Kit (JDK) 9 ou posteriores no Xamarin. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 58b1b29a34bfb03661959af4dea8ed57b8f504cc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760861"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android e Java Development Kit 9 ou posterior

_Este artigo explica como resolver erros do Java Development Kit (JDK) 9 ou posteriores no Xamarin. Android._

## <a name="overview"></a>Visão geral

O Xamarin. Android usa o Java Development Kit (JDK) para integrar com o SDK do Android para a criação de aplicativos Android e a execução do Android designer. As versões mais recentes do SDK do Android (API 24 e superior) exigem o JDK 8 (1,8) ou a versão prévia do Microsoft Mobile OpenJDK. **Como as ferramentas de SDK do Android disponíveis no Google ainda não são compatíveis com o JDK 9, o Xamarin. Android não funciona com o JDK 9 ou posterior.**

## <a name="jdk-errors"></a>Erros do JDK

Se você tentar criar um projeto Xamarin. Android com uma versão do JDK posterior a JDK 8, receberá um erro explícito indicando que não há suporte para essa versão do JDK. Por exemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Para resolver esses erros, você deve instalar o JDK 8 (1,8), conforme explicado em [como fazer atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
Como alternativa, você pode instalar o [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) . o Microsoft Mobile OpenJDK eventualmente substituirá o JDK 8 para desenvolvimento do Xamarin. Android.

## <a name="checking-the-jdk-version"></a>Verificando a versão do JDK

Você pode verificar para ver qual versão do Java você instalou inserindo o seguinte comando (o diretório do `bin` JDK deve estar no seu `PATH`):

```shell
java -version
```

Se o JDK 9 estiver instalado, você verá uma mensagem semelhante à seguinte:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se o JDK 9 ou posterior estiver instalado, você deverá instalar o Java JDK 8 (1,8) ou a versão prévia do Microsoft Mobile OpenJDK. Para obter informações sobre como instalar o JDK 8, consulte [como fazer atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Para obter informações sobre como instalar o Microsoft Mobile OpenJDK, consulte [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Observe que você não precisa desinstalar uma versão mais recente do JDK; no entanto, você deve garantir que o Xamarin esteja usando o JDK 8 em vez de uma versão posterior do JDK. No Visual Studio, clique em **ferramentas > opções > Xamarin > configurações do Android**. Se o **local do Java Development Kit** não estiver definido como um local do JDK 8 (como **C:\\\\arquivos\\de programas Java JDK 1.8.0 _111**), clique em **alterar** e defina-o como o local onde o JDK 8 está instalado. Em Visual Studio para Mac, navegue até **preferências > projetos > locais do sdk > o Android > Java SDK (JDK)** e clique em **procurar** para atualizar este caminho.

## <a name="known-issues-with-jdk-9"></a>Problemas conhecidos com o JDK 9

### <a name="apksigner"></a>apksigner

Há um problema conhecido com apksigner e JDK `apksigner.bat` 9 no qual o arquivo invoca o `apksigner.jar` com `-Djava.ext.dirs` em vez de `-classpath` qual JDK 9 espera. É recomendável usar o JDK 8 (1,8). Para obter informações sobre como instalar o JDK 8, consulte [como fazer atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Se você instalou o JDK 9, verifique se o caminho a seguir não está definido em `PATH` sua variável de ambiente, pois ele ainda apontará para `C:\ProgramData\Oracle\Java\javapath`o JDK 9:. Depois de removê- `java-version` lo, em uma linha de comando deve mostrar JDK 8.
