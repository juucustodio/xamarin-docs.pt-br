---
title: Arquitetura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ea66cda0e2a1935a430c064c9cebd4134d295729
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954241"
---
# <a name="architecture"></a>Arquitetura

Aplicativos xamarin. Android é executado dentro do ambiente de execução Mono.
Essa execução ambiente execuções lado a lado com a máquina virtual de tempo de execução (arte Android). Ambos os ambientes de tempo de execução executados sobre o kernel do Linux e expõem várias APIs para o código do usuário que permite que os desenvolvedores acessem o sistema subjacente. Tempo de execução Mono é gravado na linguagem C.

Você pode estar usando o [System](xref:System), [System.IO](xref:System.IO), [System.Net](xref:System.Net) e o restante do .NET da classe bibliotecas para acessar os recursos de sistema operacional subjacentes do Linux.

No Android, a maioria das instalações de sistema, como áudio, gráficos, OpenGL e telefonia não está disponível diretamente para aplicativos nativos, eles só são expostos por meio de APIs do Android em tempo de execução Java que residem em um dos [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * namespaces ou o [Android](https://developer.xamarin.com/api/namespace/Android/). * namespaces. A arquitetura é mais ou menos assim:

[![Diagrama de Mono e arte acima do kernel e abaixo de .NET/Java + associações](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Os desenvolvedores do xamarin. Android acessam os diversos recursos no sistema operacional por chamar as APIs do .NET que eles saibam (para acesso de nível baixo) ou usando as classes expostas nos namespaces do Android que fornece uma ponte para as APIs de Java que são expostos por o tempo de execução do Android.

Para obter mais informações sobre como classes do Android se comunicar com as classes de tempo de execução Android, consulte o [Design de API](~/android/internals/api-design.md) documento.


## <a name="application-packages"></a>Pacotes de aplicativos

Pacotes de aplicativos Android são contêineres ZIP com um *Apk* extensão de arquivo. Pacotes de aplicativos do xamarin. Android têm a mesma estrutura e o layout de pacotes Android normal, com as seguintes adições:

-   Os assemblies do aplicativo (que contém IL) são *armazenados* descompactados dentro de *assemblies* pasta. Durante o processo de inicialização na versão se baseia a *. Apk* é *mmap()* ed em processo e assemblies são carregados da memória. Isso permite a inicialização mais rápida do aplicativo, como assemblies não precisa ser extraído antes da execução.  
-   *Observação:* Informações de local do assembly, como [Assembly.Location](xref:System.Reflection.Assembly.Location) e [Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *não podem ser utilizados* em compilações de versão. Eles não existem como entradas de sistema de arquivos distintos e não precisam de nenhum local utilizável.


-   Bibliotecas nativas, que contém o tempo de execução Mono estão presentes dentro de *Apk* . Um aplicativo xamarin. Android deve conter as bibliotecas nativas para as arquiteturas de Android/destino desejado, por exemplo, *armeabi* , *armeabi-v7a* , *x86* . Aplicativos xamarin. Android não podem ser executado em uma plataforma, a menos que ele contém as bibliotecas de tempo de execução apropriado.


Aplicativos xamarin. Android também contêm *Callable Wrappers do Android* para permitir que o Android chamar código gerenciado.



## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

- **Callable wrappers do Android** são uma [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) ponte que são usados a qualquer momento em que o tempo de execução do Android precisa chamar código gerenciado. Callable wrappers do Android são métodos como virtuais podem ser substituídas e interfaces de Java podem ser implementadas. Consulte a [visão geral da integração Java](~/android/platform/java-integration/index.md) doc para obter mais informações.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrappers gerenciados

Gerenciado callable wrappers são uma ponte JNI que são usados sempre que precisa de código gerenciado para invocar o código do Android e fornecem suporte para substituir métodos virtuais e implementando interfaces de Java. Todo o [Android](https://developer.xamarin.com/api/namespace/Android/). * e namespaces relacionados são callable wrappers gerenciados, gerados por meio de [. jar associação](~/android/platform/binding-java-library/index.md).
Callable wrappers do gerenciado serão responsáveis por converter entre tipos gerenciados e Android e invocar os métodos de plataforma Android subjacente por meio de JNI.

Cada criado que pode ser chamado de wrapper gerenciado mantém uma referência global de Java, que pode ser acessada por meio de [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propriedade. Referências globais são usadas para fornecer o mapeamento entre as instâncias de Java e instâncias gerenciadas. Referências globais são um recurso limitado: emuladores permitem referências de globais 2000 somente existir de cada vez, enquanto a maioria dos hardwares permite referências de globais mais 52.000 existir por vez.

Para controlar quando as referências de globais são criadas e destruídas, você pode definir as [debug.mono.log](~/android/troubleshooting/index.md) propriedade do sistema para conter [gref](~/android/troubleshooting/index.md).

Referências globais podem ser explicitamente liberadas chamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) em um wrapper gerenciado que pode ser chamado. Isso removerá o mapeamento entre a instância de Java e a instância gerenciada e permitir que a instância de Java a serem coletados. Se a instância de Java novamente é acessada no código gerenciado, será criado um novo callable wrapper gerenciado para ele.

Tenha cuidado ao descarte gerenciado Callable Wrappers se a instância pode ser compartilhada entre threads, como descartar a instância inadvertidamente terá impacto sobre as referências de outros threads. Para segurança máxima, apenas `Dispose()` de instâncias que foram alocadas por meio `new` *ou* de métodos que você *sabe* sempre alocar novas instâncias e não armazenado em cache instâncias que podem ser fazer com que a instância acidental de compartilhamento entre threads.



## <a name="managed-callable-wrapper-subclasses"></a>Gerenciado Subclasses Callable Wrapper

As subclasses de wrapper que pode ser chamado gerenciado são em que toda a lógica específica do aplicativo "interessante" pode viver. Eles incluem custom [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) subclasses (como o [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo no modelo de projeto padrão). (Especificamente, eles são qualquer *Java.Lang.Object* subclasses que fazer *não* contêm um [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado ou [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) está *falso*, que é o padrão.)

Gerenciado como callable wrappers, gerenciados subclasses callable wrapper também contêm uma referência global, acessível por meio de [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) propriedade. Assim como com callable wrappers do gerenciado, referências de globais podem ser explicitamente liberadas chamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
Ao contrário de encapsuladores que pode ser chamados, *muito cuidado* devem ser tomadas antes de descartar essas instâncias, como *Dispose ()*- ing da instância interromperá o mapeamento entre a instância de Java (uma instância de um Android Callable Wrapper) e a instância gerenciada.


### <a name="java-activation"></a>Ativação de Java

Quando um [Callable Wrapper Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) é criado de Java, o construtor ACW fará com que o construtor c# correspondente a ser invocado. Por exemplo, o ACW para *MainActivity* conterá um construtor padrão que invocará *MainActivity*do construtor padrão. (Isso é feito por meio de *TypeManager.Activate()* chamar dentro de construtores ACW.)

Há uma assinatura de construtor da consequência: o *(IntPtr, JniHandleOwnership)* construtor. O *(IntPtr, JniHandleOwnership)* construtor será invocado sempre que um objeto de Java é exposto ao código gerenciado e um Callable Wrapper gerenciado precisa ser construído para gerenciar o identificador JNI. Geralmente, isso é feito automaticamente.

Há dois cenários em que o *(IntPtr, JniHandleOwnership)* construtor deve ser fornecido em uma subclasse gerenciados Callable Wrapper:

1. [Android](https://developer.xamarin.com/api/type/Android.App.Application/) é uma subclasse. *Aplicativo* é especial; o padrão *aplicativo* construtor será *nunca* ser chamado e o [(IntPtr, JniHandleOwnership) em vez disso, deve ser fornecido um construtor ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocação de método virtual de um construtor de classe base.


Observe que (2) é uma abstração de vazamento. Em Java, como em c#, chamadas para métodos virtuais de um construtor sempre chamar a implementação mais derivada do método. Por exemplo, o [TextView (contexto, AttributeSet, int) construtor](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) invoca o método virtual [TextView.getDefaultMovementMethod()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que está vinculado como o [ Propriedade TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Assim, se um tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) fosse (1) [subclasse TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [substituir TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [ativar uma instância dela classe por meio de XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) substituído *DefaultMovementMethod* propriedade seria invocada antes do construtor ACW tido a oportunidade de ser executado e ocorrerá antes do C# construtor tido a oportunidade Execute.

Isso é suportado pela instanciação de uma instância LogTextBox por meio de [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) construtor quando a instância ACW LogTextBox entra em código gerenciado e, em seguida, invocando o [ LogTextBox (contexto, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) construtor *na mesma instância* quando o construtor ACW for executado.

Ordem de eventos:

1.  Layout de XML é carregado em um [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Android instancia o grafo de objeto de Layout e cria uma instância de uma instância do *monodroid.apidemo.LogTextBox* , o ACW para *LogTextBox* .

3.  O *monodroid.apidemo.LogTextBox* construtor executa o [android.widget.TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) construtor.

4.  O *TextView* construtor chama *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invoca *LogTextBox.n_getDefaultMovementMethod()* , que invoca *TextView.n_GetDefaultMovementMethod()* , que invoca [Java.Lang.Object.GetObject&lt;TextView&gt; (manipular, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* verifica para ver se já houver um correspondente em c# de instância para *manipular* . Se houver, será retornado. Nesse cenário, não for, isso *Object.GetObject&lt;T&gt;()* deve criar uma.

7.  *Object.GetObject&lt;T&gt;()* procura o *LogTextBox (IntPtr, JniHandleOwneship)* construtor, invoca-lo, cria um mapeamento entre *manipular* e a instância criada e retorna a instância criada.

8.  *TextView.n_GetDefaultMovementMethod()* invoca o *LogTextBox.DefaultMovementMethod* getter de propriedade.

9.  O controle retorna para o *android.widget.TextView* construtor, que conclui a execução.

10. O *monodroid.apidemo.LogTextBox* construtor é executado, invocar *TypeManager.Activate()* .

11. O *LogTextBox (contexto, IAttributeSet, int)* construtor executa *na mesma instância criada na (7)* .

12. Se (IntPtr, JniHandleOwnership) construtor não pode ser encontrado e, em seguida, um System.MissingMethodException](xref:System.MissingMethodException) será lançada.

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Chamadas prematuro Dispose)

Há um mapeamento entre um identificador JNI e a instância correspondente do c#. Java.Lang.Object.Dispose() quebra esse mapeamento. Se um identificador JNI entra em código gerenciado depois que o mapeamento foi interrompido, ele se parece com a ativação do Java e o *(IntPtr, JniHandleOwnership)* construtor será verificada e invocado. Se o construtor não existir, uma exceção será lançada.

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

Se criamos uma instância, Dispose () e fazer com que o Managed Callable Wrapper ser recriada:

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

Se a subclasse contêm uma *(IntPtr, JniHandleOwnership)* construtor, em seguida, um *novos* instância do tipo será criada. Como resultado, a instância será exibida a "perda" todos os dados de instância, conforme ele é uma nova instância. (Observe que o valor é nulo.)

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Somente *Dispose ()* de gerenciado subclasses de wrapper que pode ser chamado quando você souber que o objeto de Java não será usado mais ou a subclasse não contém nenhum dado de instância e um *(IntPtr, JniHandleOwnership)* construtor foi fornecido.



## <a name="application-startup"></a>Inicialização do aplicativo

Quando uma atividade, serviço, etc. é iniciado, Android primeiro verifica para ver se já houver um processo em execução para hospedar a atividade/service/etc. Se nenhum tal processo existir, será criado um novo processo, o [androidmanifest. XML](https://developer.android.com/guide/topics/manifest/manifest-intro.html) são lidos e o tipo especificado na [ /manifest/application/@android:name ](https://developer.android.com/guide/topics/manifest/application-element.html#nm) atributo é carregado e instanciado. Em seguida, todos os tipos especificados pelo [ /manifest/application/provider/@android:name ](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) valores de atributo são instanciados e ter seus [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) método invocado. Ganchos de xamarin. Android em isso adicionando um *mono. MonoRuntimeProvider* *ContentProvider* para androidmanifest. XML durante o processo de compilação. O *mono. MonoRuntimeProvider.attachInfo()* método é responsável por carregar o tempo de execução Mono no processo.
Qualquer tentativa de usar Mono antes desse ponto falhará. ( *Observação*: É por isso que tipos de quais subclasse [Android](https://developer.xamarin.com/api/type/Android.App.Application/) precisa fornecer um [(IntPtr, JniHandleOwnership) construtor](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), como a instância do aplicativo é criada antes de Mono pode ser inicializado.)

Após a inicialização do processo de conclusão, `AndroidManifest.xml` é consultada para localizar o nome de classe da atividade de serviço/etc. para iniciar. Por exemplo, o [ /manifest/application/activity/@android:name atributo](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) é usado para determinar o nome de uma atividade para carregar. Para atividades, esse tipo deve herdar [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
O tipo especificado é carregado por meio [forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que exige que o tipo de ser um Java digitar, portanto, o Callable Wrappers do Android), então instanciada. Criação de uma instância do Android Callable Wrapper irá disparar a criação de uma instância do tipo c# correspondente. Android, em seguida, invoque [Activity.onCreate(Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , que fará com que o correspondente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) a ser invocada, e você está desativado para as corridas.
