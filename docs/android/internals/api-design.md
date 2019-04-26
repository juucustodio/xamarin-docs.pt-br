---
title: Princípios de Design de API do xamarin. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: e762a286069d5ef1db90f3c45808eee0a7a04a7f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954279"
---
# <a name="xamarinandroid-api-design-principles"></a>Princípios de Design de API do xamarin. Android


## <a name="overview"></a>Visão geral

Além das principais bibliotecas de classes Base que fazem parte do Mono, xamarin. Android é fornecido com associações para várias APIs do Android permitir aos desenvolvedores criar aplicativos nativos Android com o Mono.

A essência do xamarin. Android existe é um mecanismo de interoperabilidade nesse mundo pontes de c# com o mundo Java e fornece aos desenvolvedores acesso às APIs do Java do c# ou outras linguagens .NET.


## <a name="design-principles"></a>Princípios de design

Estes são alguns dos nossos princípios de design para a associação do xamarin. Android

-  Estar em conformidade com o [diretrizes de Design do .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Permitir que os desenvolvedores classes Java de subclasse.

-  Subclasse deve trabalhar com construções de linguagem c# padrão.

-  Derive de uma classe existente.

-  Chame o construtor de base para a cadeia.

-  Substituindo métodos deve ser feito com C#de substituir o sistema.

-  Possibilitam tarefas comuns de Java fácil e difíceis de tarefas de Java.

-  Expor propriedades JavaBean como propriedades do c#.

-  Expor uma API fortemente tipada:

    - Aumente a segurança de tipos.

    - Minimize os erros de tempo de execução.

    - Obter o intellisense do IDE em tipos de retorno.

    - Permite a documentação de pop-up do IDE.

-  Incentive a explorar as APIs no IDE:

    - Utilize alternativas de estrutura para minimizar Java Classlib exposição.

    - Expor delegados c# (lambdas, métodos anônimos e System. Delegate) em vez de interfaces de método único quando apropriado e aplicável.

    - Fornecem um mecanismo para chamar as bibliotecas de Java arbitrárias ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Assemblies

Xamarin. Android inclui um número de assemblies que constituem o *MonoMobile perfil*. O [Assemblies](~/cross-platform/internals/available-assemblies.md) página tem mais informações.

As associações para a plataforma Android estão contidas no `Mono.Android.dll` assembly. Esse assembly contém a associação inteira para consumo de APIs de Android e se comunicando com o tempo de execução do Android VM.


## <a name="binding-design"></a>Design de associação


### <a name="collections"></a>Coleções

As APIs do Android utilizar as coleções Util extensivamente para fornecer listas, conjuntos e mapas. Podemos expor esses elementos usando o [Collections](xref:System.Collections.Generic) interfaces em nossa ligação. Os mapeamentos fundamentais são:

-   [java.util.Set<E> ](https://developer.android.com/reference/java/util/Set.html) mapeia para o tipo de sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), classe auxiliar [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](https://developer.android.com/reference/java/util/List.html) mapeia para o tipo de sistema [IList<T>](xref:System.Collections.Generic.IList`1), classe auxiliar [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](https://developer.android.com/reference/java/util/Map.html) mapeia para o tipo de sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), classe auxiliar [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](https://developer.android.com/reference/java/util/Collection.html) mapeia para o tipo de sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), classe auxiliar [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Nós fornecemos classes auxiliares para facilitar mais rápido copyless marshaling desses tipos. Quando possível, é recomendável usar esses fornecidos coleções em vez da implementação da estrutura fornecida, como [ `List<T>` ](xref:System.Collections.Generic.List`1) ou [ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2). O [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implementações utilizar uma coleção nativa do Java internamente e, portanto, não exigem copiar de e para uma coleção nativa ao passar para um membro de API do Android.

Você pode passar qualquer implementação de interface para um método Android aceitar essa interface, por exemplo, passar uma `List<int>` para o [ArrayAdapter&lt;int&gt;(contexto, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)construtor. *No entanto*, para todas as implementações *exceto* para as implementações de Android.Runtime, isso envolve *copiando* a lista da VM Mono no tempo de execução Android VM. Se a lista for posterior alterada dentro do tempo de execução do Android (por exemplo, invocando o [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) método), essas alterações *não* estar visível em código gerenciado. Se um `JavaList<int>` foram usadas, essas alterações seria visíveis.

Refeita, coleções de interface implementações que são *não* uma das opções acima listadas **classe auxiliar**es apenas empacotar [In]:

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

Métodos Java são transformados em propriedades, quando apropriado:

-  O par de métodos Java `T getFoo()` e `void setFoo(T)` são transformados no `Foo` propriedade. Exemplo: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  O método Java `getFoo()` é transformado em que a propriedade de Foo somente leitura. Exemplo: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Propriedades de conjunto não são geradas.

-  As propriedades são *não* gerado se o tipo de propriedade seria uma matriz.



### <a name="events-and-listeners"></a>Eventos e ouvintes

As APIs do Android são criadas com base no Java e seus componentes seguem o padrão de Java para conectar ouvintes de eventos. Este padrão tende a ser inconveniente, pois exige que o usuário criar uma classe anônima e declarar os métodos para substituir, por exemplo, isso é como as coisas seriam realizadas no Android com o Java:

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

Observe que ambos os mecanismos acima estão disponíveis com o xamarin. Android. Você pode implementar uma interface de ouvinte e anexá-lo com View.SetOnClickListener, ou você pode anexar um delegado que foi criado por meio de qualquer um dos paradigmas c# normais para o evento de clique.

Quando o método de retorno de chamada de ouvinte tem um retorno nulo, podemos criar elementos de API com base em um [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) delegar. Podemos gerar um evento como o exemplo acima para esses tipos de ouvinte. No entanto, se o retorno de chamada de ouvinte retorna um não nulo e não- **boolean** valor, eventos e EventHandlers não são usados. Em vez disso, podemos gerar um delegado específico para a assinatura do retorno de chamada e adicionar propriedades ao invés de eventos. O motivo é lidar com ordem de invocação de delegado e retornar a manipulação. Essa abordagem espelha o que é feito com a API do xamarin. IOS.

Eventos de C# ou propriedades são geradas automaticamente apenas se o método de registro de evento Android:

1. Tem um `set` prefixo, por exemplo, [ *definir*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Tem um `void` tipo de retorno.

1. Aceita apenas um parâmetro, o tipo de parâmetro é uma interface, a interface tem apenas um método e o nome da interface termina `Listener` , por exemplo, [View.OnClick *ouvinte*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Além disso, se o ouvinte do método de interface tem um tipo de retorno **boolean** em vez de **void**, em seguida, gerado *EventArgs* subclasse conterá um *Handled* propriedade. O valor da *Handled* propriedade é usada como o valor retornado para o *ouvinte* método e ele assume como padrão `true`.

Por exemplo, o Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) método aceita o [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interface e o [View.OnKeyListener.onKey (modo de exibição, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) método tem um tipo de retorno booleano. Xamarin. Android gera um correspondente [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) evento, que é um [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
O *KeyEventArgs* classe por sua vez tem um [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriedade, que é usada como o valor retornado para o *View.OnKeyListener.onKey()* método.

Pretendemos adicionar sobrecargas para outros métodos e construtores de expor a conexão com base em delegado. Além disso, os ouvintes com vários retornos de chamada exigem algum inspeção adicional para determinar se é razoável, implementar retornos de chamada individuais para que os que estão sendo convertidos esses conforme eles são identificados. Se não houver nenhum evento correspondente, ouvintes devem ser usados no c#, mas traga qualquer um que você acha que poderia ter utilização de delegado para a nossa atenção. Também fizemos algumas conversões de interfaces sem o sufixo "Ouvinte" quando ficou claro que se beneficiariam de uma alternativa de delegado.

Todas as interfaces de ouvintes de implementam o [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
interface, por causa dos detalhes da implementação da associação, portanto, classes de ouvinte devem implementar essa interface. Isso pode ser feito pela implementação da interface de ouvinte em uma subclasse de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ou qualquer outro objeto de Java, como uma atividade do Android de encapsulado.


### <a name="runnables"></a>Executáveis

Java utiliza o [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interface para fornecer um mecanismo de delegação. O [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) classe é um consumidor notável desta interface. Android contratou a interface na API também.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) e [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) são exemplos importantes.

O `Runnable` interface contém um único método void [Run ()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Ele, portanto, se presta a associação em c# como um [System. Action](xref:System.Action) delegar. Fornecemos sobrecargas na associação que aceitam um `Action` parâmetro para todos os membros de API que consumir um `Runnable` na API nativa, por exemplo, [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) e [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Deixamos os [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) sobrecargas em vigor em vez de substituí-los, já que vários tipos implementam a interface e podem, portanto, ser passado como executáveis diretamente.


### <a name="inner-classes"></a>Classes internas

Java tem dois tipos diferentes de [aninhados classes](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): estático aninhado classes e classes não estáticas.

Classes aninhadas estáticas do Java são idênticas aos tipos aninhados em c#.

Não-estático aninhado classes, também chamadas de *classes internas*, são significativamente diferentes. Eles contêm uma referência implícita a uma instância do tipo delimitador e não podem conter membros estáticos (entre outras diferenças fora do escopo desta visão geral).

Quando se trata de associação e o uso do c#, classes aninhadas estáticas são tratadas como tipos aninhados normais. Classes internas, enquanto isso, tem duas diferenças significativas:

1. A referência implícita ao tipo recipiente deve ser fornecida explicitamente como um parâmetro de construtor.

1. Ao herdar de uma classe interna, a classe interna *deve* ser aninhadas dentro de um tipo que herda do tipo recipiente da classe base interna e o tipo derivado deve fornecer um construtor do mesmo tipo como o c# que contém o tipo.


Por exemplo, considere a [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) classe interna. Pois é uma classe interna, o [construtor WallpaperService.Engine()](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) usa uma referência a um [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) instância (compare e contraste para Java [WallpaperService.Engine ( construtor),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) que não usa nenhum parâmetro).

Uma derivação de exemplo de uma classe interna é CubeWallpaper.CubeEngine:

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

Observe como `CubeWallpaper.CubeEngine` é aninhada dentro de `CubeWallpaper`, `CubeWallpaper` herda da classe recipiente de `WallpaperService.Engine`, e `CubeWallpaper.CubeEngine` tem um construtor que usa o tipo de declaração `CubeWallpaper` nesse caso - todos como especificado acima.


### <a name="interfaces"></a>Interfaces

As interfaces de Java podem conter três conjuntos de membros, dois dos quais causam problemas em c#:

1. Métodos

1. Tipos

1. Campos


Interfaces de Java são convertidas em dois tipos:

1. Uma interface (opcional) que contém declarações de método. Essa interface possui o mesmo nome como a interface de Java, *exceto* também possui um ' *I* ' prefixo.

1. Uma classe estática (opcional) que contém todos os campos declarados dentro da interface Java.

Tipos aninhados são "realocados" para ser irmãos da interface do delimitador, em vez de tipos aninhados, com o nome da interface delimitador como um prefixo.

Por exemplo, considere a [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interface.
O *Parcelable* interface contém métodos, tipos aninhados e constantes. O *Parcelable* métodos de interface são colocados na [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interface.
O *Parcelable* constantes de interface são colocadas na [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) tipo. Aninhado [android.os.Parcelable.ClassLoaderCreator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) e [android.os.Parcelable.Creator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.Creator.html) tipos não estão no momento associado devido a limitações no nosso suporte a genéricos; Se eles tivessem suporte, eles estaria presentes como o *Android.OS.IParcelableClassLoaderCreator* e *Android.OS.IParcelableCreator* interfaces. Por exemplo, aninhada [android.os.IBinder.DeathRecpient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interface está vinculado como o [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interface.


> [!NOTE]
> Começando com o xamarin. Android 1.9, constantes de interface do Java são <em>duplicado</em> em um esforço para simplificar a portabilidade de Java de código. Isso ajuda a melhorar a portabilidade código Java que dependa [provedor android](https://developer.android.com/reference/android/provider/package-summary.html) constantes de interface.

Além dos tipos acima, há quatro alterações adicionais:

1. Um tipo com o mesmo nome que a interface Java é gerado para conter constantes.

1. Tipos que contêm constantes interface também contêm todas as constantes que vêm de interfaces implementadas de Java.

1. Todas as classes que implementam uma interface de Java que contêm constantes obtém um novo tipo InterfaceConsts aninhado que contém as constantes de todas as interfaces implementadas.

1. O *Consts* tipo agora está obsoleto.


Para o *android.os.Parcelable* interface, isso significa que agora haverá um [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) tipo para conter as constantes. Por exemplo, o [Parcelable.CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) constante será associada como o [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) constante, em vez de como o  *ParcelableConsts.ContentsFileDescriptor* constante.

Para interfaces que contém as constantes que implementam outras interfaces que contém ainda mais constantes, a união de todas as constantes é gerada. Por exemplo, o [android.provider.MediaStore.Video.VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interface implementa o [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interface. No entanto, anteriores à 1.9, o [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) tipo não tem nenhuma maneira de acessar as constantes declaradas na [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Como resultado, a expressão de Java *MediaStore.Video.VideoColumns.TITLE* precisa ser associado à expressão c# *MediaStore.Video.MediaColumnsConsts.Title* que seria difícil de descobrir sem leitura muita documentação do Java. Na versão 1.9, a expressão c# equivalente estará [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Além disso, considere a [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) tipo, que implementa o Java *Parcelable* interface. Como ele implementa a interface, todas as constantes na interface são acessíveis "por meio de" o tipo de pacote, por exemplo, *Bundle.CONTENTS_FILE_DESCRIPTOR* é uma expressão de Java perfeitamente válida.
Anteriormente, para essa expressão para a porta C# você precisaria examinar todas as interfaces que são implementadas para ver de qual tipo de *CONTENTS_FILE_DESCRIPTOR* veio. A partir do xamarin. Android 1.9, classes que implementam as interfaces de Java que contêm constantes terá um aninhados *InterfaceConsts* tipo, que irá conter todas as constantes de interface herdada. Isso permitirá que a tradução *Bundle.CONTENTS_FILE_DESCRIPTOR* à [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Por fim, tipos com uma *Consts* sufixo como *Android.OS.ParcelableConsts* são agora obsoleto, senão o recém-lançado InterfaceConsts tipos aninhados. Eles serão removidos no xamarin. Android 3.0.


## <a name="resources"></a>Recursos

Imagens, descrições de layout, blobs binários e dicionários de cadeia de caracteres podem ser incluídos em seu aplicativo como [arquivos de recurso](https://developer.android.com/guide/topics/resources/providing-resources.html).
Várias APIs do Android são projetadas para [operam nas IDs de recurso](https://developer.android.com/guide/topics/resources/accessing-resources.html) em vez de lidar com imagens, cadeias de caracteres ou binário blobs diretamente.

Por exemplo, um aplicativo Android de exemplo que contém um layout de interface do usuário ( `main.axml`), uma cadeia de caracteres de tabela de internacionalização ( `strings.xml`) e alguns ícones ( `drawable-*/icon.png`) mantém seus recursos no diretório "Recursos" do aplicativo:

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

Nativo APIs do Android funciona diretamente com nomes de arquivo, mas em vez disso, opera em IDs de recurso. Quando você compila um aplicativo do Android que usa os recursos, o sistema de compilação empacotar os recursos para distribuição e gera uma classe chamada `Resource` que contém os tokens para cada um dos recursos incluídos. Por exemplo, para o layout de recursos acima, isso é o que poderia expor a classe de R:

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

Nativo APIs do Android tem muitos métodos que aceitam ou retornam um int que deve ser mapeado para um campo constante para determinar o que significa que o int. Para usar esses métodos, o usuário é necessário consultar a documentação para ver quais constantes são os valores apropriados, que é inferior ao ideal.

Por exemplo, considere [Activity.requestWindowFeature (int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

Nesses casos, podemos se esforçar para agrupar constantes relacionadas em uma enumeração do .NET e remapear o método para tirar a enumeração em vez disso.
Ao fazer isso, somos capazes de oferecer a seleção do IntelliSense de valores possíveis.

O exemplo acima se torna: [Activity.RequestWindowFeature(WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/).

Observe que esse é um processo manual muito para descobrir quais constantes pertencem juntas, e quais APIs consumam essas constantes. Envie bugs para qualquer constantes usadas na API do que seria melhor expressada como uma enumeração.
