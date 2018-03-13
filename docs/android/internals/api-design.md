---
title: Projeto de API
ms.topic: article
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 23aa944b88fe3e743b6b29810c29d1843f2efc29
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="api-design"></a>Projeto de API


## <a name="overview"></a>Visão geral

Além das bibliotecas de classes Base que fazem parte do Mono principais, xamarin fornecido com associações para várias APIs Android permitir que os desenvolvedores criem aplicativos Android nativos com Mono.

No núcleo do xamarin existe é um mecanismo de interoperabilidade que world pontes c# com o mundo de Java e fornece aos desenvolvedores com acesso às APIs do Java de c# ou outras linguagens .NET.


## <a name="design-principles"></a>Princípios de design

Estes são alguns dos nossos princípios de design para a associação xamarin

-  Está de acordo com o [diretrizes de Design do .NET Framework](http://msdn.microsoft.com/en-us/library/ms229042.aspx).

-  Permitir que os desenvolvedores de classes Java de subclasse.

-  Subclasse deve funcionar com c# construtores padrão.

-  Derive de uma classe existente.

-  Chamada de construtor base para cadeia.

-  Substituindo métodos deve ser feito com o sistema de substituição do #.

-  Possibilitam a tarefas comuns de Java fácil e tarefas do disco rígidas Java.

-  Exponha as propriedades de JavaBean como propriedades do c#.

-  Expor uma API com rigidez de tipos:

    - Aumente a segurança de tipo.

    - Minimize erros de tempo de execução.

    - Obter IDE intellisense em tipos de retorno.

    - Permite a documentação de pop-up do IDE.

-  Incentivamos a exploração no IDE das APIs:

    - Utilize Framework alternativas para minimizar Java Classlib exposição.

    - Expor c# delegados (lambdas, métodos anônimos e System. Delegate) em vez de interfaces de método único quando apropriado e aplicável.

    - Fornecem um mecanismo para chamar a biblioteca de Java arbitrária ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).



## <a name="assemblies"></a>Assemblies

Xamarin inclui um número de conjuntos de módulos que constituem o *MonoMobile perfil*. O [Assemblies](~/cross-platform/internals/available-assemblies.md) página tem mais informações.

As ligações para a plataforma Android estão contidas no `Mono.Android.dll` assembly. Este assembly contém a associação inteira para consumo APIs Android e se comunicar com o tempo de execução Android VM.


## <a name="binding-design"></a>Design de associação


### <a name="collections"></a>Coleções

As APIs do Android utilizar as coleções java.util extensivamente para fornecer listas, conjuntos e mapas. Podemos expor esses elementos usando o [Generic](https://developer.xamarin.com/api/namespace/System.Collections.Generic/) interfaces em nossa associação. Os mapeamentos fundamentais são:

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html) mapeia para o tipo de sistema [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), classe auxiliar [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html) mapeia para o tipo de sistema [IList<T>](http://msdn.microsoft.com/en-us/library/5y536ey6.aspx), classe auxiliar [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](http://developer.android.com/reference/java/util/Map.html) mapeia para o tipo de sistema [IDictionary < TKey, TValue >](http://msdn.microsoft.com/en-us/library/s4ys34ea.aspx), classe auxiliar [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html) mapeia para o tipo de sistema [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), classe auxiliar [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Fornecemos classes auxiliares para facilitar mais rápido copyless marshaling desses tipos. Quando possível, é recomendável usar esses fornecido coleções em vez de implementação do framework fornecido, como [ `List<T>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/) ou [ `Dictionary<TKey, TValue>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%602/). O [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implementações utilizar uma coleção de Java nativo internamente e, portanto, não requerem a cópia de e para uma coleção nativa ao passar para um membro de API do Android.

Você pode transmitir qualquer implementação de interface para um método Android aceitar essa interface, por exemplo, passar um `List<int>` para o [ArrayAdapter&lt;int&gt;(contexto, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)construtor. *No entanto*, para todas as implementações *exceto* para implementações Android.Runtime, isso envolve *cópia* a lista da VM Mono no tempo de execução Android VM. Se a lista for alterado em tempo de execução do Android (por exemplo, ao chamar o [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) método), essas alterações *não* visíveis no código gerenciado. Se um `JavaList<int>` foram usadas, essas alterações deve ser visíveis.

Rephrased, coleções de interface implementações são *não* um dos itens acima listados **classe auxiliar**es somente empacotar [In]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>Propriedades

Métodos de Java são transformados em propriedades, quando apropriado:

-  O par de métodos Java `T getFoo()` e `void setFoo(T)` são transformadas no `Foo` propriedade. Exemplo: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  O método Java `getFoo()` é transformado em propriedade Foo somente leitura. Exemplo: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Somente conjunto de propriedades não são geradas.

-  As propriedades são *não* gerado se o tipo de propriedade deve ser uma matriz.



### <a name="events-and-listeners"></a>Eventos e ouvintes

As APIs do Android criadas sobre Java e seus componentes seguem o padrão de Java para conectar ouvintes de eventos. Esse padrão tende a ser trabalhosa conforme ele exige que o usuário criar uma classe anônima e declarar os métodos para substituir, por exemplo, isso é como as coisas que deve ser realizadas no Android com Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

O código equivalente em c# usando eventos seria:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Observe que ambos os mecanismos acima estão disponíveis com o xamarin. Você pode implementar uma interface de ouvinte e anexá-lo com View.SetOnClickListener, ou você pode anexar um delegado criado por meio de qualquer um dos c# paradigmas normais ao evento de clique.

Quando o método de retorno de chamada de ouvinte tem um retorno de void, podemos criar elementos de API com base em um [EventHandler&lt;TEventArgs&gt; ](https://developer.xamarin.com/api/type/System.EventHandler%601/) delegate. Podemos gerar um evento semelhante ao exemplo anterior para esses tipos de ouvinte. No entanto, se o retorno de chamada de ouvinte retorna um não nulo e não- **booliano** valor, eventos e EventHandlers não são usados. Em vez disso, podemos gerar um representante específico para a assinatura do retorno de chamada e adicionar propriedades em vez de eventos. O motivo é lidar com ordem de invocação do delegado e tratamento de retorno. Essa abordagem reflete o que é feito com a API do xamarin.

Propriedades ou eventos c# são geradas automaticamente apenas se o método de registro de evento Android:

1. Tem um `set` prefixo, por exemplo, [ *definir*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Tem um `void` tipo de retorno.

1. Aceita apenas um parâmetro, o tipo de parâmetro é uma interface, a interface tem apenas um método e o nome da interface termina em `Listener` , por exemplo, [View.OnClick *ouvinte*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Além disso, se o método de interface de ouvinte tem um tipo de retorno **booliano** em vez de **void**, em seguida, gerado *EventArgs* subclasse conterá um *Handled* propriedade. O valor da *Handled* propriedade é usada como o valor de retorno para o *ouvinte* método e ele assume como padrão `true`.

Por exemplo, o Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) método aceita a [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interface e o [View.OnKeyListener.onKey (modo de exibição, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) o método tem um tipo de retorno booleano. Xamarin gera correspondente [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) evento, que é um [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
O *KeyEventArgs* classe por sua vez tem um [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriedade, que é usada como o valor de retorno para o *View.OnKeyListener.onKey()* método.

Pretendemos adicionar sobrecargas para outros métodos e construtores para expor a conexão com base em delegado. Além disso, ouvintes com vários retornos de chamada exigem algumas inspeção adicional para determinar se é razoável, implementar retornos de chamada individuais para que estiver convertendo esses como são identificados. Se não houver nenhum evento correspondente, ouvintes devem ser usados em c#, mas. Coloque as que você acha que pode ter uso de delegado para nossa atenção. Também fizemos algumas conversões de interfaces sem o sufixo "Escuta" quando ficou claro que se beneficiariam de uma delegado alternativa.

Todas as interfaces de ouvintes implementam o [ `Android.Runtime.IJavaObject` ](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interface, devido a detalhes de implementação da associação, classes de ouvinte devem implementar essa interface. Isso pode ser feito pela implementação da interface de ouvinte em uma subclasse de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ou qualquer outro objeto de Java, como uma atividade Android de agrupada.


### <a name="runnables"></a>Executáveis

Java utiliza o [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interface para fornecer um mecanismo de delegação. O [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) classe é um consumidor importante desta interface. Android tenha utilizado a interface na API também.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) e [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) são exemplos importantes.

O `Runnable` interface contém um único método void [Run ()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Ele, portanto, se presta a associação em c# como um [Action](http://msdn.microsoft.com/en-us/library/system.action.aspx) delegate. Fornecemos sobrecargas na associação que aceitam um `Action` parâmetro para todos os membros da API que consumir um `Runnable` na API nativa, por exemplo, [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) e [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Deixamos o [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) sobrecargas no local em vez de substituí-los como vários tipos de implementam a interface e, portanto, podem ser passado como executáveis diretamente.


### <a name="inner-classes"></a>Classes internas

Java tem dois tipos diferentes de [classes aninhadas](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): estático aninhado classes e não-estático.

Classes aninhadas estáticos do Java são idênticos aos tipos aninhados em c#.

Não-estático aninhado classes, também chamados de *classes internas*, são significativamente diferentes. Eles contêm uma referência implícita a uma instância do tipo delimitador e não podem conter membros estáticos (entre outras diferenças fora do escopo desta visão geral).

Quando se trata de associação e c# usam, estáticos classes aninhadas são tratados como normais tipos aninhados. Classes internas, enquanto isso, tem duas diferenças significativas:

1. A referência implícita ao tipo de conteúdo deve ser fornecida explicitamente como um parâmetro de construtor.

1. Ao herdar de uma classe interna, a classe interna *deve* ser aninhadas dentro de um tipo que herda do tipo recipiente da classe base interna e o tipo derivado deve fornecer um construtor do mesmo tipo como o c# que contém o tipo.


Por exemplo, considere o [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) classe interna. Como é uma classe interna, o [WallpaperService.Engine() construtor](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) recebe uma referência a um [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) instância (comparar e contrastar para Java [WallpaperService.Engine ( construtor),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) que não usa nenhum parâmetro).

A derivação de um exemplo de uma classe interna é CubeWallpaper.CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

Observe como `CubeWallpaper.CubeEngine` é aninhada dentro de `CubeWallpaper`, `CubeWallpaper` herda da classe recipiente de `WallpaperService.Engine`, e `CubeWallpaper.CubeEngine` tem um construtor que aceite o tipo de declaração `CubeWallpaper` nesse caso - all como especificado acima.


### <a name="interfaces"></a>Interfaces

Interfaces de Java podem conter três conjuntos de membros, dois dos quais causam problemas do c#:

1. Métodos

1. Tipos

1. Campos


Interfaces de Java são convertidas em dois tipos:

1. Uma interface (opcional) que contém as declarações de método. Essa interface possui o mesmo nome como a interface de Java, *exceto* também possui um ' *I* ' prefixo.

1. Uma classe estática (opcional) que contém todos os campos declarados dentro da interface de Java.

Tipos aninhados são "realocados" para ser irmãos da interface em vez de tipos aninhados, com o nome da interface delimitador como um prefixo de delimitador.

Por exemplo, considere o [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interface.
O *Parcelable* interface contém métodos, tipos aninhados e constantes. O *Parcelable* métodos de interface são colocados no [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interface.
O *Parcelable* constantes de interface são colocados no [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) tipo. Aninhada [android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) e [android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html) tipos não estão no momento associado devido a limitações no nosso suporte genéricos; Se eles foram suportados, elas estarão presentes como o *Android.OS.IParcelableClassLoaderCreator* e *Android.OS.IParcelableCreator* interfaces. Por exemplo, aninhada [android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interface está vinculada como o [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interface.


> [!NOTE]
> Começando com o xamarin 1.9, constantes de interface do Java são <em>duplicado</em> em um esforço para simplificar a portabilidade Java de código. Isso ajuda a aumentar a portabilidade código Java que se baseia em [provedor android](http://developer.android.com/reference/android/provider/package-summary.html) interface constantes.

Além dos tipos anteriores, há quatro outras alterações:

1. Um tipo com o mesmo nome que a interface de Java é gerado para conter constantes.

1. Tipos que contêm constantes interface também contêm todas as constantes que vêm de interfaces implementadas do Java.

1. Todas as classes que implementam uma interface de Java que contêm constantes obtém um novo tipo InterfaceConsts aninhado que contém as constantes de todas as interfaces implementadas.

1. O *constantes da* tipo agora está obsoleto.


Para o *android.os.Parcelable* interface, isso significa que agora será um [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) tipo para conter as constantes. Por exemplo, o [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) constante será associada como o [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) constante, em vez de como o  *ParcelableConsts.ContentsFileDescriptor* constante.

Para interfaces que contém constantes que implementam interfaces que contém ainda mais constantes, a união de todas as constantes é gerada. Por exemplo, o [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interface implementa o [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interface. No entanto, antes de 1.9, o [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) tipo dispõe de nenhuma forma de acessar as constantes declaradas em [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Como resultado, a expressão de Java *MediaStore.Video.VideoColumns.TITLE* precisa ser associado à expressão c# *MediaStore.Video.MediaColumnsConsts.Title* que é difícil descobrir sem leitura muita documentação do Java. Em 1.9, a expressão c# equivalente será [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Além disso, considere o [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) tipo, que implementa o Java *Parcelable* interface. Desde que ele implementa a interface, todas as constantes na interface são acessíveis "por" do tipo de pacote, por exemplo, *Bundle.CONTENTS_FILE_DESCRIPTOR* é uma expressão de Java perfeitamente válida.
Anteriormente, a porta essa expressão para c# você precisa examinar todas as interfaces que são implementadas para ver de qual tipo de *CONTENTS_FILE_DESCRIPTOR* veio. A partir do xamarin 1.9, classes que implementam as interfaces de Java que contêm constantes terá um aninhada *InterfaceConsts* tipo, que contém todas as constantes de interface herdada. Isso permitirá a conversão *Bundle.CONTENTS_FILE_DESCRIPTOR* para [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Tipos Finalmente, com um *constantes da* como sufixo *Android.OS.ParcelableConsts* está agora obsoleto, que não seja o InterfaceConsts recentemente introduzido tipos aninhados. Eles serão removidos do xamarin 3.0.


## <a name="resources"></a>Recursos

Imagens, descrições de layout, blobs binários e dicionários de cadeia de caracteres podem ser incluídos em seu aplicativo como [arquivos de recurso](http://developer.android.com/guide/topics/resources/providing-resources.html).
Várias APIs Android destinam-se a [funcionarão no recurso IDs](http://developer.android.com/guide/topics/resources/accessing-resources.html) em vez de lidar com imagens, cadeias de caracteres ou binário blobs diretamente.

Por exemplo, um aplicativo Android de exemplo que contém um layout de interface do usuário ( `main.axml`), uma cadeia de caracteres de tabela de internacionalização ( `strings.xml`) e alguns ícones ( `drawable-*/icon.png`) seria manter seus recursos no diretório "Recursos" do aplicativo:

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

Nativo Android APIs não funcionam diretamente com nomes de arquivo, mas em vez disso, opera em IDs de recurso. Quando você compila um aplicativo do Android que utiliza recursos, o sistema de compilação os recursos de distribuição do pacote e gerar uma classe chamada `Resource` que contém os tokens para cada um dos recursos incluídos. Por exemplo, para o layout de recursos acima, este é o que poderia expor a classe R:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

Em seguida, você usaria `Resource.Drawable.icon` a referência a `drawable/icon.png` arquivo, ou `Resource.Layout.main` a referência a `layout/main.xml` arquivo, ou `Resource.String.first_string` para fazer referência a primeira cadeia de caracteres no arquivo de dicionário `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Constantes e enumerações

Nativo APIs Android tem vários métodos que aceitam ou retornam um int que deve ser mapeado para um campo constante para determinar o que significa que o int. Para usar esses métodos, o usuário é necessário consultar a documentação para ver quais constantes são os valores apropriados, que é inferior ao ideal.

Por exemplo, considere [Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

Nesses casos, procuramos agrupar constantes relacionadas em uma enumeração do .NET e remapear o método para fazer a enumeração em vez disso.
Ao fazer isso, é possível oferecer IntelliSense seleção de valores possíveis.

O exemplo acima se torna: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)).

Observe que esse é um processo muito manual para descobrir quais constantes pertencem juntos e quais APIs consumam constantes. Envie bugs para qualquer uso de constantes na API do que seria melhor expressa como uma enumeração.
