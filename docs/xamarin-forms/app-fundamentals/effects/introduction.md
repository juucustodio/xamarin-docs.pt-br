---
Título: "introdução aos efeitos" Descrição: "os efeitos permitem que os controles nativos em cada plataforma sejam personalizados e normalmente são usados para alterações de estilo pequeno. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados, além de descrever a classe PlatformEffect ".
MS. Prod: xamarin MS. AssetID: 30CB8615-8F39-4762-BDB7-333D2B57D112 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 03/08/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-effects"></a>Introdução aos efeitos

_Os efeitos permitem que os controles nativos em cada plataforma sejam personalizados e normalmente são usados para alterações de estilo pequeno. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados, além de descrever a classe PlatformEffect._

Xamarin.Forms[Páginas, layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresenta uma API comum para descrever interfaces de usuário móvel de plataforma cruzada. Cada página, layout e controle são renderizados de forma diferente em cada plataforma usando uma `Renderer` classe que, por sua vez, cria um controle nativo (correspondente à Xamarin.Forms representação), organiza-o na tela e adiciona o comportamento especificado no código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. No entanto, implementar uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente gera uma resposta pesada. Os efeitos simplificam esse processo, permitindo que os controles nativos em cada plataforma sejam personalizado mais facilmente.

Os efeitos são criados em projetos específicos da plataforma por meio da subclasse do `PlatformEffect` controle e, em seguida, os efeitos são consumidos anexando-os a um controle apropriado em uma Xamarin.Forms biblioteca de .net Standard ou em um projeto de biblioteca compartilhada.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Por que usar um efeito em vez de um renderizador personalizado?

Os efeitos simplificam a personalização de um controle, são reutilizáveis e podem ser parametrizados para aumentar ainda mais a reutilização.

Tudo que pode ser feito com um efeito também pode ser feito com um renderizador personalizado. No entanto, os renderizadores personalizados oferecem mais flexibilidade e personalização do que os efeitos. As diretrizes a seguir listam as circunstâncias nas quais você pode escolher um efeito em vez de um renderizador personalizado:

- É recomendado usar um efeito quando alterar as propriedades de um controle específico da plataforma atinge o resultado desejado.
- Um renderizador personalizado é necessário quando há necessidade de substituir os métodos por um controle específico da plataforma.
- Um renderizador personalizado é necessário quando há a necessidade de substituir o controle específico da plataforma que implementa um Xamarin.Forms controle.

## <a name="subclassing-the-platformeffect-class"></a>Criando subclasses da classe PlatformEffect

A tabela a seguir lista o namespace para a classe `PlatformEffect` em cada plataforma, bem como os tipos de suas propriedades:

|Plataforma|Namespace|Contêiner|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms. Platform. iOS|UIView|UIView|
|Android|Xamarin.Forms. Platform. Android|ViewGroup|Visualizar|
|UWP (Plataforma Universal do Windows)|Xamarin.Forms. Platform. UWP|FrameworkElement|FrameworkElement|

Cada classe `PlatformEffect` específica da plataforma expõe as propriedades a seguir:

- `Container` – faz referência ao controle específico da plataforma que está sendo usado para implementar o layout.
- `Control`– faz referência ao controle específico da plataforma que está sendo usado para implementar o Xamarin.Forms controle.
- `Element`– faz referência ao Xamarin.Forms controle que está sendo renderizado.

Os efeitos não têm informações de tipo sobre o contêiner, o controle ou o elemento a que estão anexados porque eles podem ser anexados a qualquer elemento. Portanto, quando um efeito é anexado a um elemento incompatível, ele deve ter o desempenho reduzido discretamente ou lançar uma exceção. No entanto, as propriedades `Container`, `Control` e `Element` podem ser convertidas em seu tipo de implementação. Para obter mais informações sobre esses tipos, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada classe `PlatformEffect` específica da plataforma expõe os métodos a seguir, que devem ser substituídos para implementar um efeito:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached)– chamado quando um efeito é anexado a um Xamarin.Forms controle. Uma versão substituída desse método, em cada classe de efeito específica da plataforma, é o local para executar a personalização do controle, juntamente com a manipulação de exceção, caso o efeito não possa ser aplicado ao Xamarin.Forms controle especificado.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached)– chamado quando um efeito é desanexado de um Xamarin.Forms controle. Uma versão de substituição deste método, em cada classe de efeito específica da plataforma, é o lugar para executar qualquer limpeza de efeito, como cancelar o registro de um manipulador de eventos.

Além disso, o `PlatformEffect` expõe o [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) método, que também pode ser substituído. Esse método é chamado quando uma propriedade do elemento foi alterada. Uma versão substituída desse método, em cada classe de efeito específica da plataforma, é o local para responder às alterações de propriedade vinculáveis no Xamarin.Forms controle. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
