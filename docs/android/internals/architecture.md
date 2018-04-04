---
title: Arquitetura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f4be88a1eabb3fa3cca733690a3f097a03516272
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="architecture"></a>Arquitetura

Aplicativos xamarin executado dentro do ambiente de execução Mono.
Essa execução ambiente executa-lado a lado com a máquina virtual de tempo de execução (arte Android). Ambos os ambientes de tempo de execução execute na parte superior do kernel do Linux e expõem várias APIs para o código do usuário que permite aos desenvolvedores acessar o sistema subjacente. O tempo de execução Mono é gravado na linguagem C.

Você pode usar o [sistema](http://msdn.microsoft.com/en-us/library/system.aspx), [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx), [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx) e o restante do .NET classe bibliotecas para acessar os recursos de sistema operacional Linux subjacentes.

No Android, a maioria das instalações do sistema como áudio, gráficos, OpenGL e telefonia não está disponível diretamente a aplicativos nativos, eles só são expostos através das APIs de Java Runtime Android que residem em um do [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * namespaces ou o [Android](https://developer.xamarin.com/api/namespace/Android/). * namespaces. A arquitetura é mais ou menos assim:

[![Diagrama de Mono e arte acima do kernel e abaixo .NET/Java + associações](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Os desenvolvedores do xamarin acessar os diversos recursos do sistema operacional chamando APIs do .NET que eles saibam (para acesso de baixo nível) ou usando as classes expostas nos namespaces Android que fornece uma ponte para as APIs de Java que são expostas pelo o tempo de execução do Android.

Para obter mais informações sobre como as classes Android se comunicam com as classes de tempo de execução Android, consulte o [API Design](~/android/internals/api-design.md) documento.


## <a name="application-packages"></a>Pacotes de aplicativos

Pacotes de aplicativos do Android são contêineres ZIP com um *. Apk* extensão de arquivo. Pacotes de aplicativos xamarin têm a mesma estrutura e layout pacotes Android normal, com as seguintes adições:

-   Os assemblies do aplicativo (que contém o IL) são *armazenado* descompactados dentro de *assemblies* pasta. Durante o processo de inicialização na versão cria o *. Apk* é *mmap()* ed para o processo e os assemblies são carregados na memória. Isso permite a inicialização de aplicativo mais rápida, como assemblies não precisam ser extraído antes da execução. - *Observação:* informações de localização do Assembly como [Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/) e [Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *não pode ser confiado* versão compilações. Eles não existem como entradas distintas do sistema de arquivos, e não tiverem nenhum local utilizável.


-   Bibliotecas nativas que contém o tempo de execução Mono estão presentes no *. Apk* . Um aplicativo xamarin deve conter bibliotecas nativas para as arquiteturas de Android/destino desejado, por exemplo, *armeabi* , *armeabi v7a* , *x86* . Aplicativos xamarin não podem ser executado em uma plataforma, a menos que ele contém as bibliotecas de tempo de execução apropriada.


Aplicativos xamarin também contêm *Android Callable Wrappers* para permitir que o Android chamar código gerenciado.



## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

- **Callable wrappers do Android** são uma [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) ponte que são usados a qualquer momento em que o tempo de execução Android precisa chamar código gerenciado. Callable wrappers do Android são métodos como virtual pode ser substituído e interfaces Java podem ser implementados. Consulte o [visão geral da integração Java](~/android/platform/java-integration/index.md) documento para obter mais informações.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrappers gerenciados

Callable wrappers do gerenciados são uma ponte JNI que são usados sempre precisa de código gerenciado para invocar o código do Android e oferecem suporte para substituir métodos virtuais e implementando interfaces Java. Todo o [Android](https://developer.xamarin.com/api/namespace/Android/). * e dos namespaces relacionados são gerenciados callable wrappers do gerado por meio de [. jar associação](~/android/platform/binding-java-library/index.md).
Callable wrappers do gerenciado serão responsáveis pela conversão entre tipos gerenciados e Android e chamar os métodos de plataforma Android subjacente por meio de JNI.

Cada criado pode ser chamado de wrapper gerenciado mantém uma referência global Java, que pode ser acessada por meio de [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propriedade. Referências globais são usadas para fornecer o mapeamento entre instâncias Java e instâncias gerenciadas. Referências globais são um recurso limitado: emuladores permitem apenas 2000 referências global de existir ao mesmo tempo, enquanto o hardware mais permite que mais 52.000 referências global existir de cada vez.

Para controlar quando referências globais são criadas e destruídas, você pode definir o [debug.mono.log](~/android/troubleshooting/index.md) propriedade do sistema para conter [gref](~/android/troubleshooting/index.md).

Referências globais podem ser liberadas explicitamente chamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) em callable wrapper gerenciado. Isso removerá o mapeamento entre a instância de Java e a instância gerenciada e permitir que a instância de Java para serem coletados. Se a instância de Java novamente é acessada de código gerenciado, será criado um novo callable wrapper gerenciado para ele.

Deve ser tomado quando descartar gerenciados Callable Wrappers se a instância pode ser compartilhada entre threads, como descartar a instância inadvertidamente afetará as referências de quaisquer outros threads. Para segurança máxima, apenas `Dispose()` de instâncias que foram alocadas por meio de `new` *ou* de métodos que você *saber* sempre alocar novas instâncias e as instâncias não armazenados em cache que podem fazer com que a instância acidental de compartilhamento entre threads.



## <a name="managed-callable-wrapper-subclasses"></a>Gerenciado Subclasses Callable Wrapper

Pode ser chamado de wrapper gerenciado subclasses são onde toda a lógica específica do aplicativo "interessante" pode residir. Esses incluem custom [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) subclasses (como o [atividade1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo no modelo de projeto padrão). (Especificamente, eles são qualquer *Java.Lang.Object* subclasses que *não* contêm um [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado ou [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) é *false*, que é o padrão.)

Gerenciados como callable wrappers, gerenciados subclasses callable wrapper também contêm uma referência global, acessível por meio de [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) propriedade. Assim como com callable wrappers do gerenciado, referências globais podem ser liberadas explicitamente chamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
Ao contrário de encapsuladores que pode ser chamadas, *muito cuidadoso* devem ser tomadas antes de descartar nesses casos, como *Dispose ()*espelhamento da instância interromperá o mapeamento entre a instância de Java (uma instância de um Android Callable Wrapper) e a instância gerenciada.


### <a name="java-activation"></a>Ativação de Java

Quando um [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) é criado a partir de Java, o construtor ACW fará com que o construtor c# correspondente a ser invocado. Por exemplo, o ACW para *MainActivity* conterá um construtor padrão que invocará *MainActivity*do construtor padrão. (Isso é feito por meio de *TypeManager.Activate()* chamar dentro de construtores ACW.)

Há uma assinatura de construtor importante: o *(IntPtr, JniHandleOwnership)* construtor. O *(IntPtr, JniHandleOwnership)* construtor é chamado sempre que um objeto de Java é exposto no código gerenciado e um Callable Wrapper gerenciado precisa ser criada para gerenciar o identificador JNI. Geralmente, isso é feito automaticamente.

Há dois cenários em que o *(IntPtr, JniHandleOwnership)* construtor deve ser fornecido em uma subclasse gerenciados Callable Wrapper:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) é uma subclasse. *Aplicativo* é especial; o padrão *aplicativo* construtor será *nunca* ser invocado e o [(IntPtr, JniHandleOwnership) em vez disso, deve ser fornecido um construtor ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocação de método virtual de um construtor de classe base.


Observe que, (2) é uma abstração vazamento. Em Java, como c#, chamadas para métodos virtuais de um construtor sempre chamar a implementação do método mais derivada. Por exemplo, o [construtor TextView (contexto, AttributeSet, int)](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) invoca o método virtual [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que é vinculado como o [ Propriedade TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Portanto, se um tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) fosse (1) [subclasse TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [substituir TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [ativar uma instância do classe por meio de XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) substituído *DefaultMovementMethod* propriedade deve ser chamada antes que o construtor ACW tido a oportunidade de executar, e ocorrerá antes que o construtor c# tido a oportunidade de executar.

Há suporte para isso criando uma instância de LogTextBox por meio de [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) construtor quando a instância LogTextBox ACW entra em código gerenciado e, em seguida, chamar o [ LogTextBox (contexto, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) construtor *na mesma instância* quando o construtor ACW é executado.

Ordem de eventos:

1.  Layout de XML é carregado em um [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Android instancia o gráfico de objeto de Layout e cria uma instância de *monodroid.apidemo.LogTextBox* , ACW para *LogTextBox* .

3.  O *monodroid.apidemo.LogTextBox* construtor executa o [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) construtor.

4.  O *TextView* construtor chama *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invoca *LogTextBox.n_getDefaultMovementMethod()* , que chama *TextView.n_GetDefaultMovementMethod()* , que invoca [Java.Lang.Object.GetObject&lt;TextView&gt; (identificador, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* verifica se já houver um correspondente c# instância para *tratar* . Se houver, será retornado. Nesse cenário, houver, isso *Object.GetObject&lt;T&gt;()* deve criar uma.

7.  *Object.GetObject&lt;T&gt;()* procura o *LogTextBox (IntPtr, JniHandleOwneship)* construtor, invoca, cria um mapeamento entre *tratar* e a instância criada e retorna a instância criada.

8.  *TextView.n_GetDefaultMovementMethod()* invoca o *LogTextBox.DefaultMovementMethod* getter de propriedade.

9.  O controle retorna para o *android.widget.TextView* construtor, que conclui a execução.

10. O *monodroid.apidemo.LogTextBox* construtor é executado, invocar *TypeManager.Activate()* .

11. O *LogTextBox (contexto, IAttributeSet, int)* construtor executa *na mesma instância criada (7)* .

12. ...


Se (IntPtr, JniHandleOwnership) não é possível encontrar o construtor, um [System. MissingMethodException](https://developer.xamarin.com/api/type/System.MissingMethodException/) será lançada.


<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Chamadas de prematuro Dispose)

Há um mapeamento entre um identificador JNI e a instância correspondente do c#. Java.Lang.Object.Dispose() quebra esse mapeamento. Se um identificador JNI entra em código gerenciado, depois que o mapeamento foi interrompido, ele se parece com a ativação de Java e o *(IntPtr, JniHandleOwnership)* construtor será verificada e invocado. Se o construtor não existir, uma exceção será lançada.

Por exemplo, dada a subclasse gerenciado que pode ser chamado Wraper seguinte:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Se criar uma instância, Dispose () e fazer com que o Managed Callable Wrapper a ser criado novamente:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

O programa morrerão:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Se a subclasse contêm um *(IntPtr, JniHandleOwnership)* construtor, um *novo* instância do tipo será criada. Como resultado, a instância será exibida "perder" todos os dados de instância, como é uma nova instância. (Observe que o valor é nulo.)

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Somente *Dispose ()* de gerenciados subclasses callable wrapper quando você souber que o objeto de Java não será usado mais ou, a subclasse não contém nenhum dado de instância e um *(IntPtr, JniHandleOwnership)* construtor foi fornecido.



## <a name="application-startup"></a>Inicialização do aplicativo

Quando uma atividade, o serviço, etc. é iniciada, Android verificará primeiro para ver se já há um processo em execução para hospedar a atividade/serviço, etc. Se esse processo não existir, será criado um novo processo, o [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html) são lidos e o tipo especificado no [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) atributo é carregado e instanciado. Em seguida, todos os tipos especificados pelo [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) valores de atributo são instanciados e ter suas [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) método invocado. Ganchos de xamarin em isso adicionando um *mono. MonoRuntimeProvider* *ContentProvider* para AndroidManifest.xml durante o processo de compilação. O *mono. MonoRuntimeProvider.attachInfo()* método é responsável por carregar o tempo de execução Mono no processo.
Qualquer tentativa de usar Mono antes deste ponto falhará. ( *Observação*: é por isso que tipos que subclasse [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) precisa fornecer um [(IntPtr, JniHandleOwnership) construtor](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), como a instância do aplicativo criado antes da inicialização do Mono.)

Após a conclusão da inicialização do processo, `AndroidManifest.xml` é consultado para localizar o nome da classe da atividade/serviço/etc. para iniciar. Por exemplo, o [ /manifest/application/activity/@android:name atributo](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) é usado para determinar o nome de uma atividade para carregar. Para atividades, esse tipo deve herdar [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
O tipo especificado é carregado por meio de [forName ()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que requer que o tipo seja um Java digitar, portanto, o Android Callable Wrappers), em seguida, criar uma instância. Criação de uma instância do Android Callable Wrapper vai disparar a criação de uma instância do tipo c# correspondente. Android, em seguida, invoque [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , que fará com que o correspondente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) a ser invocado, e você está desativado para as corridas.
