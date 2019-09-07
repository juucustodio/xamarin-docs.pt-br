---
title: Princípios de design da API do Xamarin. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 0b3d8fc4836f6f6d1f6bf30b555e3c5c285678f0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756860"
---
# <a name="xamarinandroid-api-design-principles"></a>Princípios de design da API do Xamarin. Android

Além das principais bibliotecas de classes base que fazem parte do mono, o Xamarin. Android é fornecido com associações para várias APIs do Android para permitir que os desenvolvedores criem aplicativos Android nativos com o mono.

No núcleo do Xamarin. Android há um mecanismo de interoperabilidade que une C# o mundo com o mundo Java e fornece aos desenvolvedores acesso às APIs Java de C# ou de outras linguagens .net.

## <a name="design-principles"></a>Princípios de design

Estes são alguns dos princípios de design para a associação do Xamarin. Android

- Obedecer às [diretrizes de design de .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

- Permitir que os desenvolvedores criem classes Java de subclasse.

- A subclasse deve funcionar com C# construções padrão.

- Derive de uma classe existente.

- Chame o construtor base para a cadeia.

- Os métodos de substituição devem ser C#feitos com o sistema de substituição.

- Torne as tarefas Java comuns fáceis e as tarefas de Java difíceis possíveis.

- Expor propriedades de JavaBean C# como propriedades.

- Expor uma API fortemente tipada:

  - Aumente a segurança do tipo.

  - Minimize os erros de tempo de execução.

  - Obter o IntelliSense do IDE em tipos de retorno.

  - Permite documentação de popup do IDE.

- Incentive a exploração no IDE das APIs:

  - Utilize alternativas de estrutura para minimizar a exposição do Java classlib.

  - Exponha C# delegados (lambdas, métodos anônimos e System. Delegate) em vez de interfaces de método único quando apropriado e aplicável.

  - Forneça um mecanismo para chamar bibliotecas Java arbitrárias ( [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)).

## <a name="assemblies"></a>Assemblies

O Xamarin. Android inclui vários assemblies que constituem o *perfil monomobile*. A página [assemblies](~/cross-platform/internals/available-assemblies.md) tem mais informações.

As associações à plataforma Android estão contidas no `Mono.Android.dll` assembly. Esse assembly contém toda a associação para consumir APIs do Android e se comunicar com a VM de tempo de execução do Android.

## <a name="binding-design"></a>Design de associação

### <a name="collections"></a>Coleções

As APIs do Android utilizam extensivamente as coleções de Java. util para fornecer listas, conjuntos e mapas. Nós expõemos esses elementos usando as interfaces [System. Collections. Generic](xref:System.Collections.Generic) em nossa associação. Os mapeamentos fundamentais são:

- [Java. util. Set\<E >](https://developer.android.com/reference/java/util/Set.html) mapeia para o tipo de sistema [ICollection\<t >](xref:System.Collections.Generic.ICollection`1), classe [\<auxiliar Android. Runtime. javaset T >](xref:Android.Runtime.JavaSet`1).

- [Java. util. List\<E >](https://developer.android.com/reference/java/util/List.html) mapeia para tipo de [sistema\<IList t >](xref:System.Collections.Generic.IList`1), classe [\<auxiliar Android. Runtime. javalist t >](xref:Android.Runtime.JavaList`1).

- [Java. util. Map < K, V >](https://developer.android.com/reference/java/util/Map.html) mapeia para o tipo de sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), classe auxiliar [Android. Runtime. JavaDictionary < K, V >](xref:Android.Runtime.JavaDictionary`2).

- [Java. util. Collection\<E >](https://developer.android.com/reference/java/util/Collection.html) mapeia para o tipo de sistema [ICollection\<t >](xref:System.Collections.Generic.ICollection`1), classe [\<auxiliar Android. Runtime. javacollection T >](xref:Android.Runtime.JavaCollection`1).

Fornecemos classes auxiliares para facilitar o marshaling mais rápido desses tipos. Quando possível, é recomendável usar essas coleções fornecidas em vez da implementação fornecida pela estrutura [`List<T>`](xref:System.Collections.Generic.List`1) , [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2)como ou. As implementações do [Android. Runtime](xref:Android.Runtime) utilizam uma coleção Java nativa internamente e, portanto, não exigem a cópia de e para uma coleção nativa ao passar para um membro da API do Android.

Você pode passar qualquer implementação de interface para um método Android aceitando essa interface, por exemplo `List<int>` , passar um para o construtor [ArrayAdapter&lt;&gt;int (Context&lt;,&gt;int, IList int)](xref:Android.Widget.ArrayAdapter`1) . *No entanto*, para todas as implementações, *exceto* para as implementações do Android. Runtime, isso envolve *copiar* a lista da VM mono para a VM de tempo de execução do Android. Se a lista for alterada posteriormente no tempo de execução do Android (por exemplo, invocando [&lt;ArrayAdapter&gt;T. Add (T)](xref:Android.Widget.ArrayAdapter`1.Add*) Method), essas alterações *não serão* visíveis no código gerenciado. Se um `JavaList<int>` tiver sido usado, essas alterações ficarão visíveis.

As implementações de interface de coleções reformuladas que *não* são uma das **classes auxiliares**listadas acima apenas empacotam [in]:

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

Os métodos Java são transformados em Propriedades, quando apropriado:

- O par `T getFoo()` de métodos Java `void setFoo(T)` e é transformado na `Foo` propriedade. Exemplo: [Activity. intuito](xref:Android.App.Activity.Intent).

- O método `getFoo()` Java é transformado na propriedade foo somente leitura. Exemplo: [Context.PackageName](xref:Android.Content.Context.PackageName).

- As propriedades somente set não são geradas.

- As propriedades *não* serão geradas se o tipo de propriedade for uma matriz.

### <a name="events-and-listeners"></a>Eventos e ouvintes

As APIs do Android são criadas com base no Java e seus componentes seguem o padrão Java para conectar ouvintes de evento. Esse padrão tende a ser complicado, pois requer que o usuário crie uma classe anônima e declare os métodos a serem substituídos, por exemplo, isso é como as coisas seriam feitas no Android com Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

O código equivalente no C# uso de eventos seria:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Observe que os dois mecanismos acima estão disponíveis com o Xamarin. Android. Você pode implementar uma interface de ouvinte e anexá-la a View. SetOnClickListener, ou você pode anexar um delegado criado por meio C# de qualquer um dos paradigmas usuais ao evento de clique.

Quando o método de retorno de chamada do ouvinte tem um retorno nulo, criamos elementos de API com base em um delegado [TEventArgs&lt;&gt; EventHandler](xref:System.EventHandler`1) . Geramos um evento como o exemplo acima para esses tipos de ouvinte. No entanto, se o retorno de chamada do ouvinte retornar um valor não nulo e não **booliano** , os eventos e os EventHandlers não serão usados. Em vez disso, geramos um delegado específico para a assinatura do retorno de chamada e adicionamos Propriedades em vez de eventos. O motivo é lidar com a ordem de invocação de delegado e o tratamento de retorno. Essa abordagem espelha o que é feito com a API do Xamarin. iOS.

C#eventos ou propriedades são gerados automaticamente se o método de registro de evento do Android:

1. Tem um `set` prefixo, por exemplo, [ *set*OnClickListener](xref:Android.Views.View.SetOnClickListener*).

1. Tem um `void` tipo de retorno.

1. Aceita apenas um parâmetro, o tipo de parâmetro é uma interface, a interface tem apenas um método e o nome da interface termina `Listener` em, por exemplo, o [ *ouvinte*de View. OnClick](xref:Android.Views.View.IOnClickListener).

Além disso, se o método de interface do ouvinte tiver um tipo de retorno **booliano** , em vez de **void**, a subclasse *EventArgs* gerada conterá uma propriedade *manipulada* . O valor da propriedade *Handled* é usado como o valor de retorno para o método de *ouvinte* e o `true`padrão é.

Por exemplo, o método de exibição do Android [. setOnKeyListener ()](xref:Android.Views.View.SetOnKeyListener*) aceita a interface [View. OnKeyListener](xref:Android.Views.View.IOnKeyListener) e o método [View. OnKeyListener. onKey (View, int, KeyEvent)](xref:Android.Views.View.IOnKeyListener.OnKey*) tem um tipo de retorno booleano. O Xamarin. Android gera um evento [View. KeyPress](xref:Android.Views.View.KeyPress) correspondente, que é um [modo de exibição EventHandler&lt;. KeyEventArgs.&gt;](xref:Android.Views.View.KeyEventArgs)
A classe *KeyEventArgs* , por sua vez, tem uma propriedade [View. KeyEventArgs. Handled](xref:Android.Views.View.KeyEventArgs.Handled) , que é usada como o valor de retorno para o método *View. OnKeyListener. onKey ()* .

Pretendemos adicionar sobrecargas para outros métodos e construtores para expor a conexão baseada em delegado. Além disso, os ouvintes com vários retornos de chamada exigem uma inspeção adicional para determinar se a implementação de retornos de chamada individuais é razoável, então estamos convertendo esses como eles são identificados. Se não houver nenhum evento correspondente, os ouvintes deverão ser usados C#no, mas você poderá ter um delegado de uso para nossa atenção. Também fizemos algumas conversões de interfaces sem o sufixo "Listener" quando estava claro que elas se beneficiarão de uma alternativa de delegado.

Todas as interfaces de ouvintes implementam o[`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
interface, devido aos detalhes de implementação da associação, portanto, as classes de ouvinte devem implementar essa interface. Isso pode ser feito com a implementação da interface do ouvinte em uma subclasse de [Java. lang. Object](xref:Java.Lang.Object) ou qualquer outro objeto Java encapsulado, como uma atividade do Android.

### <a name="runnables"></a>Executáveis

O Java utiliza a interface [Java. lang. executável](xref:Java.Lang.Runnable) para fornecer um mecanismo de delegação. A classe [Java. lang. thread](xref:Java.Lang.Thread) é um consumidor notável desta interface. O Android também emprega a interface na API.
[Activity. runOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) e [View.post ()](xref:Android.Views.View.Post*) são exemplos notáveis.

A `Runnable` interface contém um único método void, [Execute ()](xref:Java.Lang.Runnable.Run). Portanto, ele se presta à associação C# como um delegado [System. Action](xref:System.Action) . Fornecemos sobrecargas na associação `Action` que aceitam um parâmetro para todos os membros da API que consomem um `Runnable` na API nativa, por exemplo, [Activity. RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) e [View.post ()](xref:Android.Views.View.Post*).

Deixamos as sobrecargas [IRunnable](xref:Java.Lang.IRunnable) em vigor em vez de substituí-las, pois vários tipos implementam a interface e, portanto, podem ser passados como executáveis diretamente.

### <a name="inner-classes"></a>Classes internas

O Java tem dois tipos diferentes de [classes aninhadas](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): classes aninhadas estáticas e classes não estáticas.

As classes aninhadas do Java static C# são idênticas aos tipos aninhados.

Classes aninhadas não estáticas, também chamadas de *classes internas*, são significativamente diferentes. Eles contêm uma referência implícita a uma instância de seu tipo delimitador e não podem conter membros estáticos (entre outras diferenças fora do escopo desta visão geral).

Quando se trata de Binding e C# use, as classes aninhadas estáticas são tratadas como tipos aninhados normais. As classes internas, enquanto isso, têm duas diferenças significativas:

1. A referência implícita para o tipo recipiente deve ser fornecida explicitamente como um parâmetro de construtor.

1. Ao herdar de uma classe interna, a classe interna *deve* ser aninhada dentro de um tipo que herda do tipo recipiente da classe base interna e o tipo derivado deve fornecer um construtor do mesmo tipo que o C# tipo recipiente.

Por exemplo, considere a classe interna [Android. Service. wallpaper. WallpaperService. Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine) . Como é uma classe interna, o [Construtor WallpaperService. Engine ()](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) usa uma referência a uma instância [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) (compare e contraste com o Construtor Java WallpaperService. Engine (), que não usa parâmetros).

Uma derivação de exemplo de uma classe interna é CubeWallpaper. CubeEngine:

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

Observe como `CubeWallpaper.CubeEngine` o é aninhado `CubeWallpaper` em `CubeWallpaper`, herdado da classe `WallpaperService.Engine`recipiente de `CubeWallpaper.CubeEngine` `CubeWallpaper` e tem um construtor que usa o tipo declarativo – nesse caso, todos conforme especificado acima.

### <a name="interfaces"></a>Interfaces

As interfaces Java podem conter três conjuntos de membros, dois dos quais causam problemas C#de:

1. Métodos

1. Tipos

1. Campos

As interfaces Java são convertidas em dois tipos:

1. Uma interface (opcional) que contém declarações de método. Essa interface possui o mesmo nome como a interface de Java, *exceto* também possui um ' *I* ' prefixo.

1. Uma classe estática (opcional) que contém todos os campos declarados na interface java.

Os tipos aninhados são "realocados" para serem irmãos da interface delimitadora em vez de tipos aninhados, com o nome da interface de circunscrição como um prefixo.

Por exemplo, considere a interface [Android. os.](xref:Android.OS.Parcelable) consideble.
A interface de *diremessa* contém métodos, tipos aninhados e constantes. Os métodos de interface de colocação são *colocados na interface* [Android. os. IParcelable](xref:Android.OS.IParcelable) .
As constantes *de interface que* podem ser armazenadas são colocadas no tipo [Android. os. ParcelableConsts](xref:Android.OS.ParcelableConsts) . Os tipos aninhados [Android. os\<. ClassLoaderCreator t >](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) e [Android. os. Creator\<t >](https://developer.android.com/reference/android/os/Parcelable.Creator.html) não estão associados no momento devido a limitações em nosso suporte a genéricos; se eles tiverem suporte, eles estaria presente como as interfaces *Android. os. IParcelableClassLoaderCreator* e *Android. os. IParcelableCreator* . Por exemplo, a interface do [Android. os. IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) aninhada é associada como a interface [Android. os. IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) .

> [!NOTE]
> A partir do Xamarin. Android 1,9, as constantes da interface java são _duplicadas_ em um esforço para simplificar a portagem do código Java. Isso ajuda a melhorar o portamento do código Java que depende das constantes da interface do [provedor do Android](https://developer.android.com/reference/android/provider/package-summary.html) .

Além dos tipos acima, há quatro outras alterações:

1. Um tipo com o mesmo nome da interface java é gerado para conter constantes.

1. Tipos que contêm constantes de interface também contêm todas as constantes que vêm de interfaces Java implementadas.

1. Todas as classes que implementam uma interface java contendo constantes obtêm um novo tipo de InterfaceConsts aninhado que contém constantes de todas as interfaces implementadas.

1. O tipo *CONSTS* agora é obsoleto.

Para a interface *Android. os.* refinable, isso significa que agora haverá um tipo [*Android. os.* ](xref:Android.OS.Parcelable) refinable para conter as constantes. Por exemplo, a constante de amortizable [. CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) será associada como a constante de amortizable [ *. ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor) , em vez de como a constante *ParcelableConsts. ContentsFileDescriptor* .

Para interfaces que contêm constantes que implementam outras interfaces que contêm ainda mais constantes, a União de todas as constantes agora é gerada. Por exemplo, a interface [Android. Provider. MediaStore. video. VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) implementa a interface [Android. Provider. MediaStore. MediaColumns](xref:Android.Provider.MediaStore.MediaColumns) . No entanto, antes de 1,9, o tipo [Android. Provider. MediaStore. video. VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) não tem como acessar as constantes declaradas em [Android. Provider. MediaStore. MediaColumnsConsts](xref:Android.Provider.MediaStore.MediaColumnsConsts).
Como resultado, a expressão Java *MediaStore. video. VideoColumns. title* precisa ser associada à C# expressão *MediaStore. video. MediaColumnsConsts. title* , que é difícil de descobrir sem ler muita documentação do Java. Em 1,9, a expressão C# equivalente será [MediaStore. vídeo. VideoColumns. title](xref:Android.Provider.MediaStore.Video.VideoColumns.Title).

Além disso, considere o tipo [Android. os. Bundle](xref:Android.OS.Bundle) , que implementa a *interface de* conconjunto de Java. Como ele implementa a interface, todas as constantes nessa interface podem ser acessadas "por meio de" o tipo de pacote, por exemplo, *Bundle. CONTENTS_FILE_DESCRIPTOR* é uma expressão Java perfeitamente válida.
Anteriormente, para portar essa expressão C# para você precisaria examinar todas as interfaces implementadas para ver de qual tipo o *CONTENTS_FILE_DESCRIPTOR* veio. A partir do Xamarin. Android 1,9, as classes que implementam interfaces Java que contêm constantes terão um tipo *InterfaceConsts* aninhado, que conterá todas as constantes de interface herdadas. Isso permitirá a tradução de *Bundle. CONTENTS_FILE_DESCRIPTOR* para [*Bundle. InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor).

Por fim, os tipos com um sufixo *CONSTS* , como *Android. os. ParcelableConsts* , agora são obsoletos, além dos tipos aninhados InterfaceConsts recém introduzidos. Eles serão removidos no Xamarin. Android 3,0.

## <a name="resources"></a>Recursos

Imagens, descrições de layout, blobs binários e dicionários de cadeia de caracteres podem ser incluídos em seu aplicativo como [arquivos de recurso](https://developer.android.com/guide/topics/resources/providing-resources.html).
Várias APIs do Android são projetadas para [operar nas IDs de recurso](https://developer.android.com/guide/topics/resources/accessing-resources.html) em vez de lidar com imagens, cadeias de caracteres ou blobs binários diretamente.

Por exemplo, um aplicativo Android de exemplo que contém um layout de interface `main.axml`do usuário (), uma cadeia de `strings.xml`caracteres da tabela de internacionalização () e alguns ícones ( `drawable-*/icon.png`) manteria seus recursos no diretório "recursos" do aplicativo:

```
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
```

As APIs nativas do Android não operam diretamente com nomes de File, mas operam em IDs de recurso. Quando você compila um aplicativo Android que usa recursos, o sistema de compilação empacotará os recursos para distribuição e gerará uma `Resource` classe chamada que contém os tokens para cada um dos recursos incluídos. Por exemplo, para o layout de recursos acima, isso é o que a classe R exporia:

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

Em seguida, você `Resource.Drawable.icon` usaria para `drawable/icon.png` fazer referência ao `Resource.Layout.main` arquivo ou fazer `layout/main.xml` referência ao arquivo `Resource.String.first_string` ou fazer referência à primeira cadeia de caracteres no `values/strings.xml`arquivo de dicionário.

## <a name="constants-and-enumerations"></a>Constantes e enumerações

As APIs nativas do Android têm muitos métodos que assumem ou retornam um int que deve ser mapeado para um campo constante para determinar o que significa o int. Para usar esses métodos, o usuário precisa consultar a documentação para ver quais constantes são valores apropriados, o que é menor que o ideal.

Por exemplo, considere a [atividade. requestWindowFeature (int FeatureId)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

Nesses casos, fazemos a tarefa de agrupar constantes relacionadas em uma enumeração .NET e remapear o método para pegar a enumeração.
Ao fazer isso, podemos oferecer a seleção de IntelliSense dos valores potenciais.

O exemplo acima se torna: [Activity. RequestWindowFeature (WindowFeatures FeatureId)](xref:Android.App.Activity.RequestWindowFeature*).

Observe que esse é um processo muito manual para descobrir quais constantes pertencem em conjunto e quais APIs consomem essas constantes. Registre bugs para qualquer constante usada na API que seria melhor expressa como uma enumeração.
