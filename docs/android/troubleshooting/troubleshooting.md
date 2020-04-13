---
title: Dicas de solução de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 6d83afa47c459633506736b2497a82c444352c90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303914"
---
# <a name="troubleshooting-tips"></a>Dicas de solução de problemas

## <a name="getting-diagnostic-information"></a>Obtendo informações de diagnóstico

Xamarin.Android tem alguns lugares para procurar ao rastrear vários bugs.
Eles incluem:

1. Saída mSBuild de diagnóstico.
2. Registros de implantação de dispositivos.
3. Saída de log de depuração do Android.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Saída de MSBuild de diagnóstico

O MSBuild de diagnóstico pode conter informações adicionais relacionadas à construção de pacotes e pode conter algumas informações de implantação do pacote.

Para habilitar os resultados de diagnóstico do MSBuild no Visual Studio para Mac:

1. Clique **em Ferramentas > Opções...**
2. Na visualização da árvore à esquerda, selecione **Projetos e Soluções > Build and Run**
3. No painel à direita, defina a verbosidade de saída de compilação do MSBuild para Diagnóstico
4. Clique em **OK**.
5. Limpe e recompile o seu pacote.
6. A saída de diagnóstico é visível dentro do painel Saída.

Para habilitar a saída de mSBuild de diagnóstico no Visual Studio para Mac/OS X:

1. Clique **em Visual Studio para Mac > Preferências...**
2. Na visão da árvore à esquerda, selecione **Projetos > Construir**
3. No painel à direita, defina a verbosidade de log para baixo para Diagnóstico
4. Clique em **OK**.
5. Reiniciar o Visual Studio para Mac
6. Limpe e recompile o seu pacote.
7. A saída de diagnóstico é visível no Bloco de erros **(Exibir > Pads > Erros),** clicando no botão 'Saída de compilação'.

## <a name="device-deployment-logs"></a>Logs de implantação de dispositivos

Para permitir o registro de implantação de dispositivos no Visual Studio:

1. **Ferramentas > Opções...**>
2. Na visualização da árvore à esquerda, selecione **Xamarin > Configurações do Android**
3. No painel à direita, habilite a caixa de seleção de depuração de extensão [X] **(grava monodroid.log na sua área de trabalho).**
4. As mensagens de log são gravadas no arquivo monodroid.log na sua área de trabalho.

O Visual Studio for Mac sempre grava registros de implantação de dispositivos. FInding-los é um pouco mais difícil; um arquivo de log *AndroidUtils* é criado para todos os dias + hora em que ocorre uma implantação, por exemplo: **AndroidTools-2012-10-24_12-35-45.log**.

- No Windows, os arquivos `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`de log são gravados para .
- No OS X, os `$HOME/Library/Logs/XamarinStudio-{VERSION}`arquivos de log são gravados para .

## <a name="android-debug-log-output"></a>Saída de log de depuração do Android

O Android escreverá muitas mensagens para o [Registro de Depuração do Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android usa propriedades do sistema Android para controlar a geração de mensagens adicionais para o Registro de Depuração do Android. As propriedades do sistema Android podem ser definidas através do comando *setprop* dentro da [Ponte de Depuração do Android (adb):](https://developer.android.com/guide/developing/tools/adb.html)

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

As propriedades do sistema são lidas durante a inicialização do processo e, portanto, devem ser definidas antes do aplicativo ser iniciado ou o aplicativo deve ser reiniciado após a alteração das propriedades do sistema.

### <a name="xamarinandroid-system-properties"></a>Propriedades do sistema Xamarin.Android

Xamarin.Android suporta as seguintes propriedades do sistema:

- *debug.mono.debug*: Se uma seqüência não `*mono-debug*`vazia, isso é equivalente a .

- *debug.mono.env*: Uma lista separada*|* de tubos (' ') de variáveis de ambiente para exportar durante a inicialização do aplicativo, *antes* da inicialização do mono. Isso permite definir variáveis de ambiente que controlam o registro mono.

  > [!NOTE]
  > Uma vez que*|* o valor é ' 'separado, o valor deve \`ter um nível extra de cotação, pois o comando *adb shell* \` removerá um conjunto de cotações.

  > [!NOTE]
  > Os valores de propriedade do sistema Android não podem ter mais de 92 caracteres de comprimento.

  Exemplo:

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug.mono.log*: Uma lista de componentes separados por comuma ('*,*') que devem imprimir mensagens adicionais para o Registro de depuração do Android. Por padrão, nada está definido. Os componentes incluem:

  - *todos*: Imprimir todas as mensagens
  - *gc*: Imprimir mensagens relacionadas ao GC.
  - *gref*: Imprimir (fraca, global) mensagens de alocação e desalocação.
  - *lref*: Imprimir mensagens de alocação e desalocação de referência local.

  > [!NOTE]
  > Estes são *extremamente* verbosos. Não habilite a menos que você realmente precise.

- *debug.mono.trace*: Permite definir a configuração [mono--trace.](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE`

## <a name="deleting-bin-and-obj"></a>Excluindo `bin` e excluindo`obj`

Xamarin.Android sofreu no passado de uma situação como:

- Você encontra um estranho erro de compilação ou tempo de execução.
- Você `Clean` `Rebuild`, ou exclua `bin` `obj` manualmente seus diretórios.
- O problema desaparece.

Estamos fortemente investidos na correção de problemas como estes devido ao seu impacto na produtividade dos desenvolvedores.

Se um problema como esse acontecer com você:

1. Faça uma nota mental. Qual foi a última ação que colocou seu projeto nesse estado?
1. Salve seu registro de compilação atual. Tente construir novamente e grave um [registro de compilação de diagnóstico.](#diagnostic-msbuild-output)
1. Enviar um [relatório de bug][bug].

Antes de `bin` excluir `obj` seus diretórios, feche-os e guarde-os para um diagnóstico posterior, se necessário. Você provavelmente `Clean` pode apenas o seu projeto de aplicativo Xamarin.Android para fazer as coisas funcionarem novamente.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android não pode resolver System.ValueTuple

Este erro ocorre devido a uma incompatibilidade com o Visual Studio.

- **Visual Studio 2017 A atualização 1** (versão 15.1 ou mais antiga) só é compatível com o **System.ValueTuple NuGet 4.3.0** (ou mais antigo).

- **Visual Studio 2017 Atualização 2** (versão 15.2 ou mais recente) só é compatível com o **System.ValueTuple NuGet 4.3.1** (ou mais recente).

Escolha o Sistema.ValueTuple NuGet que corresponde à instalação do Visual Studio 2017.

## <a name="gc-messages"></a>Mensagens GC

As mensagens de componente gc podem ser visualizadas definindo a propriedade debug.mono.log system para um valor que contém gc.

As mensagens GC são geradas sempre que o GC executa e fornece informações sobre quanto trabalho o GC fez:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Informações adicionais de GC, como informações `MONO_LOG_LEVEL` de `debug`tempo, podem ser geradas definindo a variável de ambiente para:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Isso resultará em (muitas) mensagens Mono adicionais, incluindo estas três de conseqüência:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Na `GC_BRIDGE` mensagem, `num-objects` está o número de objetos `num_hash_entries` de ponte que esta passagem está considerando, e é o número de objetos processados durante esta invocação do código da ponte.

Nas `GC_MINOR` mensagens `GC_MAJOR` e `total` mensagens, é a quantidade de tempo enquanto o `bridge` mundo é pausado (nenhum segmento está sendo executado), enquanto é o tempo demorado no código de processamento da ponte (que lida com o Java VM). O mundo *não* é pausado enquanto ocorre o processamento da ponte.

 *Em geral,* quanto maior `num_hash_entries`o valor de `bridge` , quanto mais tempo `total` as coletas levarem, e maior será o tempo gasto de coleta.

## <a name="global-reference-messages"></a>Mensagens de referência globais

Para habilitar o registro de loggig de referência global (GREF), a propriedade do sistema *debug.mono.log* deve conter *gref*, por exemplo:

```shell
adb shell setprop debug.mono.log gref
```

O Xamarin.Android usa referências globais do Android para fornecer mapeamentos entre as instâncias Java e as instâncias gerenciadas associadas, pois ao invocar um método Java uma instância Java precisa ser fornecida ao Java.

Infelizmente, os emuladores do Android só permitem que 2000 referências globais existam ao mesmo tempo. O hardware tem um limite muito maior de 52000 referências globais. O limite inferior pode ser problemático ao executar aplicativos no emulador, portanto, saber de *onde* veio a instância pode ser muito útil.

> [!NOTE]
> A contagem de referência global é interna ao Xamarin.Android, e não inclui (e não pode) incluir referências globais retiradas por outras bibliotecas nativas carregadas no processo. Use a contagem de referência global como estimativa.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Há quatro mensagens de conseqüência:

- Criação de referência global: estas são as linhas que começam com *+g+* , e fornecerão um rastreamento de pilha para o caminho de criação de código.
- Destruição de referência global: estas são as linhas que começam com *-g-* , e podem fornecer um traço de pilha para o caminho de código eliminando a referência global. Se o GC estiver se desfazendo do gref, nenhum traço de pilha será fornecido.
- Criação de referência global fraca: estas são as linhas que começam com *+w+* .
- Fraca destruição de referência global: estas são linhas que começam com *-w-* .

Em todas as mensagens, o valor *grefc* é a contagem de referências globais que o Xamarin.Android criou, enquanto o valor *grefwc* é a contagem de referências globais fracas que o Xamarin.Android criou. O valor *da alça* ou *alça obj* é o valor */* da alça JNI, e o caractere após o ' ' é o tipo de valor da alça: */L* para referência local, */G* para referências globais e */W* para referências globais fracas.

Como parte do processo de GC, as referências globais (+g+) são convertidas em referências globais fracas (causando um +w+ e -g-), um GC do lado de Java é chutado e, em seguida, a fraca referência global é verificada para ver se foi coletada. Se ele ainda estiver vivo, um novo gref é criado em torno do árbitro fraco (+g+, -w-), caso contrário o árbitro fraco é destruído (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>A instância java é criada e embrulhada por um MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Um GC está sendo executado...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Objeto ainda está vivo, como alça != nulo
## <a name="wref-turned-back-into-a-gref"></a>wref voltou em um gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Objeto está morto, como alça == nulo
## <a name="wref-is-freed-no-new-gref-created"></a>wref é libertado, nenhum novo gref criado

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Há uma ruga "interessante" aqui: em alvos rodando Android antes de 4.0, o valor gref é igual ao endereço do objeto Java na memória do Android runtime. (Ou seja, o GC é um colecionador não móvel, conservador, e está distribuindo referências diretas a esses objetos.) Assim, após uma seqüência de +g+, +w+, +g+, -w-, o gref resultante terá o mesmo valor do valor gref original. Isso torna o grepping através de logs bastante simples.

O Android 4.0, no entanto, tem um coletor em movimento e não distribui mais referências diretas a objetos VM em tempo de execução do Android. Consequentemente, depois de um +g+, +w+, -g-, +g+, -w-sequence, o valor gref *será diferente*. Se o objeto sobreviver a vários GCs, ele passará por vários valores gref, tornando mais difícil determinar de onde uma instância foi realmente alocada.

### <a name="querying-programmatically"></a>Consultando Programáticamente

Você pode consultar as contagens GREF e `JniRuntime` WREF consultando o objeto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`- Contagem global de referência

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`- Contagem de referência fraca

## <a name="android-debug-logs"></a>Logs de depuração do Android

Os [Registros de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) podem fornecer um contexto adicional sobre quaisquer erros de tempo de execução que você esteja vendo.

## <a name="floating-point-performance-is-terrible"></a>O desempenho do Ponto Flutuante é terrível!

Alternativamente, "Meu aplicativo roda 10x mais rápido com a compilação Debug do que com a compilação Release!"

Xamarin.Android suporta vários dispositivos ABIs: *armeabi*, *armeabi-v7a*e *x86*. Os ABIs do dispositivo podem ser especificados na **guia Project Properties > > arquiteturas suportadas .**

Compilações de depuração usam um pacote Android que fornece todos os ABIs e, portanto, usarão o ABI mais rápido para o dispositivo de destino.

As compilações de lançamento incluirão apenas os ABIs selecionados na guia Propriedades do projeto. Mais de um pode ser selecionado.

*armeabi* é o ABI padrão e tem o suporte mais amplo do dispositivo. *No entanto,* armeabi não suporta dispositivos multi-CPU e hardware flutuante, amont outras coisas. Consequentemente, os aplicativos que usam o tempo de execução de liberação armeabi serão vinculados a um único núcleo e usarão uma implementação soft-float. Ambos podem contribuir para um desempenho significativamente mais lento para o seu aplicativo.

Se o seu aplicativo exigir um desempenho decente de ponto flutuante (por exemplo, jogos), você deve ativar o *ARMEABI-v7a* ABI. Você pode querer apenas suportar o tempo *de execução armeabi-v7a,* embora isso signifique que dispositivos mais antigos que só suportam *armeabi* não serão capazes de executar o seu aplicativo.

## <a name="could-not-locate-android-sdk"></a>Não foi possível localizar o Android SDK

Há 2 downloads disponíveis no Google para o Android SDK para Windows.
Se você escolher o instalador .exe, ele escreverá chaves de registro que informem xamarin.Android onde ele foi instalado. Se você escolher o arquivo .zip e descompactá-lo você mesmo, Xamarin.Android não sabe onde procurar o SDK. Você pode dizer ao Xamarin.Android onde o SDK está no Visual Studio indo para **Ferramentas > Opções > Xamarin > Configurações do Android**:

[![Localização do Android SDK nas configurações do Android Xamarin](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>O IDE não exibe o dispositivo de destino

Às vezes, você tentará implantar seu aplicativo em um dispositivo, mas o dispositivo para o que deseja implantar não é mostrado na caixa de diálogo Selecionar dispositivo. Isso pode acontecer quando o Android Debug Bridge decide sair de férias.

Para diagnosticar este problema, encontre o [programa adb](~/android/deploy-test/debugging/android-debug-log.md)e execute:

```shell
adb devices
```

Se o seu dispositivo não estiver presente, então você precisa reiniciar o servidor Android Debug Bridge para que seu dispositivo possa ser encontrado:

```shell
adb kill-server
adb start-server
```

O software HTC Sync pode impedir **que o servidor inicial adb** esteja funcionando corretamente. Se o comando **adb start-server** não imprimir qual porta está começando, saia do software HTC Sync e tente reiniciar o servidor adb.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>A ferramenta de chave executável da tarefa especificada não pôde ser executada

Isso significa que o path não contém o diretório onde o diretório bin do Java SDK está localizado. Verifique se você seguiu essas etapas a partir do guia [de instalação.](~/android/get-started/installation/index.md)

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe ou aresgen.exe saiu com o código 1

Para ajudá-lo a depurar esse problema, entre no Visual Studio e altere o nível de verbosidade do MSBuild, para fazer isso, selecione: **Ferramentas > Opções > Projeto** e **Soluções > Construir** e Executar > **MSBuild Project Build Output Verbosity** e definir esse valor como **Normal**.

Reconstruir e verificar o painel de saída do Visual Studio, que deve conter o erro total.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Não há espaço de armazenamento suficiente no dispositivo para implantar o pacote

Isso ocorre quando você não inicia o emulador de dentro do Visual Studio. Ao iniciar o emulador fora do Visual `-partition-size 512` Studio, você precisa passar as opções, por exemplo.

```shell
emulator -partition-size 512 -avd MonoDroid
```

Certifique-se de usar o nome correto do simulador, ou seja, [o nome usado ao configurar o simulador](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>\_INSTALE\_o\_APK INVÁLIDO COM FALHA ao instalar um pacote

Os nomes dos pacotes android *devem* conter um período ('*.*. Edite o nome do pacote para que ele contenha um período.

- Dentro do Visual Studio:
  - Clique com o botão direito do mouse no projeto > Propriedades
  - Clique na guia Manifesto do Android à esquerda.
  - Atualize o campo Nome do pacote.
    - Se você ver &ldquo;a mensagem No AndroidManifest.xml encontrado. Clique para adicionar um. &rdquo;, clique no link e atualize o campo Nome do pacote.
- Dentro do Visual Studio para Mac:
  - Clique com o botão direito do mouse no seu projeto > Opções.
  - Navegue até a seção Build / Android Application.
  - Altere o campo Nome do pacote para conter um '.'.

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>INSTALAR\_\_FALHA\_AUSENTE\_BIBLIOTECA COMPARTILHADA AO instalar um pacote

Uma "biblioteca compartilhada" neste contexto *não* é um arquivo de biblioteca compartilhada nativa *(libfoo.so);* em vez disso, é uma biblioteca que deve ser instalada separadamente no dispositivo de destino, como o Google Maps.

O pacote Android especifica quais bibliotecas compartilhadas são necessárias com o `<uses-library/>` elemento. Se uma biblioteca *necessária* não estiver presente no `//uses-library/@android:required` dispositivo de destino (por exemplo, é *verdade,* que é o padrão), a instalação do pacote falhará com *a INSTALAÇÃO\_FALHA\_FALHA NDO\_BIBLIOTECA COMPARTILHADA\_*.

Para determinar quais bibliotecas compartilhadas são necessárias, visualize *o*
arquivo**Gerado AndroidManifest.xml** (por **exemplo, obj\\Debug\\androidAndroidManifest.xml\\**) e procure os `<uses-library/>` elementos. `<uses-library/>`elementos podem ser adicionados manualmente no arquivo **Propriedades\\do seu projeto AndroidManifest.xml** e através do [atributo personalizado UseLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Por exemplo, adicionar uma referência de montagem ao *Mono.Android.GoogleMaps.dll* adicionará implicitamente um `<uses-library/>` para a biblioteca compartilhada do Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>INSTALAÇÃO\_\_ATUALIZAÇÃO\_FALHA INCOMPATÍVEL Ao instalar um pacote

Os pacotes Android têm três requisitos:

- Eles devem conter um "." (veja a entrada anterior)
- Eles devem ter um nome de pacote de string exclusivo (daí a convenção reversa tld vista em nomes de aplicativos para Android, por exemplo, com.android.chrome para o aplicativo Chrome)
- Ao atualizar pacotes, o pacote deve ter a mesma tecla de assinatura.

Assim, imagine este cenário:

1. Você constrói & implantar seu aplicativo como um aplicativo Debug
2. Você altera a tecla de assinatura, por exemplo, para usar como um aplicativo de lançamento (ou porque você não gosta da tecla de assinatura de depuração fornecida por padrão)
3. Você instala seu aplicativo sem removê-lo primeiro, por exemplo, depurar > Start sem depuração dentro do Visual Studio

Quando isso acontecer, a instalação\_do\_\_pacote falhará com um erro INCOMPATÍVEL DE ATUALIZAÇÃO DE INSTALAÇÃO FALHA, porque o nome do pacote não mudou enquanto a chave de assinatura o fez. O [Registro de Depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) também conterá uma mensagem semelhante a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corrigir este erro, remova completamente o aplicativo do seu dispositivo antes de reinstalar.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>INSTALAÇÃO\_\_DE UID\_COM FALHA ALTERADA ao instalar um pacote

Quando um pacote Android é instalado, ele é atribuído a um *id de usuário* (UID).
*Às vezes,* por razões atualmente desconhecidas, ao instalar sobre `INSTALL_FAILED_UID_CHANGED`um aplicativo já instalado, a instalação falhará com:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para contornar esse problema, *desinstale totalmente* o pacote Android, seja instalando o `adb`aplicativo na GUI do alvo Android, ou usando :

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NÃO USE,** `adb uninstall -k`pois isso *preservará* os dados do aplicativo e, assim, preservará o UID conflitante no dispositivo de destino.

## <a name="release-apps-fail-to-launch-on-device"></a>Aplicativos de lançamento não são lançados no dispositivo

A saída do Registro de depuração do Android conterá uma mensagem semelhante a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Se assim for, há duas causas possíveis para isso:

1. O .apk não fornece um ABI que o dispositivo de destino suporta.
    Por exemplo, o .apk contém apenas binários armeabi-v7a, e o dispositivo de destino suporta apenas armeabi.

2. Um [bug do Android](https://code.google.com/p/android/issues/detail?id=21670). Se esse for o caso, desinstale o aplicativo, cruze os dedos e reinstale o aplicativo.

Para corrigir (1), edite as opções/propriedades do projeto e [adicione suporte para o ABI necessário à lista de ABIs suportados](~/android/app-fundamentals/cpu-architectures.md).Para determinar qual ABI você precisa adicionar, execute o seguinte comando adb contra o dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

A saída conterá os ABIs primários (e secundários opcionais).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>A propriedade OutPath não &ldquo;está definida para o projeto MyApp.csproj&rdquo;

Isso geralmente significa que você tem um &ldquo;&rdquo; computador HP e a variável ambiente Plataforma foi definida para algo como MCD ou HPD. Isso conflita com a propriedade MSBuild &ldquo;Platform&rdquo; &ldquo;que geralmente&rdquo;é definida como Qualquer CPU ou x86 . Você precisará remover essa variável de ambiente da sua máquina antes que o MSBuild possa funcionar:

- Sistema de > do painel de controle > variáveis avançadas do ambiente >

Reinicie o Visual Studio ou o Visual Studio para mac e tente reconstruir. As coisas agora devem funcionar como esperado.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject não pode ser lançado para...

Xamarin.Android 4.x não empacota corretamente tipos genéricos aninhados corretamente. Por exemplo, considere\# o seguinte código C usando [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```

O problema é que o Xamarin.Android incorretamente aninhada tipos genéricos. O `List<IDictionary<string, object>>` está sendo empajado para um [java.lang.ArrrayList,](xref:Java.Util.ArrayList)mas `ArrayList` as `mono.android.runtime.JavaObject` instâncias estão contendo (que fazem referência às `Dictionary<string, object>` instâncias) em vez de algo que implementa [java.util.Map,](xref:Java.Util.IMap)resultando na seguinte exceção:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

A solução é usar os tipos de [Java Collection](~/android/internals/api-design.md) fornecidos em vez dos `System.Collections.Generic` tipos para os &ldquo;tipos internos.&rdquo; Isso resultará em tipos Java apropriados ao empacotar as instâncias. (O código a seguir é mais complicado do que o necessário para reduzir as vidas de gref. Ele pode ser simplificado para alterar `s/List/JavaList/g` `s/Dictionary/JavaDictionary/g` o código original via e se as vidas de gref não são uma preocupação.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Isso será corrigido em uma versão futura.](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)

## <a name="unexpected-nullreferenceexceptions"></a>Exceções de referência de nulidade inesperadas

Ocasionalmente, o [Registro de Depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) mencionará NullReferenceExceptions que &ldquo;não podem acontecer,&rdquo; ou vêm do código de tempo de execução do Mono para Android pouco antes do aplicativo morrer:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

ou

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Isso pode acontecer quando o tempo de execução do Android decide abortar o processo, o que pode &ldquo;&rdquo; acontecer por várias razões, incluindo atingir o limite GREF do alvo ou fazer algo errado com o JNI.

Para ver se esse é o caso, verifique o Registro de Depuração do Android para obter uma mensagem do seu processo semelhante a:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Abortar devido à exaustão de referência global

A camada JNI do Android runtime só suporta um número limitado de referências de objetos JNI para ser válida a qualquer momento. Quando esse limite é excedido, as coisas quebram.

O limite GREF *(referência global)* é de 2000 referências no emulador, e ~52000 referências em hardware.

Você sabe que está começando a criar muitos GREFs quando você vê mensagens como esta no Registro de Depuração do Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando você atinge o limite GREF, uma mensagem como a seguinte é impressa:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```

No exemplo acima (que, aliás, vem do [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) o problema é que muitas instâncias do Android.Graphics.Point estão sendo criadas; consulte [ \#o comentário 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obter uma lista de correções para este bug em particular.

Normalmente, uma solução útil é encontrar qual tipo &ndash; tem muitas instâncias alocadas &ndash; Android.Graphics.Point no dump acima, em seguida, encontrar onde eles são criados em seu código-fonte e eliminá-los adequadamente (para que sua vida útil do objeto Java seja encurtada). Isso nem sempre\#é apropriado ( 685215 é multithreaded, então a solução trivial evita a chamada Descarte), mas é a primeira coisa a considerar.

Você pode habilitar [o GREF Logging](~/android/troubleshooting/index.md) para ver quando os GREFs são criados e quantos existem.

## <a name="abort-due-to-jni-type-mismatch"></a>Abortar devido à incompatibilidade tipo JNI

Se você rolar manualmente o código JNI, é possível que os tipos não coinem `java.lang.Runnable.run` corretamente, por exemplo, se você tentar invocar um tipo que não implemente `java.lang.Runnable`. Quando isso ocorrer, haverá uma mensagem semelhante a esta no Registro de Depuração do Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Suporte a código dinâmico

### <a name="dynamic-code-does-not-compile"></a>Código dinâmico não compila

Para usar\# a dinâmica C em seu aplicativo ou biblioteca, você tem que adicionar System.Core.dll, Microsoft.CSharp.dll e Mono.CSharp.dll ao seu projeto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Na compilação Release, MissingMethodException ocorre para código dinâmico em tempo de execução.

- É provável que seu projeto de aplicativo não tenha referências ao System.Core.dll, Microsoft.CSharp.dll ou Mono.CSharp.dll. Certifique-se de que essas assembléias sejam referenciadas.

  - Tenha em mente que o código dinâmico sempre custa. Se você precisar de um código eficiente, considere não usar código dinâmico.

- Na primeira prévia, essas assembléias foram excluídas, a menos que os tipos em cada montagem sejam explicitamente usados pelo código do aplicativo. Veja a seguir para obter uma solução:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Projetos construídos com falha AOT+LLVM em dispositivos x86

Ao implantar um aplicativo construído com [AOT+LLVM](~/android/deploy-test/release-prep/index.md) em dispositivos baseados em x86, você pode ver uma mensagem de erro de exceção semelhante à seguinte:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Este é um problema conhecido – a solução é desativar o LLVM.
