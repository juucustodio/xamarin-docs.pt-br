---
title: "Log de depuração do Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 26ac42e4b7acbe19dee746130fc335fdf18ffc46
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="android-debug-log"></a>Log de depuração do Android

## <a name="android-debug-log-overview"></a>Visão geral do log de depuração do Android

Um truque muito comum que os desenvolvedores usam para depurar seus aplicativos é fazer chamadas para o `Console.WriteLine`. No entanto, em uma plataforma móvel como Android não há console. Dispositivos Android fornecem um log que você pode utilizar ao gravar aplicativos. Ele às vezes é chamado de 'logcat', devido ao comando digitado para recuperá-lo.

## <a name="accessing-from-visual-studio"></a>Acessando do Visual Studio

Os blocos de log do Android e do iOS são unificados no Visual Studio 2015 e versões posteriores.

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 e 2017

A nova janela de ferramentas de Log do Dispositivo do Visual Studio mostra os logs de dispositivos Android e iOS. Ela pode ser exibida executando qualquer um dos seguintes comandos: 

-   **Exibir -> Outras Janelas -> Log do Dispositivo**
-   **Ferramentas -> Android -> Log do Dispositivo**
-   **Barra de ferramentas do Android -> Log do Dispositivo**

Quando a janela de ferramentas for exibida, o dispositivo físico poderá ser selecionado na caixa de combinação de dispositivos. Quando o dispositivo for selecionado, ele começará automaticamente a adicionar entradas de log de um aplicativo em execução na tabela. Alternar entre dispositivos parará e iniciará o registro em log do dispositivo. Para que os dispositivos sejam exibidos na caixa de combinação, um projeto do Android deve ser carregado. Se o dispositivo não aparecer na caixa de combinação, verifique primeiro se ele está disponível na lista suspensa Iniciar Depuração. 

Essa janela de ferramentas fornece: uma tabela de entradas de log, uma caixa de combinação para a seleção de dispositivo, uma maneira de limpar entradas de log, uma caixa de pesquisa e botões reproduzir/parar/pausar. 



## <a name="accessing-from-the-command-line"></a>Acessando da Linha de Comando

Outra opção para exibir o log de depuração é por meio da linha de comando. Abra uma janela do console e navegue até a pasta de ferramentas de plataforma do SDK do Android (como **C:\android-sdk-windows\platform-tools**). 

Se apenas um dispositivo estiver conectado, o log poderá ser visualizado com:

```shell
$ adb logcat
```

Se mais de um dispositivo estiver conectado, o dispositivo deverá ser identificado. Por exemplo, `adb -d logcat` mostra o log do único dispositivo físico conectado, enquanto que `adb -e logcat` mostra o log do único emulador em execução. 

Para encontrar mais comandos é só executar o **adb**.



## <a name="writing-to-the-debug-log"></a>Gravar no Log de Depuração

As mensagens podem ser gravadas no Log de Depuração usando os métodos na classe [Android.Util.Log](https://developer.xamarin.com/api/type/Android.Util.Log/): 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Isso produz:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```


## <a name="interesting-messages"></a>Mensagens interessantes

Ao ler o log e, especialmente, ao fornecer trechos de log para outras pessoas (pois fornecer o arquivo de log completo é (1) um exagero e (2) é ilegível), a linha *mais importante* para começar é uma linha que se assemelha a:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

Especificamente, uma linha que corresponde à expressão regular:

```shell
^I.*ActivityManager.*Starting: Intent
```

e que contenha o nome do pacote de aplicativos. Esta é a linha que corresponde ao início de uma atividade e *a maior parte* das mensagens a seguir (mas não todas) deve estar relacionada ao aplicativo. 

Em particular, cada mensagem contém o PID (identificador de processo) do processo de geração de mensagem. Na mensagem `ActivityManager` acima, o processo `12944` gerou a mensagem. Para determinar qual processo é o processo do aplicativo que está sendo depurado, procure a mensagem mono.MonoRuntimeProvider: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Essa mensagem é proveniente do processo que foi iniciado e todas as mensagens seguintes que contêm o mesmo pid, vêm do mesmo processo. 
